# 📚 Spring Boot & Microservices — Complete Study Notes

## Core Java & Spring Fundamentals

### Java 17+ Features
- **Records**: Immutable data carriers — `record Order(Long id, String name) { }`
- **Pattern Matching**: `instanceof` with variable binding — `if (obj instanceof String s)`
- **Sealed Classes**: Restricted class hierarchies — `sealed class Shape permits Circle, Square`
- **Text Blocks**: Multi-line strings — `""" SELECT * FROM users """`
- **Switch Expressions**: `switch (day) { case MONDAY, FRIDAY -> "Work"; }`

### Spring Core Concepts
- **IoC Container**: ApplicationContext manages bean lifecycle
- **Dependency Injection**: Constructor, Setter, Field injection
- **Bean Scopes**: Singleton, Prototype, Request, Session, Application
- **Spring AOP**: Cross-cutting concerns — `@Before`, `@After`, `@Around`
- **Spring Boot Auto-Configuration**: Conditional configuration based on classpath

---

## REST API Development

### Annotations
- `@RestController`: RESTful controller
- `@RequestMapping`: Class/ method-level URL mapping
- `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@PatchMapping`
- `@RequestParam`: Query parameters — `?page=1&size=10`
- `@PathVariable`: URL template variables — `/users/{id}`
- `@RequestBody`: JSON to object conversion
- `@ResponseStatus`: Custom HTTP status codes

### Best Practices
- **API Versioning**: URL (`/v1/orders`), Header (`Accept: version=1`), Parameter (`?version=1`)
- **Error Handling**: `@ControllerAdvice` + `@ExceptionHandler`
- **Validation**: `@Valid` + Bean Validation annotations
- **ResponseEntity**: Full control over response status, headers, body
- **HATEOAS**: Hypermedia-driven APIs — links in responses

---

## Spring Data & Persistence

### JPA Annotations
- `@Entity`: Database table mapping
- `@Id`, `@GeneratedValue`: Primary key strategies
- `@Column`: Column-level customization
- `@OneToMany`, `@ManyToOne`, `@OneToOne`, `@ManyToMany`: Relationships
- `@JoinColumn`: Foreign key specification
- `@Query`: Custom JPQL/ native SQL queries
- `@Modifying`: UPDATE/DELETE operations
- `@Transactional`: Declarative transaction management

### Repository Patterns
- `CrudRepository`: Basic CRUD operations
- `JpaRepository`: JPA-specific methods + pagination
- `PagingAndSortingRepository`: Pageable + Sort support
- Custom repository methods: `findByLastName(String lastName)`

### Caching with Redis
- `@EnableCaching`: Enable annotation-driven caching
- `@Cacheable`: Method result caching — `key = "#id"`
- `@CacheEvict`: Remove cache entries
- `@CachePut`: Update cache without interfering method execution
- **RedisTemplate**: Low-level Redis operations
- **@RedisHash**: Object-hash mapping

---

## Microservices Architecture

### Service Discovery — Netflix Eureka
- **Eureka Server**: `@EnableEurekaServer` — Service registry
- **Eureka Client**: `@EnableEurekaClient` — Register service
- **Heartbeat**: 30s interval — lease renewal
- **Self-Preservation Mode**: Protect registry during network partition

### API Gateway — Spring Cloud Gateway
- **Route**: ID + URI + Predicates + Filters
- **Predicates**: Path, Method, Header, Host, Cookie, Query
- **Filters**: AddRequestHeader, RewritePath, CircuitBreaker, RateLimiter
- **Global Filters**: Applied to all routes — order priority
- **Custom Filters**: Implement `GlobalFilter`, `GatewayFilter`

### Centralized Configuration — Spring Cloud Config
- **Config Server**: `@EnableConfigServer` — Git-backed properties
- **Config Client**: `bootstrap.yml` — Fetch config at startup
- **Native Profile**: File system configuration
- **Encryption**: Symmetric (`encrypt.key`) + Asymmetric (JKS keystore)
- **Refresh Scope**: `@RefreshScope` — Runtime property reload
- **Spring Cloud Bus**: Broadcast configuration changes — RabbitMQ/ Kafka

---

## Security

### JWT — JSON Web Tokens
- **Structure**: Header.Payload.Signature
- **Header**: Algorithm (HS256, RS256) + Token type
- **Payload**: Claims — subject, issued at, expiration, custom claims
- **Signature**: `HMACSHA256(base64(header) + "." + base64(payload), secret)`
- **Flow**: Authenticate → Generate JWT → Send to client → Client sends in `Authorization: Bearer <token>` → Validate

### Spring Security
- **SecurityFilterChain**: Define HTTP security rules
- **AuthenticationManager**: Authenticate credentials
- **UserDetailsService**: Load user-specific data
- **PasswordEncoder**: BCrypt, Argon2, PBKDF2
- **Method Security**: `@PreAuthorize`, `@PostAuthorize`, `@Secured`, `@RolesAllowed`
- **CORS**: `@CrossOrigin` — Configure allowed origins

### OAuth2
- **Authorization Server**: Issues tokens
- **Resource Server**: Validates tokens — protects APIs
- **Client**: Application requesting access
- **Grant Types**: Authorization Code, Client Credentials, Password, Refresh Token
- **JWT + OAuth2**: Stateless, scalable authentication

---

## Communication Patterns

### Synchronous — REST/ gRPC
- **REST**: HTTP/1.1, JSON, Stateless, CRUD operations
- **gRPC**: HTTP/2, Protocol Buffers, Streaming, Strong typing

### Asynchronous — Messaging
- **Kafka**:
  - **Producer**: `KafkaTemplate.send()` — Publish to topic
  - **Consumer**: `@KafkaListener` — Subscribe to topic
  - **Partitions**: Parallelism + Ordering guarantee
  - **Consumer Groups**: Load balancing across instances
  - **Exactly-Once Semantics**: Idempotent producer + Transactions

- **JMS**:
  - **Queue**: Point-to-point — One consumer
  - **Topic**: Publish-subscribe — Multiple consumers
  - **JmsTemplate**: Convert and send messages
  - `@JmsListener`: Method-level message consumption

### Event-Driven Architecture
- **Event**: Immutable fact — `OrderCreated`, `PaymentProcessed`
- **Event Sourcing**: Store events, derive state
- **CQRS**: Separate Command (write) + Query (read) models
- **Saga Pattern**: Distributed transactions — Choreography (events) vs Orchestration (coordinator)

---

## Resilience Patterns

### Circuit Breaker — Resilience4j
- **States**: CLOSED (normal) → OPEN (failing) → HALF-OPEN (testing recovery)
- **Configuration**: failure-rate-threshold, sliding-window-size, wait-duration-in-open-state
- **Annotations**: `@CircuitBreaker(name = "service", fallbackMethod = "recover")`
- **Events**: State transitions, call rejections, success/ failure metrics

### Retry Pattern
- **Exponential Backoff**: Delay increases after each attempt
- **Retry Conditions**: Specific exceptions, result predicates
- **@Retryable**: maxAttempts, backoff, retryFor
- **@Recover**: Fallback after all retries exhausted

### Bulkhead Pattern
- **Thread Pool Isolation**: Dedicated thread pool per service
- **Semaphore Isolation**: Limit concurrent executions
- **@Bulkhead**: type = THREADPOOL/ SEMAPHORE

### Rate Limiter
- **Token Bucket Algorithm**: Tokens added at fixed rate, consumed per request
- **@RateLimiter**: limitForPeriod, limitRefreshPeriod, timeoutDuration
- **Headers**: X-RateLimit-Limit, X-RateLimit-Remaining, X-RateLimit-Reset

### Timeout Handling
- **@TimeLimiter**: timeoutDuration, cancelRunningFuture
- **WebClient/ RestTemplate**: connectTimeout, readTimeout
- **Deadline Propagation**: Pass remaining time to downstream services

---

## Observability

### Distributed Tracing — Zipkin/ Jaeger
- **Trace ID**: Unique identifier for entire request flow
- **Span ID**: Individual operation within trace
- **Parent Span ID**: Hierarchical relationship
- **Propagation**: HTTP headers (B3, W3C TraceContext)
- **Sampling**: Head-based (probabilistic), Tail-based (latency/ error based)

### Logging
- **Structured Logging**: JSON format — `{"timestamp":"...","level":"INFO","message":"..."}`
- **MDC**: Mapped Diagnostic Context — `MDC.put("traceId", traceId)`
- **Log Levels**: TRACE, DEBUG, INFO, WARN, ERROR, FATAL
- **Log Aggregation**: ELK Stack — Filebeat → Logstash → Elasticsearch → Kibana

### Metrics — Micrometer + Prometheus
- **Counter**: Monotonically increasing — `Counter.builder("requests.total").register(registry)`
- **Gauge**: Current value — `Gauge.builder("memory.used", heap, MemoryUsage::getUsed)`
- **Timer**: Latency distribution — `Timer.Sample sample = Timer.start(registry)`
- **DistributionSummary**: Size/ amount distribution — `DistributionSummary.builder("order.amount")`
- **Prometheus**: /actuator/prometheus endpoint — Scraped by Prometheus → Visualized in Grafana

### Health Checks — Spring Boot Actuator
- **Liveness Probe**: /actuator/health/liveness — Is app running?
- **Readiness Probe**: /actuator/health/readiness — Can app accept traffic?
- **Custom HealthIndicator**: Implement `HealthIndicator` interface
- **Statuses**: UP, DOWN, OUT_OF_SERVICE, UNKNOWN

---

## API Gateway

### Spring Cloud Gateway
- **Predicate Factories**: Path, Method, Header, Host, Cookie, Query, Weight
- **Filter Factories**: AddRequestHeader, RewritePath, CircuitBreaker, RequestRateLimiter
- **Global Filters**: Logging, Authentication, Correlation ID
- **CORS Configuration**: Allowed origins, methods, headers
- **SSL Termination**: HTTPS at gateway level

---

## Database per Service Pattern

- **Private Database**: Each service owns its database exclusively
- **No Direct Access**: Other services access data only via APIs
- **Polyglot Persistence**: SQL for transactions, NoSQL for scalability, Redis for caching
- **Shared Database Anti-pattern**: Creates tight coupling, prevents independent evolution

---

## Tools & Build Automation

### Maven
- **pom.xml**: Project Object Model — Dependencies, Plugins, Properties
- **Lifecycle**: validate, compile, test, package, verify, install, deploy
- **Profiles**: environment-specific configurations

### Gradle
- **build.gradle**: Groovy/ Kotlin DSL — Task-based build
- **Dependency Configurations**: implementation, compileOnly, runtimeOnly, testImplementation
- **Multi-module Projects**: settings.gradle — include 'module-a', 'module-b'

### Docker
- **Dockerfile**: Multi-stage builds — FROM, COPY, RUN, CMD, ENTRYPOINT
- **docker-compose.yml**: Multi-container orchestration — services, networks, volumes
- **.dockerignore**: Exclude files from build context

---

## Interview Preparation Notes

### Microservices Advantages
- Independent deployment, scaling, technology diversity
- Fault isolation, team autonomy, faster time-to-market

### Microservices Challenges
- Distributed system complexity, network latency, data consistency
- Service discovery, configuration management, observability

### CAP Theorem
- Consistency, Availability, Partition Tolerance — Choose 2
- Microservices: AP (Eureka) vs CP (Consul, ZooKeeper)

### ACID vs BASE
- ACID: Atomicity, Consistency, Isolation, Durability — SQL databases
- BASE: Basically Available, Soft state, Eventual consistency — NoSQL

### Idempotency
- Same request multiple times → Same result
- Implementation: Idempotency key header, database unique constraints, conditional updates

---

*These notes represent hands-on implementation experience with production-grade microservices patterns. Each concept has been applied in working code within this repository.*

---
