# teste1
Teste Técnico — Desenvolvedoprevenção
# Teste Técnico — Desenvolvedor Full Stack Java & Angular

Olá!

Segue minha entrega referente ao teste técnico para a vaga de Desenvolvedor Full Stack Java & Angular.

O projeto foi desenvolvido simulando um cenário real de sustentação e evolução de sistemas corporativos, com foco em qualidade de código, organização arquitetural, troubleshooting, observabilidade e boas práticas de engenharia.

A solução contempla desenvolvimento ponta a ponta, incluindo front-end, back-end, persistência em banco de dados, logs para diagnóstico, testes automatizados e análise de incidente.

Tecnologias utilizadas:

* Java 17
* Spring Boot
* Angular
* PostgreSQL
* Docker
* JUnit e Mockito
* Swagger/OpenAPI

O projeto possui:

* API REST documentada
* Front-end funcional com validações
* Persistência em banco de dados
* Logs estruturados
* Testes unitários
* Docker Compose para execução simplificada
* Estrutura organizada em camadas
* Tratamento global de exceções
* Análise de incidente com proposta de correção e prevenção

Repositório:

[https://github.com/seuusuario/fullstack-ticket-management](https://github.com/seuusuario/fullstack-ticket-management)

Fico à disposição para apresentar as decisões técnicas adotadas e explicar possíveis melhorias futuras.

Muito obrigado pela oportunidade.
/React

## Visão Geral

Este projeto foi desenvolvido com foco em boas práticas de engenharia de software, arquitetura limpa, observabilidade, testes automatizados e resolução de incidentes em ambiente de produção.

A solução contempla:

* API REST com Java + Spring Boot
* Front-end Angular
* Persistência com PostgreSQL
* Logs estruturados para troubleshooting
* Testes unitários e de integração
* Documentação da API com Swagger/OpenAPI
* Docker Compose para execução simplificada
* Análise de incidente com proposta de correção e prevenção

---

# Parte 1 — Desenvolvimento

## Cenário Proposto

Foi desenvolvido um módulo de gerenciamento de chamados técnicos.

O sistema permite:

* Criar chamados
* Listar chamados
* Atualizar status
* Filtrar chamados
* Validar dados obrigatórios
* Registrar logs de operação

---

# Arquitetura do Projeto

```bash
project-root/
│
├── backend/
│   ├── src/main/java
│   ├── src/test/java
│   ├── Dockerfile
│   └── pom.xml
│
├── frontend/
│   ├── src/app
│   ├── Dockerfile
│   └── package.json
│
├── docker-compose.yml
└── README.md
```

---

# Back-end

## Tecnologias

* Java 17
* Spring Boot
* Spring Web
* Spring Data JPA
* PostgreSQL
* Lombok
* JUnit
* Mockito
* Swagger/OpenAPI
* Logback

---

## Entidade

```java
@Entity
@Table(name = "tickets")
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class Ticket {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String title;

    @Column(nullable = false)
    private String description;

    @Column(nullable = false)
    private String status;

    private LocalDateTime createdAt;
}
```

---

## Repository

```java
@Repository
public interface TicketRepository extends JpaRepository<Ticket, Long> {

    List<Ticket> findByStatus(String status);
}
```

---

## DTO

```java
@Getter
@Setter
public class TicketDTO {

    @NotBlank
    private String title;

    @NotBlank
    private String description;

    private String status;
}
```

---

## Service

```java
@Service
@RequiredArgsConstructor
@Slf4j
public class TicketService {

    private final TicketRepository repository;

    public Ticket create(TicketDTO dto) {

        log.info("Creating ticket: {}", dto.getTitle());

        Ticket ticket = Ticket.builder()
                .title(dto.getTitle())
                .description(dto.getDescription())
                .status("OPEN")
                .createdAt(LocalDateTime.now())
                .build();

        return repository.save(ticket);
    }

    public List<Ticket> findAll() {
        log.info("Listing all tickets");
        return repository.findAll();
    }

    public Ticket updateStatus(Long id, String status) {

        Ticket ticket = repository.findById(id)
                .orElseThrow(() -> new RuntimeException("Ticket not found"));

        ticket.setStatus(status);

        log.info("Updating ticket {} to status {}", id, status);

        return repository.save(ticket);
    }
}
```

---

## Controller

```java
@RestController
@RequestMapping("/api/tickets")
@RequiredArgsConstructor
@Tag(name = "Tickets")
public class TicketController {

    private final TicketService service;

    @PostMapping
    public ResponseEntity<Ticket> create(@Valid @RequestBody TicketDTO dto) {
        return ResponseEntity.status(HttpStatus.CREATED)
                .body(service.create(dto));
    }

    @GetMapping
    public ResponseEntity<List<Ticket>> findAll() {
        return ResponseEntity.ok(service.findAll());
    }

    @PatchMapping("/{id}/status")
    public ResponseEntity<Ticket> updateStatus(
            @PathVariable Long id,
            @RequestParam String status) {

        return ResponseEntity.ok(service.updateStatus(id, status));
    }
}
```

---

## Tratamento Global de Exceções

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(RuntimeException.class)
    public ResponseEntity<Map<String, String>> handleRuntime(RuntimeException ex) {

        Map<String, String> error = new HashMap<>();
        error.put("message", ex.getMessage());

        return ResponseEntity.badRequest().body(error);
    }
}
```

---

## Logs

Exemplo de log:

```bash
2026-05-21 10:15:32 INFO  Creating ticket: Payment failure
2026-05-21 10:16:12 INFO  Updating ticket 10 to status CLOSED
```

Os logs foram adicionados com foco em:

* rastreabilidade
* troubleshooting
* análise de incidentes
* auditoria mínima

---

## Testes Unitários

```java
@ExtendWith(MockitoExtension.class)
class TicketServiceTest {

    @InjectMocks
    private TicketService service;

    @Mock
    private TicketRepository repository;

    @Test
    void shouldCreateTicket() {

        TicketDTO dto = new TicketDTO();
        dto.setTitle("Error");
        dto.setDescription("API failure");

        Ticket ticket = Ticket.builder()
                .id(1L)
                .title(dto.getTitle())
                .description(dto.getDescription())
                .status("OPEN")
                .build();

        when(repository.save(any())).thenReturn(ticket);

        Ticket result = service.create(dto);

        assertEquals("OPEN", result.getStatus());
    }
}
```

---

# Front-end

## Tecnologias

* Angular
* TypeScript
* Angular Material
* Reactive Forms
* HttpClient

---

## Funcionalidades

* Cadastro de chamados
* Listagem de chamados
* Atualização de status
* Validações de formulário
* Integração com API REST

---

## Serviço HTTP

```typescript
@Injectable({
  providedIn: 'root'
})
export class TicketService {

  private api = 'http://localhost:8080/api/tickets';

  constructor(private http: HttpClient) {}

  create(data: any) {
    return this.http.post(this.api, data);
  }

  findAll() {
    return this.http.get(this.api);
  }

  updateStatus(id: number, status: string) {
    return this.http.patch(`${this.api}/${id}/status?status=${status}`, {});
  }
}
```

---

## Formulário

```typescript
this.form = this.fb.group({
  title: ['', Validators.required],
  description: ['', Validators.required]
});
```

---

# Docker Compose

```yaml
version: '3.9'

services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: tickets
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    ports:
      - "5432:5432"

  backend:
    build: ./backend
    ports:
      - "8080:8080"
    depends_on:
      - postgres

  frontend:
    build: ./frontend
    ports:
      - "4200:4200"
```

---

# Execução do Projeto

## Back-end

```bash
cd backend
mvn clean install
mvn spring-boot:run
```

---

## Front-end

```bash
cd frontend
npm install
ng serve
```

---

## Docker

```bash
docker-compose up --build
```

---

# API Documentation

Swagger disponível em:

```bash
http://localhost:8080/swagger-ui/index.html
```

---

# Endpoints

## Criar chamado

```http
POST /api/tickets
```

Body:

```json
{
  "title": "Erro no login",
  "description": "Usuário não consegue acessar"
}
```

---

## Listar chamados

```http
GET /api/tickets
```

---

## Atualizar status

```http
PATCH /api/tickets/{id}/status
```

---

# Parte 2 — Análise de Incidente

## Cenário

Usuários reportaram lentidão e falhas intermitentes na atualização de status dos chamados.

Logs identificados:

```bash
2026-05-20 11:03:22 ERROR Timeout while connecting to database
2026-05-20 11:03:24 ERROR Could not execute statement
2026-05-20 11:03:25 WARN Retrying operation updateStatus
```

---

# Análise Técnica

## Possível causa raiz

A aplicação apresentava timeout de conexão com banco de dados durante períodos de alta concorrência.

Também foi identificado:

* ausência de pool tuning
* queries sem otimização
* ausência de retry controlado
* falta de monitoramento ativo

---

# Correções Propostas

## Banco de Dados

* otimização de queries
* criação de índices
* ajuste de pool de conexões
* análise de locks

## Aplicação

* timeout configurável
* retry controlado
* tratamento de exceções mais específico
* logs estruturados com correlation-id

## Observabilidade

* integração com Kibana/ELK
* métricas com Prometheus
* dashboards de monitoramento
* alertas automáticos

---

# Medidas Preventivas

* testes de carga
* monitoramento contínuo
* circuit breaker
* health checks
* tracing distribuído
* definição de SLAs
* rollback automatizado
* cobertura maior de testes de integração

---

# Decisões Técnicas

* Spring Boot escolhido pela robustez e produtividade
* PostgreSQL pela confiabilidade e estabilidade
* Angular pela organização arquitetural
* Docker para padronização do ambiente
* Logs estruturados para facilitar troubleshooting
* DTOs para desacoplamento entre camadas

---

# Trade-offs

* Arquitetura simplificada para reduzir complexidade do teste
* Autenticação JWT não implementada devido ao escopo
* Mensageria não aplicada inicialmente

---

# Melhorias Futuras

* autenticação JWT
* refresh token
* Kafka
* observabilidade completa com ELK
* CI/CD
* cache Redis
* testes E2E
* arquitetura baseada em microsserviços
* integração com IA para classificação automática de chamados

---

# Conclusão

O projeto foi desenvolvido seguindo princípios de engenharia de software, organização em camadas, observabilidade, testes automatizados e foco em manutenção evolutiva.

A solução busca demonstrar:

* capacidade de desenvolvimento full stack
* resolução de incidentes
* qualidade de código
* boas práticas arquiteturais
* preocupação com rastreabilidade e operação em produção
* visão de escalabilidade e sustentação
