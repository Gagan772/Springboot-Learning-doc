# 🚀 Spring Boot Complete Mastery Guide (Beginner → Advanced)

> **Enterprise Training Manual** — A structured, self-contained learning path covering Spring Boot from absolute beginner to 3+ years experienced developer level.  
> **Estimated Duration:** 3–6 months of deep learning.

---

## 📑 Table of Contents

| # | Topic | Link |
|---|-------|------|
| 1 | Java Prerequisites Refresher | [Go](#1--java-prerequisites-refresher) |
| 2 | Spring Core Fundamentals | [Go](#2--spring-core-fundamentals) |
| 3 | Spring Boot Fundamentals | [Go](#3--spring-boot-fundamentals) |
| 4 | Building REST APIs | [Go](#4--building-rest-apis) |
| 5 | Spring Data JPA & Database | [Go](#5--spring-data-jpa--database) |
| 6 | Security (Spring Security & JWT) | [Go](#6--security-spring-security--jwt) |
| 7 | Testing | [Go](#7--testing) |
| 8 | Microservices Concepts | [Go](#8--microservices-concepts) |
| 9 | Advanced Production Concepts | [Go](#9--advanced-production-concepts) |
| 10 | Interview Preparation | [Go](#10--interview-preparation) |
| 11 | Best Practices & Project Structure | [Go](#11--best-practices--project-structure) |

---

## 1. 📘 Java Prerequisites Refresher

### 1.1 OOP Concepts

Object-Oriented Programming is the backbone of Java. Mastery of these four pillars is essential before touching Spring.

| Pillar | Description |
|--------|-------------|
| **Encapsulation** | Wrapping data (fields) and methods in a single unit (class), restricting direct access via access modifiers. |
| **Inheritance** | A class (child) acquires properties/methods of another class (parent) using `extends`. |
| **Polymorphism** | One interface, many implementations — compile-time (overloading) and runtime (overriding). |
| **Abstraction** | Hiding implementation details, exposing only functionality via abstract classes/interfaces. |

```java
// Encapsulation
public class Employee {
    private String name;
    private double salary;

    public Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public double getSalary() { return salary; }
}

// Inheritance + Polymorphism
public class Manager extends Employee {
    private String department;

    public Manager(String name, double salary, String department) {
        super(name, salary);
        this.department = department;
    }

    @Override
    public String toString() {
        return getName() + " manages " + department;
    }
}

// Abstraction
public interface Payable {
    double calculatePay();
}

public class ContractEmployee implements Payable {
    @Override
    public double calculatePay() {
        return 5000.00;
    }
}
```

---

### 1.2 Annotations

Annotations provide metadata about the program. They do not directly affect the code's operation but are heavily used in Spring.

```java
// Built-in annotations
@Override    // Compile-time check that method overrides parent
@Deprecated  // Marks method as deprecated
@SuppressWarnings("unchecked") // Suppresses compiler warnings
@FunctionalInterface // Ensures interface has exactly one abstract method

// Custom annotation
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface LogExecutionTime {
    String value() default "";
}

// Usage
public class UserService {
    @LogExecutionTime("user-fetch")
    public User findUser(Long id) {
        // ...
    }
}
```

> **Key Point:** Spring Boot is built on annotations. Understanding `@Retention`, `@Target`, and how annotation processing works is crucial.

---

### 1.3 Collections Framework

| Interface | Implementation | Ordering | Duplicates | Thread-Safe |
|-----------|---------------|----------|-----------|-------------|
| `List` | `ArrayList` | Insertion order | Yes | No |
| `List` | `LinkedList` | Insertion order | Yes | No |
| `Set` | `HashSet` | No order | No | No |
| `Set` | `TreeSet` | Sorted | No | No |
| `Map` | `HashMap` | No order | Keys: No, Values: Yes | No |
| `Map` | `ConcurrentHashMap` | No order | Keys: No, Values: Yes | Yes |
| `Queue` | `PriorityQueue` | Natural/Comparator | Yes | No |

```java
// ArrayList
List<String> names = new ArrayList<>();
names.add("Alice");
names.add("Bob");
names.forEach(System.out::println);

// HashMap
Map<String, Integer> scores = new HashMap<>();
scores.put("Alice", 95);
scores.put("Bob", 87);
scores.forEach((k, v) -> System.out.println(k + " -> " + v));

// Set - removes duplicates
Set<String> uniqueNames = new HashSet<>(Arrays.asList("Alice", "Bob", "Alice"));
System.out.println(uniqueNames.size()); // 2
```

---

### 1.4 Streams API (Java 8+)

Streams enable functional-style operations on collections.

```java
List<Employee> employees = List.of(
    new Employee("Alice", 75000),
    new Employee("Bob", 60000),
    new Employee("Charlie", 90000),
    new Employee("Diana", 85000)
);

// Filter + Map + Collect
List<String> highEarners = employees.stream()
    .filter(e -> e.getSalary() > 70000)
    .map(Employee::getName)
    .sorted()
    .collect(Collectors.toList());
// [Alice, Charlie, Diana]

// Reduce
double totalSalary = employees.stream()
    .mapToDouble(Employee::getSalary)
    .sum();

// Grouping
Map<String, List<Employee>> grouped = employees.stream()
    .collect(Collectors.groupingBy(e -> e.getSalary() > 80000 ? "Senior" : "Junior"));

// Optional
Optional<Employee> topEarner = employees.stream()
    .max(Comparator.comparing(Employee::getSalary));
topEarner.ifPresent(e -> System.out.println("Top: " + e.getName()));
```

---

### 1.5 Exception Handling

| Type | Description | Must Handle? |
|------|-------------|--------------|
| **Checked** | Compile-time exceptions (IOException, SQLException) | Yes |
| **Unchecked** | Runtime exceptions (NullPointerException, IllegalArgumentException) | No (but recommended) |
| **Error** | JVM-level errors (OutOfMemoryError, StackOverflowError) | No |

```java
// Custom exception
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}

// Try-with-resources
public String readFile(String path) {
    try (BufferedReader reader = new BufferedReader(new FileReader(path))) {
        return reader.lines().collect(Collectors.joining("\n"));
    } catch (IOException e) {
        throw new RuntimeException("Failed to read file: " + path, e);
    }
}

// Multi-catch
try {
    // risky operations
} catch (IOException | SQLException e) {
    log.error("Operation failed", e);
}
```

---

### 1.6 Multithreading Basics

```java
// Using Runnable (preferred)
Runnable task = () -> {
    System.out.println("Running in: " + Thread.currentThread().getName());
};
Thread thread = new Thread(task);
thread.start();

// ExecutorService (enterprise approach)
ExecutorService executor = Executors.newFixedThreadPool(4);
Future<String> future = executor.submit(() -> {
    Thread.sleep(1000);
    return "Completed!";
});
String result = future.get(); // blocks until done
executor.shutdown();

// CompletableFuture (modern async)
CompletableFuture.supplyAsync(() -> fetchUserFromDB(1L))
    .thenApply(user -> user.getName())
    .thenAccept(name -> System.out.println("User: " + name))
    .exceptionally(ex -> { log.error("Failed", ex); return null; });
```

> **Interview Note:** Always use `ExecutorService` or `CompletableFuture` in production. Never manually create raw threads.

---

### 1.7 Maven Basics

Maven is the build tool for most Spring Boot projects.

| Concept | Description |
|---------|-------------|
| `pom.xml` | Project Object Model — defines project metadata, dependencies, plugins |
| `groupId` | Organization identifier (e.g., `com.company`) |
| `artifactId` | Project name |
| `version` | Project version |
| Dependency Scope | `compile`, `test`, `provided`, `runtime` |

```xml
<!-- pom.xml structure -->
<project>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

**Common Maven Commands:**

```bash
mvn clean install          # Clean + compile + test + package
mvn spring-boot:run        # Run Spring Boot app
mvn dependency:tree        # Show dependency tree
mvn test                   # Run tests only
mvn package -DskipTests    # Package without running tests
```

---

## 2. 🌱 Spring Core Fundamentals

### 2.1 What is Spring?

Spring is a comprehensive framework for enterprise Java development. It provides infrastructure support so you can focus on business logic.

| Feature | Description |
|---------|-------------|
| **Lightweight** | Minimal overhead, POJO-based |
| **IoC Container** | Manages object creation and lifecycle |
| **AOP** | Separates cross-cutting concerns |
| **Transaction Management** | Declarative transaction support |
| **MVC Framework** | Web application development |
| **Data Access** | JDBC, ORM, JPA abstractions |

---

### 2.2 Inversion of Control (IoC)

IoC is a design principle where the control of object creation is transferred from the application code to the Spring container.

**Without IoC (tight coupling):**
```java
public class OrderService {
    private PaymentService paymentService = new PaymentService(); // tight coupling
}
```

**With IoC (loose coupling):**
```java
@Service
public class OrderService {
    private final PaymentService paymentService;

    @Autowired
    public OrderService(PaymentService paymentService) { // injected by Spring
        this.paymentService = paymentService;
    }
}
```

> **Interview Note:** IoC is the principle; Dependency Injection (DI) is the implementation pattern. Spring achieves IoC through DI.

---

### 2.3 Dependency Injection (DI)

Three types of DI in Spring:

| Type | Recommended? | Description |
|------|-------------|-------------|
| **Constructor Injection** | ✅ Yes | Dependencies via constructor parameters |
| **Setter Injection** | ⚠️ Sometimes | Dependencies via setter methods |
| **Field Injection** | ❌ Avoid | Dependencies directly injected into fields via `@Autowired` |

```java
// ✅ Constructor Injection (RECOMMENDED)
@Service
public class UserService {
    private final UserRepository userRepository;
    private final EmailService emailService;

    // @Autowired is optional if single constructor (Spring 4.3+)
    public UserService(UserRepository userRepository, EmailService emailService) {
        this.userRepository = userRepository;
        this.emailService = emailService;
    }
}

// ⚠️ Setter Injection
@Service
public class NotificationService {
    private SMSService smsService;

    @Autowired
    public void setSmsService(SMSService smsService) {
        this.smsService = smsService;
    }
}

// ❌ Field Injection (avoid in production)
@Service
public class ReportService {
    @Autowired
    private ReportRepository reportRepository; // hard to test, hides dependencies
}
```

> **Why Constructor Injection?**
> - Makes dependencies explicit
> - Enables immutability (`final` fields)
> - Easier unit testing (no reflection needed)
> - Fails fast if dependency is missing

---

### 2.4 Bean Lifecycle

```
Container Started
    → Bean Definition Loaded
        → Bean Instantiated (Constructor)
            → Dependencies Injected
                → @PostConstruct called
                    → InitializingBean.afterPropertiesSet()
                        → Custom init-method
                            → Bean Ready for Use
                                → @PreDestroy called
                                    → DisposableBean.destroy()
                                        → Custom destroy-method
                                            → Bean Destroyed
```

```java
@Component
public class CacheService {

    @PostConstruct
    public void init() {
        System.out.println("CacheService initialized — loading cache...");
        // Load initial cache data
    }

    @PreDestroy
    public void cleanup() {
        System.out.println("CacheService shutting down — clearing cache...");
        // Release resources
    }
}
```

**Bean Scopes:**

| Scope | Description |
|-------|-------------|
| `singleton` (default) | One instance per Spring container |
| `prototype` | New instance every time it's requested |
| `request` | One instance per HTTP request (web only) |
| `session` | One instance per HTTP session (web only) |
| `application` | One instance per ServletContext |

```java
@Component
@Scope("prototype")
public class ReportGenerator {
    // New instance created each time this bean is requested
}
```

---

### 2.5 ApplicationContext

`ApplicationContext` is the central interface for Spring IoC container.

| Implementation | Use Case |
|---------------|----------|
| `AnnotationConfigApplicationContext` | Java-based config |
| `ClassPathXmlApplicationContext` | XML-based config |
| `WebApplicationContext` | Web applications |

```java
// Java-based configuration
@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig {
    // Configuration class
}

// Accessing ApplicationContext
@Component
public class AppRunner implements ApplicationContextAware {
    private ApplicationContext context;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) {
        this.context = applicationContext;
    }

    public void printBeans() {
        String[] beanNames = context.getBeanDefinitionNames();
        Arrays.stream(beanNames).sorted().forEach(System.out::println);
    }
}
```

---

### 2.6 Stereotype Annotations

| Annotation | Layer | Purpose |
|------------|-------|---------|
| `@Component` | Generic | General-purpose Spring-managed bean |
| `@Service` | Business | Business logic layer — semantic clarity |
| `@Repository` | Data | Data access layer — enables exception translation |
| `@Controller` | Web | MVC controller for views |
| `@RestController` | Web | REST API controller (`@Controller` + `@ResponseBody`) |

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {}

@Service
public class UserService {
    private final UserRepository userRepository;
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}

@RestController
@RequestMapping("/api/users")
public class UserController {
    private final UserService userService;
    public UserController(UserService userService) {
        this.userService = userService;
    }
}
```

> **Interview Note:** All stereotype annotations are specializations of `@Component`. `@Repository` adds persistence exception translation. `@Service` and `@Controller` are mainly for semantic clarity and AOP targeting.

---

### 2.7 @Configuration and @Bean

```java
@Configuration
public class AppConfig {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplateBuilder()
                .setConnectTimeout(Duration.ofSeconds(5))
                .setReadTimeout(Duration.ofSeconds(10))
                .build();
    }

    @Bean
    public ObjectMapper objectMapper() {
        ObjectMapper mapper = new ObjectMapper();
        mapper.registerModule(new JavaTimeModule());
        mapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
        return mapper;
    }

    @Bean
    @Primary // Default bean when multiple candidates exist
    public DataSource primaryDataSource() {
        return DataSourceBuilder.create()
                .url("jdbc:mysql://localhost:3306/maindb")
                .build();
    }

    @Bean
    @Qualifier("reporting")
    public DataSource reportingDataSource() {
        return DataSourceBuilder.create()
                .url("jdbc:mysql://localhost:3306/reportdb")
                .build();
    }
}
```

---

### 2.8 Profiles

Profiles allow you to define environment-specific configurations.

```java
@Configuration
@Profile("dev")
public class DevConfig {
    @Bean
    public DataSource dataSource() {
        return new EmbeddedDatabaseBuilder()
                .setType(EmbeddedDatabaseType.H2)
                .build();
    }
}

@Configuration
@Profile("prod")
public class ProdConfig {
    @Bean
    public DataSource dataSource() {
        return DataSourceBuilder.create()
                .url("jdbc:mysql://prod-server:3306/appdb")
                .username("produser")
                .password("${DB_PASSWORD}")
                .build();
    }
}
```

**Activating profiles:**
```properties
# application.properties
spring.profiles.active=dev

# or via command line
java -jar app.jar --spring.profiles.active=prod

# or via environment variable
SPRING_PROFILES_ACTIVE=prod
```

---

### 2.9 Example Project Structure

```
src/
├── main/
│   ├── java/
│   │   └── com/example/myapp/
│   │       ├── MyAppApplication.java          // Main class
│   │       ├── config/
│   │       │   ├── AppConfig.java
│   │       │   ├── SecurityConfig.java
│   │       │   └── SwaggerConfig.java
│   │       ├── controller/
│   │       │   └── UserController.java
│   │       ├── service/
│   │       │   ├── UserService.java
│   │       │   └── impl/
│   │       │       └── UserServiceImpl.java
│   │       ├── repository/
│   │       │   └── UserRepository.java
│   │       ├── model/
│   │       │   ├── entity/
│   │       │   │   └── User.java
│   │       │   └── dto/
│   │       │       ├── UserRequestDTO.java
│   │       │       └── UserResponseDTO.java
│   │       ├── exception/
│   │       │   ├── GlobalExceptionHandler.java
│   │       │   └── ResourceNotFoundException.java
│   │       └── util/
│   │           └── DateUtils.java
│   └── resources/
│       ├── application.properties
│       ├── application-dev.properties
│       ├── application-prod.properties
│       └── static/
└── test/
    └── java/
        └── com/example/myapp/
            ├── controller/
            │   └── UserControllerTest.java
            ├── service/
            │   └── UserServiceTest.java
            └── repository/
                └── UserRepositoryTest.java
```

---

## 3. 🚀 Spring Boot Fundamentals

### 3.1 What is Spring Boot?

Spring Boot is an opinionated framework that simplifies Spring application development by providing auto-configuration, embedded servers, and production-ready defaults.

| Feature | Spring | Spring Boot |
|---------|--------|-------------|
| Configuration | Manual, verbose XML/Java | Auto-configuration |
| Server | External (Tomcat WAR) | Embedded (Tomcat/Jetty/Undertow JAR) |
| Dependency Mgmt | Manual version management | Starter POMs with curated versions |
| Production Ready | Manual setup | Actuator, Health checks built-in |
| Setup Time | Hours | Minutes |

```java
// Minimal Spring Boot Application
@SpringBootApplication  // = @Configuration + @EnableAutoConfiguration + @ComponentScan
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

> **Interview Note:** `@SpringBootApplication` is a convenience annotation that combines:
> - `@Configuration` — marks the class as a source of bean definitions
> - `@EnableAutoConfiguration` — enables Spring Boot's auto-configuration
> - `@ComponentScan` — scans the package and sub-packages for components

---

### 3.2 Auto-Configuration

Spring Boot automatically configures beans based on classpath dependencies and properties.

**How it works:**
1. Spring Boot scans `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`
2. Each auto-config class uses `@Conditional` annotations to decide if it should apply
3. Your explicit configuration always takes priority

```java
// Spring Boot auto-configures DataSource if:
// 1. spring-boot-starter-data-jpa is on classpath
// 2. Database driver (H2, MySQL) is on classpath
// 3. spring.datasource.url is configured

// You can exclude auto-configurations
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
public class MyApplication { }

// Or via properties
// spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
```

**Common Mistake:** Adding `spring-boot-starter-data-jpa` without a database driver causes startup failure. Always add both the starter and the driver.

---

### 3.3 Starter Dependencies

Starters are curated dependency descriptors that bring in everything you need for a feature.

| Starter | Purpose |
|---------|---------|
| `spring-boot-starter-web` | REST APIs, MVC, embedded Tomcat |
| `spring-boot-starter-data-jpa` | JPA + Hibernate |
| `spring-boot-starter-security` | Spring Security |
| `spring-boot-starter-test` | JUnit, Mockito, AssertJ |
| `spring-boot-starter-validation` | Bean validation (Hibernate Validator) |
| `spring-boot-starter-actuator` | Production monitoring endpoints |
| `spring-boot-starter-cache` | Caching abstraction |
| `spring-boot-starter-mail` | Email sending |
| `spring-boot-starter-amqp` | RabbitMQ messaging |
| `spring-boot-starter-data-redis` | Redis data access |
| `spring-boot-starter-websocket` | WebSocket support |

```xml
<!-- Just add the starter — no manual version management needed -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <!-- Version inherited from parent POM -->
</dependency>
```

---

### 3.4 application.properties vs application.yml

**application.properties:**
```properties
server.port=8080
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=secret
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
logging.level.com.example=DEBUG
```

**application.yml (equivalent):**
```yaml
server:
  port: 8080

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: secret
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true

logging:
  level:
    com.example: DEBUG
```

| Feature | properties | YAML |
|---------|-----------|------|
| Readability | Flat, repetitive prefixes | Hierarchical, cleaner |
| Multi-profile in one file | No | Yes (using `---`) |
| List support | Indexed (`list[0]=a`) | Native (`- a`) |
| Complexity | Simple | Can be error-prone (indentation) |

**Binding properties to a class:**
```java
@Component
@ConfigurationProperties(prefix = "app.mail")
public class MailProperties {
    private String host;
    private int port;
    private String from;
    // getters and setters
}
```

```yaml
app:
  mail:
    host: smtp.gmail.com
    port: 587
    from: no-reply@example.com
```

**Common Mistake:** YAML is indentation-sensitive. Using tabs instead of spaces will cause parse errors. Always use spaces.

---

### 3.5 Embedded Servers

Spring Boot embeds Tomcat, Jetty, or Undertow directly into the JAR.

| Server | Default? | Best For |
|--------|----------|----------|
| **Tomcat** | ✅ Yes | General purpose, most common |
| **Jetty** | No | Lightweight, async-heavy apps |
| **Undertow** | No | High-performance, non-blocking |

```xml
<!-- Switch from Tomcat to Undertow -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-undertow</artifactId>
</dependency>
```

---

### 3.6 Spring Boot DevTools

DevTools provides automatic restart, LiveReload, and development-friendly defaults.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
```

| Feature | Description |
|---------|-------------|
| Auto-restart | Restarts app on classpath changes |
| LiveReload | Refreshes browser automatically |
| Disable caching | Template caches disabled in dev |
| H2 Console | Auto-enabled in dev |

**Common Mistake:** DevTools must NOT be included in production. Using `<optional>true</optional>` ensures it's not packaged transitively.

---

### 3.7 Spring Boot Actuator

Actuator provides production-ready monitoring endpoints.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,env,beans,mappings
  endpoint:
    health:
      show-details: always
  info:
    env:
      enabled: true
```

| Endpoint | URL | Purpose |
|----------|-----|---------|
| Health | `/actuator/health` | App health status |
| Info | `/actuator/info` | App metadata |
| Metrics | `/actuator/metrics` | Performance metrics |
| Env | `/actuator/env` | Environment properties |
| Beans | `/actuator/beans` | All registered beans |
| Mappings | `/actuator/mappings` | All request mappings |
| Loggers | `/actuator/loggers` | View/change log levels at runtime |

**Custom Health Indicator:**
```java
@Component
public class DatabaseHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {
        boolean dbIsUp = checkDatabase();
        if (dbIsUp) {
            return Health.up()
                    .withDetail("database", "MySQL")
                    .withDetail("status", "reachable")
                    .build();
        }
        return Health.down()
                .withDetail("error", "Cannot reach database")
                .build();
    }

    private boolean checkDatabase() {
        // Check database connectivity
        return true;
    }
}
```

---

### 3.8 Spring Boot CLI

The Spring Boot CLI allows rapid prototyping with Groovy scripts.

```bash
# Install via SDKMAN
sdk install springboot

# Create a new project
spring init --dependencies=web,data-jpa,mysql --java-version=17 myproject

# Run a Groovy script
spring run app.groovy
```

> In practice, most teams use **Spring Initializr** (https://start.spring.io) to bootstrap projects.

---

## 4. 🌐 Building REST APIs

### 4.1 @RestController and @RequestMapping

```java
@RestController
@RequestMapping("/api/v1/users")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    // Endpoints go here
}
```

> `@RestController` = `@Controller` + `@ResponseBody`  
> All methods return data directly (JSON by default), not view names.

---

### 4.2 CRUD Operations

```java
@RestController
@RequestMapping("/api/v1/users")
@Validated
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    // GET all users
    @GetMapping
    public ResponseEntity<List<UserResponseDTO>> getAllUsers() {
        return ResponseEntity.ok(userService.getAllUsers());
    }

    // GET user by ID
    @GetMapping("/{id}")
    public ResponseEntity<UserResponseDTO> getUserById(@PathVariable Long id) {
        return ResponseEntity.ok(userService.getUserById(id));
    }

    // POST create user
    @PostMapping
    public ResponseEntity<UserResponseDTO> createUser(@Valid @RequestBody UserRequestDTO request) {
        UserResponseDTO created = userService.createUser(request);
        URI location = URI.create("/api/v1/users/" + created.getId());
        return ResponseEntity.created(location).body(created);
    }

    // PUT update user
    @PutMapping("/{id}")
    public ResponseEntity<UserResponseDTO> updateUser(
            @PathVariable Long id,
            @Valid @RequestBody UserRequestDTO request) {
        return ResponseEntity.ok(userService.updateUser(id, request));
    }

    // DELETE user
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.deleteUser(id);
        return ResponseEntity.noContent().build();
    }

    // GET with query params
    @GetMapping("/search")
    public ResponseEntity<List<UserResponseDTO>> searchUsers(
            @RequestParam(required = false) String name,
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "10") int size) {
        return ResponseEntity.ok(userService.searchUsers(name, page, size));
    }
}
```

---

### 4.3 DTO Pattern

DTOs (Data Transfer Objects) decouple your API contract from internal entities.

```java
// Request DTO
public class UserRequestDTO {

    @NotBlank(message = "Name is required")
    @Size(min = 2, max = 100, message = "Name must be between 2 and 100 characters")
    private String name;

    @NotBlank(message = "Email is required")
    @Email(message = "Invalid email format")
    private String email;

    @NotNull(message = "Age is required")
    @Min(value = 18, message = "Age must be at least 18")
    @Max(value = 120, message = "Age must not exceed 120")
    private Integer age;

    // Getters, setters, constructors
}

// Response DTO
public class UserResponseDTO {
    private Long id;
    private String name;
    private String email;
    private Integer age;
    private LocalDateTime createdAt;

    // Static factory method from entity
    public static UserResponseDTO fromEntity(User user) {
        UserResponseDTO dto = new UserResponseDTO();
        dto.setId(user.getId());
        dto.setName(user.getName());
        dto.setEmail(user.getEmail());
        dto.setAge(user.getAge());
        dto.setCreatedAt(user.getCreatedAt());
        return dto;
    }
}
```

> **Best Practice:** Never expose your JPA entities directly in API responses. Always use DTOs.

---

### 4.4 Service Layer

```java
public interface UserService {
    List<UserResponseDTO> getAllUsers();
    UserResponseDTO getUserById(Long id);
    UserResponseDTO createUser(UserRequestDTO request);
    UserResponseDTO updateUser(Long id, UserRequestDTO request);
    void deleteUser(Long id);
    List<UserResponseDTO> searchUsers(String name, int page, int size);
}

@Service
@Transactional(readOnly = true)
public class UserServiceImpl implements UserService {

    private final UserRepository userRepository;
    private static final Logger log = LoggerFactory.getLogger(UserServiceImpl.class);

    public UserServiceImpl(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @Override
    public List<UserResponseDTO> getAllUsers() {
        return userRepository.findAll().stream()
                .map(UserResponseDTO::fromEntity)
                .collect(Collectors.toList());
    }

    @Override
    public UserResponseDTO getUserById(Long id) {
        User user = userRepository.findById(id)
                .orElseThrow(() -> new ResourceNotFoundException("User not found with id: " + id));
        return UserResponseDTO.fromEntity(user);
    }

    @Override
    @Transactional
    public UserResponseDTO createUser(UserRequestDTO request) {
        if (userRepository.existsByEmail(request.getEmail())) {
            throw new DuplicateResourceException("Email already registered: " + request.getEmail());
        }
        User user = new User();
        user.setName(request.getName());
        user.setEmail(request.getEmail());
        user.setAge(request.getAge());
        User saved = userRepository.save(user);
        log.info("User created with id: {}", saved.getId());
        return UserResponseDTO.fromEntity(saved);
    }

    @Override
    @Transactional
    public UserResponseDTO updateUser(Long id, UserRequestDTO request) {
        User user = userRepository.findById(id)
                .orElseThrow(() -> new ResourceNotFoundException("User not found with id: " + id));
        user.setName(request.getName());
        user.setEmail(request.getEmail());
        user.setAge(request.getAge());
        User updated = userRepository.save(user);
        log.info("User updated with id: {}", updated.getId());
        return UserResponseDTO.fromEntity(updated);
    }

    @Override
    @Transactional
    public void deleteUser(Long id) {
        if (!userRepository.existsById(id)) {
            throw new ResourceNotFoundException("User not found with id: " + id);
        }
        userRepository.deleteById(id);
        log.info("User deleted with id: {}", id);
    }

    @Override
    public List<UserResponseDTO> searchUsers(String name, int page, int size) {
        Pageable pageable = PageRequest.of(page, size, Sort.by("name").ascending());
        Page<User> users = (name != null)
                ? userRepository.findByNameContainingIgnoreCase(name, pageable)
                : userRepository.findAll(pageable);
        return users.stream()
                .map(UserResponseDTO::fromEntity)
                .collect(Collectors.toList());
    }
}
```

---

### 4.5 Validation

```xml
<!-- Add validation starter -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

| Annotation | Description |
|------------|-------------|
| `@NotNull` | Field must not be null |
| `@NotBlank` | String must not be null or empty |
| `@NotEmpty` | Collection must not be null or empty |
| `@Size(min, max)` | String/Collection size constraint |
| `@Min` / `@Max` | Numeric range |
| `@Email` | Valid email format |
| `@Pattern` | Regex pattern match |
| `@Past` / `@Future` | Date must be in past/future |
| `@Valid` | Triggers nested validation |

**Custom Validator:**
```java
// Annotation
@Documented
@Constraint(validatedBy = UniqueEmailValidator.class)
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface UniqueEmail {
    String message() default "Email already exists";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}

// Validator
public class UniqueEmailValidator implements ConstraintValidator<UniqueEmail, String> {
    @Autowired
    private UserRepository userRepository;

    @Override
    public boolean isValid(String email, ConstraintValidatorContext context) {
        return email != null && !userRepository.existsByEmail(email);
    }
}
```

---

### 4.6 Global Exception Handling

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    private static final Logger log = LoggerFactory.getLogger(GlobalExceptionHandler.class);

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
        log.warn("Resource not found: {}", ex.getMessage());
        ErrorResponse error = new ErrorResponse(
                HttpStatus.NOT_FOUND.value(),
                ex.getMessage(),
                LocalDateTime.now()
        );
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }

    @ExceptionHandler(DuplicateResourceException.class)
    public ResponseEntity<ErrorResponse> handleDuplicate(DuplicateResourceException ex) {
        log.warn("Duplicate resource: {}", ex.getMessage());
        ErrorResponse error = new ErrorResponse(
                HttpStatus.CONFLICT.value(),
                ex.getMessage(),
                LocalDateTime.now()
        );
        return ResponseEntity.status(HttpStatus.CONFLICT).body(error);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException ex) {
        Map<String, String> fieldErrors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(err ->
                fieldErrors.put(err.getField(), err.getDefaultMessage()));

        ErrorResponse error = new ErrorResponse(
                HttpStatus.BAD_REQUEST.value(),
                "Validation failed",
                LocalDateTime.now(),
                fieldErrors
        );
        return ResponseEntity.badRequest().body(error);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGeneric(Exception ex) {
        log.error("Unexpected error", ex);
        ErrorResponse error = new ErrorResponse(
                HttpStatus.INTERNAL_SERVER_ERROR.value(),
                "An unexpected error occurred",
                LocalDateTime.now()
        );
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
    }
}

// Error response model
public class ErrorResponse {
    private int status;
    private String message;
    private LocalDateTime timestamp;
    private Map<String, String> fieldErrors;

    public ErrorResponse(int status, String message, LocalDateTime timestamp) {
        this.status = status;
        this.message = message;
        this.timestamp = timestamp;
    }

    public ErrorResponse(int status, String message, LocalDateTime timestamp, Map<String, String> fieldErrors) {
        this(status, message, timestamp);
        this.fieldErrors = fieldErrors;
    }

    // Getters and setters
}

// Custom exceptions
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}

public class DuplicateResourceException extends RuntimeException {
    public DuplicateResourceException(String message) {
        super(message);
    }
}
```

---

### 4.7 Logging Best Practices

```java
@Service
public class OrderService {

    // Use SLF4J Logger — Spring Boot's default logging facade
    private static final Logger log = LoggerFactory.getLogger(OrderService.class);

    public Order processOrder(OrderRequest request) {
        log.info("Processing order for customer: {}", request.getCustomerId());
        log.debug("Order details: {}", request);

        try {
            Order order = createOrder(request);
            log.info("Order created successfully: orderId={}", order.getId());
            return order;
        } catch (InsufficientStockException e) {
            log.warn("Insufficient stock for product: {}", request.getProductId());
            throw e;
        } catch (Exception e) {
            log.error("Failed to process order for customer: {}", request.getCustomerId(), e);
            throw new OrderProcessingException("Order processing failed", e);
        }
    }
}
```

**Logging configuration:**
```yaml
logging:
  level:
    root: INFO
    com.example.myapp: DEBUG
    org.springframework.web: WARN
    org.hibernate.SQL: DEBUG
  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n"
  file:
    name: logs/application.log
    max-size: 10MB
    max-history: 30
```

| Log Level | When to Use |
|-----------|-------------|
| `TRACE` | Very detailed flow data, rarely needed |
| `DEBUG` | Development diagnostics, not production |
| `INFO` | Key business events, startup messages |
| `WARN` | Recoverable problems, degraded functionality |
| `ERROR` | Failures that need attention |

---

### 4.8 REST API Best Practices

| Practice | Example |
|----------|---------|
| Use nouns, not verbs | `/api/users` not `/api/getUsers` |
| Use plural nouns | `/api/users` not `/api/user` |
| Version your API | `/api/v1/users` |
| Use proper HTTP methods | GET (read), POST (create), PUT (update), DELETE (remove) |
| Use proper status codes | 200 OK, 201 Created, 204 No Content, 400 Bad Request, 404 Not Found |
| Pagination for lists | `?page=0&size=10&sort=name,asc` |
| Consistent error format | Always return structured error objects |
| Use DTOs | Never expose entities directly |
| HATEOAS (advanced) | Include links to related resources |

---

## 5. 💾 Spring Data JPA & Database

### 5.1 Entity Mapping

```java
@Entity
@Table(name = "users", indexes = {
    @Index(name = "idx_user_email", columnList = "email", unique = true)
})
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 100)
    private String name;

    @Column(nullable = false, unique = true, length = 255)
    private String email;

    @Column(nullable = false)
    private Integer age;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false)
    private UserStatus status = UserStatus.ACTIVE;

    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createdAt;

    @LastModifiedDate
    private LocalDateTime updatedAt;

    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Order> orders = new ArrayList<>();

    // Constructors, Getters, Setters

    // Correct equals/hashCode for JPA
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof User)) return false;
        User user = (User) o;
        return id != null && id.equals(user.getId());
    }

    @Override
    public int hashCode() {
        return getClass().hashCode();
    }
}

public enum UserStatus {
    ACTIVE, INACTIVE, SUSPENDED
}
```

**Relationship Annotations:**

| Annotation | Description | Example |
|------------|-------------|---------|
| `@OneToOne` | One-to-one | User ↔ Profile |
| `@OneToMany` | One-to-many | User → Orders |
| `@ManyToOne` | Many-to-one | Order → User |
| `@ManyToMany` | Many-to-many | User ↔ Roles |

```java
@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String product;
    private BigDecimal amount;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false)
    private User user;
}
```

> **Interview Note:** Always use `FetchType.LAZY` for `@ManyToOne` and `@OneToMany` to avoid N+1 query problems. Use `JOIN FETCH` in queries when you need eager loading.

---

### 5.2 Repository Layer

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // Derived query methods (Spring generates SQL)
    Optional<User> findByEmail(String email);
    List<User> findByNameContainingIgnoreCase(String name);
    List<User> findByStatusAndAgeBetween(UserStatus status, int minAge, int maxAge);
    boolean existsByEmail(String email);
    long countByStatus(UserStatus status);

    // Derived query with pagination
    Page<User> findByNameContainingIgnoreCase(String name, Pageable pageable);

    // JPQL (Java Persistence Query Language)
    @Query("SELECT u FROM User u WHERE u.status = :status ORDER BY u.createdAt DESC")
    List<User> findActiveUsers(@Param("status") UserStatus status);

    // JPQL with JOIN FETCH (solves N+1)
    @Query("SELECT u FROM User u LEFT JOIN FETCH u.orders WHERE u.id = :id")
    Optional<User> findByIdWithOrders(@Param("id") Long id);

    // Native SQL
    @Query(value = "SELECT * FROM users WHERE email LIKE %:domain", nativeQuery = true)
    List<User> findByEmailDomain(@Param("domain") String domain);

    // Modifying queries
    @Modifying
    @Transactional
    @Query("UPDATE User u SET u.status = :status WHERE u.id = :id")
    int updateUserStatus(@Param("id") Long id, @Param("status") UserStatus status);

    // Delete
    @Modifying
    @Transactional
    void deleteByStatus(UserStatus status);
}
```

**JpaRepository Hierarchy:**

```
Repository (marker)
    └── CrudRepository (CRUD operations)
        └── PagingAndSortingRepository (pagination + sorting)
            └── JpaRepository (JPA-specific: flush, batch, etc.)
```

---

### 5.3 Pagination and Sorting

```java
// Controller
@GetMapping
public ResponseEntity<Page<UserResponseDTO>> getAllUsers(
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "10") int size,
        @RequestParam(defaultValue = "id") String sortBy,
        @RequestParam(defaultValue = "asc") String direction) {

    Sort sort = direction.equalsIgnoreCase("desc")
            ? Sort.by(sortBy).descending()
            : Sort.by(sortBy).ascending();
    Pageable pageable = PageRequest.of(page, size, sort);

    Page<UserResponseDTO> users = userService.getAllUsers(pageable);
    return ResponseEntity.ok(users);
}

// Service
public Page<UserResponseDTO> getAllUsers(Pageable pageable) {
    return userRepository.findAll(pageable)
            .map(UserResponseDTO::fromEntity);
}
```

**Response format:**
```json
{
  "content": [/* array of users */],
  "pageable": {
    "pageNumber": 0,
    "pageSize": 10,
    "sort": { "sorted": true, "unsorted": false }
  },
  "totalElements": 45,
  "totalPages": 5,
  "last": false,
  "first": true
}
```

---

### 5.4 Transactions

```java
@Service
@Transactional(readOnly = true) // Default for read operations
public class TransferService {

    @Transactional // Writable transaction
    public void transferMoney(Long fromId, Long toId, BigDecimal amount) {
        Account from = accountRepository.findById(fromId)
                .orElseThrow(() -> new ResourceNotFoundException("Account not found"));
        Account to = accountRepository.findById(toId)
                .orElseThrow(() -> new ResourceNotFoundException("Account not found"));

        if (from.getBalance().compareTo(amount) < 0) {
            throw new InsufficientFundsException("Insufficient balance");
        }

        from.setBalance(from.getBalance().subtract(amount));
        to.setBalance(to.getBalance().add(amount));

        accountRepository.save(from);
        accountRepository.save(to);
        // If any exception occurs, BOTH saves are rolled back
    }
}
```

| Attribute | Description |
|-----------|-------------|
| `readOnly` | Optimization hint for read-only queries |
| `propagation` | `REQUIRED` (default), `REQUIRES_NEW`, `NESTED` |
| `isolation` | `READ_COMMITTED` (default for most DBs), `SERIALIZABLE` |
| `rollbackFor` | Exceptions that trigger rollback |
| `timeout` | Transaction timeout in seconds |

> **Interview Note:** `@Transactional` only works on public methods when using proxy-based AOP. Self-invocation (calling a transactional method from within the same class) will NOT trigger the transaction.

---

### 5.5 Auditing

```java
// Enable JPA Auditing
@Configuration
@EnableJpaAuditing
public class JpaConfig {

    @Bean
    public AuditorAware<String> auditorProvider() {
        return () -> Optional.ofNullable(SecurityContextHolder.getContext())
                .map(SecurityContext::getAuthentication)
                .filter(Authentication::isAuthenticated)
                .map(Authentication::getName)
                .or(() -> Optional.of("SYSTEM"));
    }
}

// Base auditable entity
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class Auditable {

    @CreatedBy
    @Column(updatable = false)
    private String createdBy;

    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createdAt;

    @LastModifiedBy
    private String updatedBy;

    @LastModifiedDate
    private LocalDateTime updatedAt;

    // Getters and setters
}

// Entity extending auditable
@Entity
public class Product extends Auditable {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private BigDecimal price;
}
```

---

### 5.6 Database Configuration

**MySQL Configuration:**
```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb?useSSL=false&serverTimezone=UTC
    username: root
    password: secret
    driver-class-name: com.mysql.cj.jdbc.Driver
    hikari:
      maximum-pool-size: 10
      minimum-idle: 5
      idle-timeout: 300000
      connection-timeout: 20000
  jpa:
    hibernate:
      ddl-auto: validate  # NEVER use update/create in production
    show-sql: false
    properties:
      hibernate:
        format_sql: true
        dialect: org.hibernate.dialect.MySQLDialect
```

**H2 In-Memory (for testing/development):**
```yaml
spring:
  datasource:
    url: jdbc:h2:mem:testdb
    driver-class-name: org.h2.Driver
    username: sa
    password:
  h2:
    console:
      enabled: true
      path: /h2-console
  jpa:
    hibernate:
      ddl-auto: create-drop
    database-platform: org.hibernate.dialect.H2Dialect
```

| ddl-auto Value | Description | Environment |
|----------------|-------------|-------------|
| `none` | No action | Production |
| `validate` | Validate schema matches entities | Production |
| `update` | Update schema (additive only) | Development |
| `create` | Create schema (drop + create) | Testing |
| `create-drop` | Create on startup, drop on shutdown | Testing |

---

## 6. 🔐 Security (Spring Security & JWT)

### 6.1 Spring Security Basics

Spring Security provides authentication, authorization, and protection against common exploits.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

> Adding this starter automatically secures ALL endpoints with HTTP Basic authentication.  
> Default username: `user` | Default password: printed in console at startup.

**Authentication vs Authorization:**

| Concept | Question | Example |
|---------|----------|---------|
| **Authentication** | Who are you? | Login with username/password |
| **Authorization** | What can you do? | Admin vs. User role permissions |

---

### 6.2 Security Configuration (Spring Boot 3.x)

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {

    private final JwtAuthenticationFilter jwtAuthFilter;
    private final UserDetailsService userDetailsService;

    public SecurityConfig(JwtAuthenticationFilter jwtAuthFilter,
                          UserDetailsService userDetailsService) {
        this.jwtAuthFilter = jwtAuthFilter;
        this.userDetailsService = userDetailsService;
    }

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable()) // Disable CSRF for REST APIs
            .sessionManagement(session ->
                session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**").permitAll()
                .requestMatchers("/actuator/health").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .requestMatchers("/api/users/**").hasAnyRole("USER", "ADMIN")
                .anyRequest().authenticated()
            )
            .authenticationProvider(authenticationProvider())
            .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }

    @Bean
    public AuthenticationProvider authenticationProvider() {
        DaoAuthenticationProvider provider = new DaoAuthenticationProvider();
        provider.setUserDetailsService(userDetailsService);
        provider.setPasswordEncoder(passwordEncoder());
        return provider;
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration config) throws Exception {
        return config.getAuthenticationManager();
    }
}
```

---

### 6.3 JWT (JSON Web Token) Implementation

**Dependencies:**
```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.12.3</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.12.3</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.12.3</version>
    <scope>runtime</scope>
</dependency>
```

**JWT Service:**
```java
@Service
public class JwtService {

    @Value("${jwt.secret}")
    private String secretKey;

    @Value("${jwt.expiration}")
    private long jwtExpiration; // in milliseconds

    public String generateToken(UserDetails userDetails) {
        return generateToken(new HashMap<>(), userDetails);
    }

    public String generateToken(Map<String, Object> extraClaims, UserDetails userDetails) {
        return Jwts.builder()
                .claims(extraClaims)
                .subject(userDetails.getUsername())
                .issuedAt(new Date())
                .expiration(new Date(System.currentTimeMillis() + jwtExpiration))
                .signWith(getSigningKey())
                .compact();
    }

    public String extractUsername(String token) {
        return extractClaim(token, Claims::getSubject);
    }

    public <T> T extractClaim(String token, Function<Claims, T> claimsResolver) {
        final Claims claims = extractAllClaims(token);
        return claimsResolver.apply(claims);
    }

    public boolean isTokenValid(String token, UserDetails userDetails) {
        final String username = extractUsername(token);
        return (username.equals(userDetails.getUsername())) && !isTokenExpired(token);
    }

    private boolean isTokenExpired(String token) {
        return extractClaim(token, Claims::getExpiration).before(new Date());
    }

    private Claims extractAllClaims(String token) {
        return Jwts.parser()
                .verifyWith(getSigningKey())
                .build()
                .parseSignedClaims(token)
                .getPayload();
    }

    private SecretKey getSigningKey() {
        byte[] keyBytes = Decoders.BASE64.decode(secretKey);
        return Keys.hmacShaKeyFor(keyBytes);
    }
}
```

**JWT Authentication Filter:**
```java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    private final JwtService jwtService;
    private final UserDetailsService userDetailsService;

    public JwtAuthenticationFilter(JwtService jwtService, UserDetailsService userDetailsService) {
        this.jwtService = jwtService;
        this.userDetailsService = userDetailsService;
    }

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain)
            throws ServletException, IOException {

        final String authHeader = request.getHeader("Authorization");

        if (authHeader == null || !authHeader.startsWith("Bearer ")) {
            filterChain.doFilter(request, response);
            return;
        }

        final String jwt = authHeader.substring(7);
        final String username = jwtService.extractUsername(jwt);

        if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {
            UserDetails userDetails = userDetailsService.loadUserByUsername(username);

            if (jwtService.isTokenValid(jwt, userDetails)) {
                UsernamePasswordAuthenticationToken authToken =
                        new UsernamePasswordAuthenticationToken(
                                userDetails, null, userDetails.getAuthorities());
                authToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                SecurityContextHolder.getContext().setAuthentication(authToken);
            }
        }
        filterChain.doFilter(request, response);
    }
}
```

---

### 6.4 Authentication Controller

```java
@RestController
@RequestMapping("/api/auth")
public class AuthController {

    private final AuthenticationManager authenticationManager;
    private final JwtService jwtService;
    private final UserDetailsService userDetailsService;
    private final UserService userService;

    public AuthController(AuthenticationManager authenticationManager,
                          JwtService jwtService,
                          UserDetailsService userDetailsService,
                          UserService userService) {
        this.authenticationManager = authenticationManager;
        this.jwtService = jwtService;
        this.userDetailsService = userDetailsService;
        this.userService = userService;
    }

    @PostMapping("/register")
    public ResponseEntity<AuthResponse> register(@Valid @RequestBody RegisterRequest request) {
        userService.registerUser(request);
        UserDetails userDetails = userDetailsService.loadUserByUsername(request.getEmail());
        String token = jwtService.generateToken(userDetails);
        return ResponseEntity.ok(new AuthResponse(token));
    }

    @PostMapping("/login")
    public ResponseEntity<AuthResponse> login(@Valid @RequestBody LoginRequest request) {
        authenticationManager.authenticate(
                new UsernamePasswordAuthenticationToken(request.getEmail(), request.getPassword()));

        UserDetails userDetails = userDetailsService.loadUserByUsername(request.getEmail());
        String token = jwtService.generateToken(userDetails);
        return ResponseEntity.ok(new AuthResponse(token));
    }
}

// DTOs
public record LoginRequest(
    @NotBlank String email,
    @NotBlank String password
) {}

public record RegisterRequest(
    @NotBlank String name,
    @Email String email,
    @NotBlank @Size(min = 8) String password
) {}

public record AuthResponse(String token) {}
```

---

### 6.5 Role-Based Access Control

```java
// Method-level security
@Service
public class AdminService {

    @PreAuthorize("hasRole('ADMIN')")
    public void deleteAllUsers() { /* ... */ }

    @PreAuthorize("hasRole('ADMIN') or #userId == authentication.principal.id")
    public UserResponseDTO getUserDetails(Long userId) { /* ... */ }

    @PreAuthorize("hasAnyRole('ADMIN', 'MANAGER')")
    public List<Report> getReports() { /* ... */ }

    @PostAuthorize("returnObject.email == authentication.name")
    public User getCurrentUser() { /* ... */ }
}
```

**Security Best Practices:**

| Practice | Details |
|----------|---------|
| Never store plain-text passwords | Always use `BCryptPasswordEncoder` |
| Use HTTPS in production | Configure SSL/TLS |
| Validate JWT on every request | Use filter chain |
| Use short-lived tokens | Access tokens: 15–30 minutes |
| Implement refresh tokens | For re-authentication without password |
| Rate limiting | Prevent brute-force attacks |
| CORS configuration | Restrict allowed origins |

---

## 7. 🧪 Testing

### 7.1 Unit Testing with JUnit 5

```java
class UserServiceTest {

    private UserService userService;
    private UserRepository userRepository;

    @BeforeEach
    void setUp() {
        userRepository = mock(UserRepository.class);
        userService = new UserServiceImpl(userRepository);
    }

    @Test
    @DisplayName("Should return user when valid ID provided")
    void getUserById_ValidId_ReturnsUser() {
        // Arrange
        User user = new User();
        user.setId(1L);
        user.setName("Alice");
        user.setEmail("alice@example.com");
        when(userRepository.findById(1L)).thenReturn(Optional.of(user));

        // Act
        UserResponseDTO result = userService.getUserById(1L);

        // Assert
        assertNotNull(result);
        assertEquals("Alice", result.getName());
        assertEquals("alice@example.com", result.getEmail());
        verify(userRepository, times(1)).findById(1L);
    }

    @Test
    @DisplayName("Should throw exception when user not found")
    void getUserById_InvalidId_ThrowsException() {
        // Arrange
        when(userRepository.findById(99L)).thenReturn(Optional.empty());

        // Act & Assert
        assertThrows(ResourceNotFoundException.class, () -> userService.getUserById(99L));
        verify(userRepository).findById(99L);
    }

    @Test
    @DisplayName("Should create user successfully")
    void createUser_ValidRequest_ReturnsCreatedUser() {
        // Arrange
        UserRequestDTO request = new UserRequestDTO("Bob", "bob@example.com", 25);
        User savedUser = new User();
        savedUser.setId(1L);
        savedUser.setName("Bob");
        savedUser.setEmail("bob@example.com");
        savedUser.setAge(25);

        when(userRepository.existsByEmail("bob@example.com")).thenReturn(false);
        when(userRepository.save(any(User.class))).thenReturn(savedUser);

        // Act
        UserResponseDTO result = userService.createUser(request);

        // Assert
        assertNotNull(result);
        assertEquals(1L, result.getId());
        assertEquals("Bob", result.getName());
        verify(userRepository).save(any(User.class));
    }
}
```

---

### 7.2 Mockito Deep Dive

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

    @Mock
    private OrderRepository orderRepository;

    @Mock
    private PaymentService paymentService;

    @Mock
    private NotificationService notificationService;

    @InjectMocks
    private OrderServiceImpl orderService;

    @Captor
    private ArgumentCaptor<Order> orderCaptor;

    @Test
    void processOrder_Success() {
        // Stubbing
        when(paymentService.processPayment(any())).thenReturn(true);
        when(orderRepository.save(any(Order.class))).thenAnswer(invocation -> {
            Order order = invocation.getArgument(0);
            order.setId(1L);
            return order;
        });

        // Act
        orderService.processOrder(new OrderRequest("PROD-1", 2, BigDecimal.TEN));

        // Verify interactions
        verify(paymentService).processPayment(any());
        verify(orderRepository).save(orderCaptor.capture());
        verify(notificationService).sendOrderConfirmation(anyLong());

        // Assert captured value
        Order capturedOrder = orderCaptor.getValue();
        assertEquals("PROD-1", capturedOrder.getProductId());
        assertEquals(2, capturedOrder.getQuantity());
    }

    @Test
    void processOrder_PaymentFailed_ThrowsException() {
        when(paymentService.processPayment(any())).thenReturn(false);

        assertThrows(PaymentFailedException.class, () ->
                orderService.processOrder(new OrderRequest("PROD-1", 2, BigDecimal.TEN)));

        verify(orderRepository, never()).save(any()); // Order should NOT be saved
        verify(notificationService, never()).sendOrderConfirmation(anyLong());
    }
}
```

---

### 7.3 @SpringBootTest (Integration Testing)

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@AutoConfigureMockMvc
@ActiveProfiles("test")
class UserIntegrationTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;

    @Autowired
    private UserRepository userRepository;

    @BeforeEach
    void setUp() {
        userRepository.deleteAll();
    }

    @Test
    void createUser_ValidRequest_Returns201() throws Exception {
        UserRequestDTO request = new UserRequestDTO("Alice", "alice@example.com", 30);

        mockMvc.perform(post("/api/v1/users")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(request)))
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.name").value("Alice"))
                .andExpect(jsonPath("$.email").value("alice@example.com"))
                .andExpect(jsonPath("$.id").isNotEmpty());

        assertEquals(1, userRepository.count());
    }

    @Test
    void createUser_DuplicateEmail_Returns409() throws Exception {
        // Pre-create user
        User existing = new User();
        existing.setName("Existing");
        existing.setEmail("alice@example.com");
        existing.setAge(25);
        userRepository.save(existing);

        UserRequestDTO request = new UserRequestDTO("Alice", "alice@example.com", 30);

        mockMvc.perform(post("/api/v1/users")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(request)))
                .andExpect(status().isConflict());
    }

    @Test
    void getUserById_NotFound_Returns404() throws Exception {
        mockMvc.perform(get("/api/v1/users/999"))
                .andExpect(status().isNotFound())
                .andExpect(jsonPath("$.message").value("User not found with id: 999"));
    }

    @Test
    void getAllUsers_WithPagination_ReturnsPagedResult() throws Exception {
        // Create 15 users
        for (int i = 1; i <= 15; i++) {
            User user = new User();
            user.setName("User " + i);
            user.setEmail("user" + i + "@example.com");
            user.setAge(20 + i);
            userRepository.save(user);
        }

        mockMvc.perform(get("/api/v1/users")
                        .param("page", "0")
                        .param("size", "5"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.content.length()").value(5))
                .andExpect(jsonPath("$.totalElements").value(15))
                .andExpect(jsonPath("$.totalPages").value(3));
    }
}
```

---

### 7.4 MockMvc Detailed Examples

```java
@WebMvcTest(UserController.class)
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private UserService userService;

    @Autowired
    private ObjectMapper objectMapper;

    @Test
    void getUser_ReturnsUser() throws Exception {
        UserResponseDTO user = new UserResponseDTO(1L, "Alice", "alice@example.com", 30, LocalDateTime.now());
        when(userService.getUserById(1L)).thenReturn(user);

        mockMvc.perform(get("/api/v1/users/{id}", 1L)
                        .accept(MediaType.APPLICATION_JSON))
                .andDo(print()) // Print request/response for debugging
                .andExpect(status().isOk())
                .andExpect(content().contentType(MediaType.APPLICATION_JSON))
                .andExpect(jsonPath("$.id").value(1))
                .andExpect(jsonPath("$.name").value("Alice"))
                .andExpect(jsonPath("$.email").value("alice@example.com"));
    }

    @Test
    void createUser_InvalidRequest_Returns400() throws Exception {
        UserRequestDTO request = new UserRequestDTO("", "invalid-email", null);

        mockMvc.perform(post("/api/v1/users")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(request)))
                .andExpect(status().isBadRequest())
                .andExpect(jsonPath("$.fieldErrors.name").exists())
                .andExpect(jsonPath("$.fieldErrors.email").exists())
                .andExpect(jsonPath("$.fieldErrors.age").exists());
    }
}
```

---

### 7.5 Testcontainers

Testcontainers provides lightweight, disposable Docker containers for integration tests.

```xml
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>junit-jupiter</artifactId>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>mysql</artifactId>
    <scope>test</scope>
</dependency>
```

```java
@SpringBootTest
@Testcontainers
@ActiveProfiles("test")
class UserRepositoryIntegrationTest {

    @Container
    static MySQLContainer<?> mysql = new MySQLContainer<>("mysql:8.0")
            .withDatabaseName("testdb")
            .withUsername("test")
            .withPassword("test");

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", mysql::getJdbcUrl);
        registry.add("spring.datasource.username", mysql::getUsername);
        registry.add("spring.datasource.password", mysql::getPassword);
    }

    @Autowired
    private UserRepository userRepository;

    @Test
    void findByEmail_ReturnsUser() {
        User user = new User();
        user.setName("Alice");
        user.setEmail("alice@example.com");
        user.setAge(30);
        userRepository.save(user);

        Optional<User> found = userRepository.findByEmail("alice@example.com");

        assertTrue(found.isPresent());
        assertEquals("Alice", found.get().getName());
    }
}
```

---

## 8. ☁️ Microservices Concepts

### 8.1 Monolith vs Microservices

| Aspect | Monolith | Microservices |
|--------|----------|---------------|
| **Deployment** | Single unit | Independent services |
| **Scaling** | Scale entire app | Scale individual services |
| **Technology** | One stack | Polyglot (different tech per service) |
| **Team Structure** | One team | Team per service |
| **Complexity** | Simple initially | Complex (networking, data consistency) |
| **Data** | Shared database | Database per service |
| **Failure** | Single point of failure | Fault isolation |
| **Communication** | Method calls | HTTP/REST, Messaging, gRPC |

---

### 8.2 Microservices Architecture Diagram

```
┌──────────────────────────────────────────────────────────────────────┐
│                          CLIENT (Browser/App)                       │
└─────────────────────────────────┬────────────────────────────────────┘
                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────────────┐
│                         API GATEWAY (Spring Cloud Gateway)          │
│                    - Routing, Load Balancing, Rate Limiting         │
│                    - Authentication (JWT Validation)                │
└─────────┬──────────────┬──────────────┬──────────────┬──────────────┘
          │              │              │              │
          ▼              ▼              ▼              ▼
    ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────┐
    │  USER    │  │  ORDER   │  │ PRODUCT  │  │ NOTIFICATION │
    │ SERVICE  │  │ SERVICE  │  │ SERVICE  │  │   SERVICE    │
    │ :8081    │  │ :8082    │  │ :8083    │  │   :8084      │
    └────┬─────┘  └────┬─────┘  └────┬─────┘  └──────┬───────┘
         │              │              │               │
         ▼              ▼              ▼               ▼
    ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────┐
    │ User DB  │  │ Order DB │  │Product DB│  │  Email/SMS   │
    │ (MySQL)  │  │ (Postgres)│ │ (MongoDB)│  │   Provider   │
    └──────────┘  └──────────┘  └──────────┘  └──────────────┘

                  ┌─────────────────────────────────┐
                  │     SERVICE DISCOVERY (Eureka)   │
                  │     - Service Registration       │
                  │     - Health Monitoring          │
                  └─────────────────────────────────┘

                  ┌─────────────────────────────────┐
                  │     CONFIG SERVER                │
                  │     - Centralized Configuration  │
                  │     - Git-backed Properties      │
                  └─────────────────────────────────┘

                  ┌─────────────────────────────────┐
                  │     MESSAGE BROKER               │
                  │     - RabbitMQ / Kafka            │
                  │     - Async Communication         │
                  └─────────────────────────────────┘
```

---

### 8.3 Service Discovery (Eureka)

**Eureka Server:**
```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }
}
```

```yaml
# Eureka Server config
server:
  port: 8761
eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
```

**Eureka Client (Microservice):**
```yaml
# Each microservice
spring:
  application:
    name: user-service
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
  instance:
    prefer-ip-address: true
```

---

### 8.4 Feign Client (Service-to-Service Communication)

```java
// In Order Service — calling User Service
@FeignClient(name = "user-service", fallback = UserServiceFallback.class)
public interface UserServiceClient {

    @GetMapping("/api/v1/users/{id}")
    UserResponseDTO getUserById(@PathVariable("id") Long id);

    @GetMapping("/api/v1/users/email/{email}")
    UserResponseDTO getUserByEmail(@PathVariable("email") String email);
}

// Fallback for circuit breaker
@Component
public class UserServiceFallback implements UserServiceClient {

    @Override
    public UserResponseDTO getUserById(Long id) {
        return new UserResponseDTO(id, "Unknown", "N/A", 0, null);
    }

    @Override
    public UserResponseDTO getUserByEmail(String email) {
        return new UserResponseDTO(0L, "Unknown", email, 0, null);
    }
}
```

---

### 8.5 API Gateway (Spring Cloud Gateway)

```yaml
spring:
  application:
    name: api-gateway
  cloud:
    gateway:
      routes:
        - id: user-service
          uri: lb://user-service
          predicates:
            - Path=/api/v1/users/**
          filters:
            - StripPrefix=0

        - id: order-service
          uri: lb://order-service
          predicates:
            - Path=/api/v1/orders/**
          filters:
            - StripPrefix=0
            - name: CircuitBreaker
              args:
                name: orderServiceCB
                fallbackUri: forward:/fallback/orders

      default-filters:
        - name: RequestRateLimiter
          args:
            redis-rate-limiter.replenishRate: 10
            redis-rate-limiter.burstCapacity: 20
```

---

### 8.6 Config Server

**Config Server Application:**
```java
@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }
}
```

```yaml
# Config Server
server:
  port: 8888
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/myorg/config-repo
          default-label: main
```

**Config Client (Microservice):**
```yaml
spring:
  application:
    name: user-service
  config:
    import: configserver:http://localhost:8888
```

---

### 8.7 Circuit Breaker (Resilience4j)

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
</dependency>
```

```java
@Service
public class OrderService {

    private final UserServiceClient userServiceClient;

    @CircuitBreaker(name = "userService", fallbackMethod = "getUserFallback")
    @Retry(name = "userService", fallbackMethod = "getUserFallback")
    @RateLimiter(name = "userService")
    public UserResponseDTO getUser(Long userId) {
        return userServiceClient.getUserById(userId);
    }

    private UserResponseDTO getUserFallback(Long userId, Exception ex) {
        log.warn("Circuit breaker activated for user: {}, error: {}", userId, ex.getMessage());
        return new UserResponseDTO(userId, "Unavailable", "N/A", 0, null);
    }
}
```

```yaml
resilience4j:
  circuitbreaker:
    instances:
      userService:
        sliding-window-size: 10
        failure-rate-threshold: 50
        wait-duration-in-open-state: 10000
        permitted-number-of-calls-in-half-open-state: 3
        register-health-indicator: true
  retry:
    instances:
      userService:
        max-attempts: 3
        wait-duration: 1000
  ratelimiter:
    instances:
      userService:
        limit-for-period: 10
        limit-refresh-period: 1s
```

**Circuit Breaker States:**

```
CLOSED (normal) ──[failure rate exceeds threshold]──→ OPEN (failing)
                                                          │
                                                          │ [wait duration expires]
                                                          ▼
                                                    HALF-OPEN (testing)
                                                          │
                                        ┌─────────────────┴────────────────┐
                                        │                                  │
                                  [requests succeed]                [requests fail]
                                        │                                  │
                                        ▼                                  ▼
                                     CLOSED                              OPEN
```

---

### 8.8 Distributed Logging

```xml
<!-- Sleuth for distributed tracing (Spring Boot 2.x) -->
<!-- For Spring Boot 3.x, use Micrometer Tracing -->
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-tracing-bridge-brave</artifactId>
</dependency>
<dependency>
    <groupId>io.zipkin.reporter2</groupId>
    <artifactId>zipkin-reporter-brave</artifactId>
</dependency>
```

```yaml
management:
  tracing:
    sampling:
      probability: 1.0  # 100% for dev, lower in production
  zipkin:
    tracing:
      endpoint: http://localhost:9411/api/v2/spans

logging:
  pattern:
    level: "%5p [${spring.application.name:},%X{traceId:-},%X{spanId:-}]"
```

> This adds `traceId` and `spanId` to every log line, allowing you to trace a request across multiple services.

---

## 9. ⚡ Advanced Production Concepts

### 9.1 Caching with Redis

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

```yaml
spring:
  data:
    redis:
      host: localhost
      port: 6379
      password: secret
  cache:
    type: redis
    redis:
      time-to-live: 600000  # 10 minutes
      cache-null-values: false
```

```java
@Configuration
@EnableCaching
public class CacheConfig {

    @Bean
    public RedisCacheManager cacheManager(RedisConnectionFactory connectionFactory) {
        RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofMinutes(10))
                .serializeValuesWith(
                        RedisSerializationContext.SerializationPair.fromSerializer(
                                new GenericJackson2JsonRedisSerializer()))
                .disableCachingNullValues();

        return RedisCacheManager.builder(connectionFactory)
                .cacheDefaults(config)
                .withCacheConfiguration("users",
                        config.entryTtl(Duration.ofMinutes(30)))
                .build();
    }
}

@Service
public class UserService {

    @Cacheable(value = "users", key = "#id")
    public UserResponseDTO getUserById(Long id) {
        log.info("Fetching user from database: {}", id);
        return userRepository.findById(id)
                .map(UserResponseDTO::fromEntity)
                .orElseThrow(() -> new ResourceNotFoundException("User not found"));
    }

    @CachePut(value = "users", key = "#result.id")
    public UserResponseDTO updateUser(Long id, UserRequestDTO request) {
        // Update and return — cache is updated with the result
    }

    @CacheEvict(value = "users", key = "#id")
    public void deleteUser(Long id) {
        userRepository.deleteById(id);
    }

    @CacheEvict(value = "users", allEntries = true)
    public void clearCache() {
        log.info("User cache cleared");
    }
}
```

| Annotation | Purpose |
|------------|---------|
| `@Cacheable` | Caches the result; skips method if cached |
| `@CachePut` | Always executes method, updates cache |
| `@CacheEvict` | Removes entry from cache |
| `@Caching` | Combines multiple cache annotations |

---

### 9.2 Scheduling

```java
@Configuration
@EnableScheduling
public class SchedulerConfig {}

@Component
public class ScheduledTasks {

    private static final Logger log = LoggerFactory.getLogger(ScheduledTasks.class);

    // Fixed rate — every 5 minutes
    @Scheduled(fixedRate = 300000)
    public void cleanupExpiredTokens() {
        log.info("Cleaning up expired tokens...");
    }

    // Fixed delay — 1 minute after previous execution completes
    @Scheduled(fixedDelay = 60000, initialDelay = 10000)
    public void syncData() {
        log.info("Syncing data...");
    }

    // Cron expression — every day at 2 AM
    @Scheduled(cron = "0 0 2 * * ?")
    public void dailyReport() {
        log.info("Generating daily report...");
    }

    // Cron — every Monday at 9 AM
    @Scheduled(cron = "0 0 9 ? * MON")
    public void weeklyDigest() {
        log.info("Sending weekly digest...");
    }
}
```

**Cron Expression Format:**

```
┌───────── second (0-59)
│ ┌───────── minute (0-59)
│ │ ┌───────── hour (0-23)
│ │ │ ┌───────── day of month (1-31)
│ │ │ │ ┌───────── month (1-12)
│ │ │ │ │ ┌───────── day of week (0-7, SUN-SAT)
│ │ │ │ │ │
* * * * * *
```

---

### 9.3 Async Processing

```java
@Configuration
@EnableAsync
public class AsyncConfig {

    @Bean(name = "taskExecutor")
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(10);
        executor.setQueueCapacity(25);
        executor.setThreadNamePrefix("Async-");
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        executor.initialize();
        return executor;
    }
}

@Service
public class NotificationService {

    @Async("taskExecutor")
    public CompletableFuture<Void> sendEmailAsync(String to, String subject, String body) {
        log.info("Sending email asynchronously to: {}", to);
        // Email sending logic
        return CompletableFuture.completedFuture(null);
    }

    @Async("taskExecutor")
    public CompletableFuture<ReportData> generateReportAsync(Long reportId) {
        log.info("Generating report: {}", reportId);
        // Long-running report generation
        ReportData data = generateReport(reportId);
        return CompletableFuture.completedFuture(data);
    }
}

// Usage
@Service
public class OrderService {

    private final NotificationService notificationService;

    public void processOrder(OrderRequest request) {
        Order order = saveOrder(request);

        // Fire-and-forget async email
        notificationService.sendEmailAsync(
                request.getEmail(),
                "Order Confirmed",
                "Your order #" + order.getId() + " has been placed."
        );

        // Await async result
        CompletableFuture<ReportData> report = notificationService.generateReportAsync(order.getId());
        report.thenAccept(data -> log.info("Report generated: {}", data));
    }
}
```

> **Interview Note:** `@Async` only works when called from another bean. Self-invocation (calling an async method within the same class) won't work due to proxy-based AOP.

---

### 9.4 AOP (Aspect-Oriented Programming)

```java
@Aspect
@Component
public class LoggingAspect {

    private static final Logger log = LoggerFactory.getLogger(LoggingAspect.class);

    // Log execution time for all service methods
    @Around("execution(* com.example.myapp.service..*(..))")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        String methodName = joinPoint.getSignature().toShortString();

        try {
            Object result = joinPoint.proceed();
            long elapsed = System.currentTimeMillis() - start;
            log.info("{} executed in {}ms", methodName, elapsed);
            return result;
        } catch (Exception e) {
            long elapsed = System.currentTimeMillis() - start;
            log.error("{} failed after {}ms: {}", methodName, elapsed, e.getMessage());
            throw e;
        }
    }

    // Log all controller entries
    @Before("execution(* com.example.myapp.controller..*(..))")
    public void logControllerEntry(JoinPoint joinPoint) {
        log.info("→ {}.{}() called with args: {}",
                joinPoint.getTarget().getClass().getSimpleName(),
                joinPoint.getSignature().getName(),
                Arrays.toString(joinPoint.getArgs()));
    }

    // Log all controller exits
    @AfterReturning(pointcut = "execution(* com.example.myapp.controller..*(..))", returning = "result")
    public void logControllerExit(JoinPoint joinPoint, Object result) {
        log.info("← {}.{}() returned: {}",
                joinPoint.getTarget().getClass().getSimpleName(),
                joinPoint.getSignature().getName(),
                result);
    }
}
```

| Advice Type | When It Runs |
|-------------|-------------|
| `@Before` | Before method execution |
| `@After` | After method execution (always) |
| `@AfterReturning` | After successful return |
| `@AfterThrowing` | After exception thrown |
| `@Around` | Wraps the method entirely |

---

### 9.5 Custom Spring Boot Starter

Creating a reusable auto-configuration module:

```java
// AutoConfiguration class
@AutoConfiguration
@ConditionalOnClass(NotificationService.class)
@EnableConfigurationProperties(NotificationProperties.class)
public class NotificationAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean
    public NotificationService notificationService(NotificationProperties properties) {
        return new NotificationService(properties.getApiKey(), properties.getFrom());
    }
}

// Configuration properties
@ConfigurationProperties(prefix = "app.notification")
public class NotificationProperties {
    private String apiKey;
    private String from;
    // Getters and setters
}

// Register in META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
// com.example.notification.NotificationAutoConfiguration
```

---

### 9.6 Dockerizing Spring Boot

**Dockerfile:**
```dockerfile
# Multi-stage build
FROM eclipse-temurin:17-jdk-alpine AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
COPY .mvn ./.mvn
COPY mvnw .
RUN chmod +x mvnw && ./mvnw clean package -DskipTests

FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
RUN addgroup -S spring && adduser -S spring -G spring
COPY --from=build /app/target/*.jar app.jar
USER spring:spring
EXPOSE 8080
HEALTHCHECK --interval=30s --timeout=3s \
  CMD wget --quiet --tries=1 --spider http://localhost:8080/actuator/health || exit 1
ENTRYPOINT ["java", "-jar", "app.jar"]
```

**docker-compose.yml:**
```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=prod
      - SPRING_DATASOURCE_URL=jdbc:mysql://db:3306/mydb
      - SPRING_DATASOURCE_USERNAME=root
      - SPRING_DATASOURCE_PASSWORD=secret
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: mydb
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  mysql_data:
```

---

### 9.7 Performance Tuning

**JVM Tuning:**
```bash
java -jar app.jar \
  -Xms512m \          # Initial heap size
  -Xmx1024m \         # Maximum heap size
  -XX:+UseG1GC \      # G1 Garbage Collector
  -XX:MaxGCPauseMillis=200 \
  -XX:+HeapDumpOnOutOfMemoryError \
  -XX:HeapDumpPath=/tmp/heapdump.hprof
```

**HikariCP Connection Pool Tuning:**
```yaml
spring:
  datasource:
    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
      idle-timeout: 300000        # 5 minutes
      max-lifetime: 1800000       # 30 minutes
      connection-timeout: 20000    # 20 seconds
      leak-detection-threshold: 60000  # 1 minute
```

**Performance Checklist:**

| Area | Recommendation |
|------|---------------|
| Database | Use connection pooling (HikariCP), optimize queries, add indexes |
| Caching | Cache frequently accessed, rarely changed data |
| Lazy Loading | Use `FetchType.LAZY` for relationships |
| Pagination | Always paginate large result sets |
| Async | Offload long-running tasks to async threads |
| Compression | Enable GZIP compression for responses |
| JVM | Tune heap size, GC algorithm for your workload |
| Monitoring | Use Actuator + Micrometer + Prometheus/Grafana |

**Enable Response Compression:**
```yaml
server:
  compression:
    enabled: true
    mime-types: application/json,application/xml,text/html,text/plain
    min-response-size: 1024
```

---

### 9.8 Kubernetes Basics

**Deployment manifest:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: user-service
  labels:
    app: user-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: user-service
  template:
    metadata:
      labels:
        app: user-service
    spec:
      containers:
        - name: user-service
          image: myregistry/user-service:1.0.0
          ports:
            - containerPort: 8080
          env:
            - name: SPRING_PROFILES_ACTIVE
              value: "prod"
            - name: SPRING_DATASOURCE_URL
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: url
          resources:
            requests:
              memory: "256Mi"
              cpu: "250m"
            limits:
              memory: "512Mi"
              cpu: "500m"
          livenessProbe:
            httpGet:
              path: /actuator/health/liveness
              port: 8080
            initialDelaySeconds: 30
            periodSeconds: 10
          readinessProbe:
            httpGet:
              path: /actuator/health/readiness
              port: 8080
            initialDelaySeconds: 20
            periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: user-service
spec:
  selector:
    app: user-service
  ports:
    - port: 80
      targetPort: 8080
  type: ClusterIP
```

---

### 9.9 CI/CD Overview

```yaml
# GitHub Actions — .github/workflows/ci.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    services:
      mysql:
        image: mysql:8.0
        env:
          MYSQL_ROOT_PASSWORD: test
          MYSQL_DATABASE: testdb
        ports:
          - 3306:3306

    steps:
      - uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: maven

      - name: Build and Test
        run: mvn clean verify

      - name: Build Docker Image
        if: github.ref == 'refs/heads/main'
        run: docker build -t myregistry/myapp:${{ github.sha }} .

      - name: Push to Registry
        if: github.ref == 'refs/heads/main'
        run: |
          echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin
          docker push myregistry/myapp:${{ github.sha }}
```

---

## 10. 🎯 Interview Preparation

### 10.1 Common Interview Questions

#### Spring Core

| # | Question | Key Points |
|---|----------|------------|
| 1 | What is IoC and DI? | IoC = principle (control inversion), DI = pattern (inject dependencies). Spring uses DI to achieve IoC. |
| 2 | Difference between `@Component`, `@Service`, `@Repository`? | All are stereotypes of `@Component`. `@Repository` adds exception translation. `@Service` marks business logic. |
| 3 | Bean scopes in Spring? | singleton (default), prototype, request, session, application |
| 4 | Constructor vs Field injection? | Constructor: explicit, immutable, testable. Field: hidden deps, needs reflection for testing. |
| 5 | What is `@Qualifier`? | Disambiguates when multiple beans of same type exist. |
| 6 | Difference between `@Bean` and `@Component`? | `@Bean` = method-level in `@Configuration`, `@Component` = class-level auto-detected. |
| 7 | What is `@Primary`? | Marks a bean as default when multiple candidates exist. |

#### Spring Boot

| # | Question | Key Points |
|---|----------|------------|
| 1 | What is auto-configuration? | Spring Boot automatically configures beans based on classpath + properties. |
| 2 | What does `@SpringBootApplication` do? | `@Configuration` + `@EnableAutoConfiguration` + `@ComponentScan`. |
| 3 | How to disable a specific auto-config? | `@SpringBootApplication(exclude = {DataSourceAutoConfig.class})` or `spring.autoconfigure.exclude`. |
| 4 | Properties vs YAML? | Properties = flat, YAML = hierarchical. YAML supports multi-doc. |
| 5 | How does Spring Boot find the main class? | `MANIFEST.MF` has `Main-Class` pointing to `JarLauncher`, which finds `@SpringBootApplication`. |
| 6 | What is the Actuator? | Production monitoring: health, metrics, info, env endpoints. |

#### REST API

| # | Question | Key Points |
|---|----------|------------|
| 1 | `@Controller` vs `@RestController`? | `@RestController` = `@Controller` + `@ResponseBody` (returns data, not views). |
| 2 | `@RequestBody` vs `@RequestParam`? | `@RequestBody` = JSON body, `@RequestParam` = URL query params. |
| 3 | How to handle exceptions globally? | `@RestControllerAdvice` + `@ExceptionHandler` methods. |
| 4 | HTTP status code best practices? | 200 OK, 201 Created, 204 No Content, 400 Bad Request, 404 Not Found, 500 Internal Error. |
| 5 | What is `ResponseEntity`? | Wraps response with status code, headers, and body. |

#### JPA & Database

| # | Question | Key Points |
|---|----------|------------|
| 1 | What is N+1 problem? | Loading a list fires 1 query for list + N queries for related entities. Fix: `JOIN FETCH`. |
| 2 | `FetchType.LAZY` vs `EAGER`? | LAZY = load on access. EAGER = load immediately. Default: `@ManyToOne` = EAGER, `@OneToMany` = LAZY. |
| 3 | `save()` vs `saveAndFlush()`? | `save()` queues for batch. `saveAndFlush()` immediately writes to DB. |
| 4 | JPQL vs Native query? | JPQL = entity-based, portable. Native = SQL-based, database-specific. |
| 5 | `@Transactional` on private method? | Won't work — proxy cannot intercept private methods. |

#### Security

| # | Question | Key Points |
|---|----------|------------|
| 1 | How does JWT work? | Client sends credentials → Server returns signed token → Client sends token in `Authorization: Bearer <token>` header. |
| 2 | Password storage best practice? | BCrypt hashing. Never store plain text. |
| 3 | CSRF for REST APIs? | Typically disabled for stateless REST APIs using JWT. |
| 4 | `@PreAuthorize` vs `@Secured`? | `@PreAuthorize` supports SpEL expressions, more powerful. |

---

### 10.2 Scenario-Based Questions

**Scenario 1: Designing a high-traffic e-commerce checkout**
```
Q: How would you design a checkout service that handles 10,000 orders/minute?

Answer framework:
1. Stateless services behind load balancer
2. Async order processing with message queue (Kafka/RabbitMQ)
3. Redis caching for product/inventory data
4. Database read replicas for read-heavy queries
5. Circuit breaker for payment gateway calls
6. Idempotency keys to prevent duplicate orders
7. Distributed locking (Redis/Redisson) for inventory deductions
8. Event-driven architecture for order status updates
```

**Scenario 2: Debugging slow API response**
```
Q: A REST endpoint takes 5 seconds. How do you diagnose?

Answer framework:
1. Check logs for the request trace
2. Enable SQL logging to find slow queries
3. Use Actuator metrics (/actuator/metrics/http.server.requests)
4. Profile with JVisualVM or async-profiler
5. Check for N+1 queries (add JOIN FETCH)
6. Verify connection pool isn't exhausted
7. Check external service call latency (add timeouts)
8. Consider caching if data is read-heavy
```

**Scenario 3: Handling distributed transactions**
```
Q: Order Service and Payment Service need to be consistent.

Answer framework:
1. Saga Pattern — choreography (events) or orchestration (central coordinator)
2. Outbox Pattern — store events in DB table, publish reliably
3. Eventual consistency — accept that services sync asynchronously
4. Compensating transactions — undo if later steps fail
5. AVOID distributed 2-phase commit in microservices
```

---

### 10.3 Architecture Discussion Topics

| Topic | Key Points to Discuss |
|-------|----------------------|
| **SOLID Principles** | Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion |
| **12-Factor App** | Codebase, Dependencies, Config, Backing services, Build/Release/Run, Processes, Port binding, Concurrency, Disposability, Dev/Prod parity, Logs, Admin processes |
| **CAP Theorem** | Consistency, Availability, Partition tolerance — pick two in distributed systems |
| **Event-Driven Architecture** | Events, Event sourcing, CQRS, Message brokers |
| **Database Strategies** | Sharding, Replication, Read replicas, Connection pooling |

---

### 10.4 Debugging Questions

| Scenario | Investigation Steps |
|----------|-------------------|
| `OutOfMemoryError` | Heap dump analysis, check for memory leaks, increase `-Xmx`, use profiler |
| `StackOverflowError` | Check for infinite recursion, circular dependencies |
| Bean creation failure | Check component scanning, missing dependencies, profile activation |
| 404 on endpoint | Verify `@RequestMapping`, component scan package, context path |
| `LazyInitializationException` | Entity accessed outside transaction. Use `@Transactional` or DTO projection. |
| Connection pool exhaustion | Check `maximum-pool-size`, look for unclosed connections, add leak detection |

---

## 11. 📐 Best Practices & Project Structure

### 11.1 Clean Architecture

```
┌────────────────────────────────────────────────────┐
│                  PRESENTATION LAYER                │
│              (Controllers, DTOs, Views)            │
│                                                    │
│  ┌──────────────────────────────────────────────┐  │
│  │              APPLICATION LAYER               │  │
│  │          (Services, Use Cases, Ports)         │  │
│  │                                              │  │
│  │  ┌────────────────────────────────────────┐  │  │
│  │  │           DOMAIN LAYER                 │  │  │
│  │  │     (Entities, Value Objects,          │  │  │
│  │  │      Domain Services, Interfaces)      │  │  │
│  │  └────────────────────────────────────────┘  │  │
│  │                                              │  │
│  └──────────────────────────────────────────────┘  │
│                                                    │
└────────────────────────────────────────────────────┘
                         │
              ┌──────────┴──────────┐
              │  INFRASTRUCTURE     │
              │  (DB, HTTP, MQ,     │
              │   External APIs)    │
              └─────────────────────┘
```

**Dependency Rule:** Inner layers NEVER depend on outer layers. Dependencies always point inward.

---

### 11.2 Hexagonal Architecture (Ports & Adapters)

```
                        ┌──────────────────┐
                        │   REST Adapter    │
                        │  (Controller)     │
                        └────────┬─────────┘
                                 │
                                 ▼
                    ┌────────────────────────┐
     ┌─────────┐   │      INPUT PORT        │   ┌─────────────┐
     │ Message  │──→│    (Service Interface) │   │  Scheduler   │
     │ Adapter  │   │                        │   │  Adapter     │
     └─────────┘   │  ┌──────────────────┐  │   └──────┬──────┘
                    │  │     DOMAIN       │  │          │
                    │  │  (Entities,      │  │←─────────┘
                    │  │   Business Logic)│  │
                    │  └──────────────────┘  │
                    │                        │
                    │     OUTPUT PORT        │
                    │  (Repository Interface)│
                    └───────────┬────────────┘
                                │
               ┌────────────────┼────────────────┐
               ▼                ▼                ▼
        ┌────────────┐  ┌────────────┐  ┌────────────┐
        │ JPA Adapter│  │Redis Adapter│  │ HTTP Adapter│
        │ (MySQL)    │  │ (Cache)    │  │ (External) │
        └────────────┘  └────────────┘  └────────────┘
```

---

### 11.3 Enterprise Project Structure

```
com.example.myapp/
├── MyAppApplication.java
│
├── config/                          # Configuration
│   ├── SecurityConfig.java
│   ├── CacheConfig.java
│   ├── AsyncConfig.java
│   └── OpenApiConfig.java
│
├── controller/                      # REST Controllers
│   ├── UserController.java
│   ├── OrderController.java
│   └── AuthController.java
│
├── service/                         # Business Logic
│   ├── UserService.java
│   ├── OrderService.java
│   └── impl/
│       ├── UserServiceImpl.java
│       └── OrderServiceImpl.java
│
├── repository/                      # Data Access
│   ├── UserRepository.java
│   └── OrderRepository.java
│
├── model/                           # Data Models
│   ├── entity/
│   │   ├── User.java
│   │   ├── Order.java
│   │   └── BaseEntity.java
│   ├── dto/
│   │   ├── request/
│   │   │   ├── UserRequestDTO.java
│   │   │   └── OrderRequestDTO.java
│   │   └── response/
│   │       ├── UserResponseDTO.java
│   │       └── OrderResponseDTO.java
│   └── enums/
│       ├── UserStatus.java
│       └── OrderStatus.java
│
├── exception/                       # Exception Handling
│   ├── GlobalExceptionHandler.java
│   ├── ErrorResponse.java
│   ├── ResourceNotFoundException.java
│   ├── DuplicateResourceException.java
│   └── BusinessException.java
│
├── security/                        # Security
│   ├── JwtService.java
│   ├── JwtAuthenticationFilter.java
│   └── CustomUserDetailsService.java
│
├── mapper/                          # Object Mapping
│   ├── UserMapper.java
│   └── OrderMapper.java
│
├── aspect/                          # AOP
│   ├── LoggingAspect.java
│   └── PerformanceAspect.java
│
├── event/                           # Domain Events
│   ├── UserCreatedEvent.java
│   └── OrderPlacedEvent.java
│
├── scheduler/                       # Scheduled Tasks
│   └── CleanupScheduler.java
│
└── util/                            # Utilities
    ├── DateUtils.java
    └── Constants.java
```

---

### 11.4 Naming Conventions

| Type | Convention | Example |
|------|-----------|---------|
| Class | PascalCase | `UserService`, `OrderController` |
| Method | camelCase, verb-first | `getUserById()`, `createOrder()` |
| Variable | camelCase | `userName`, `orderCount` |
| Constant | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT`, `DEFAULT_PAGE_SIZE` |
| Package | lowercase | `com.example.myapp.service` |
| REST endpoint | lowercase, kebab-case | `/api/v1/user-profiles` |
| Database table | snake_case, plural | `user_profiles`, `order_items` |
| Database column | snake_case | `first_name`, `created_at` |
| DTO | Suffixed | `UserRequestDTO`, `UserResponseDTO` |
| Entity | Singular noun | `User`, `Order`, `Product` |
| Repository | Entity + Repository | `UserRepository` |
| Service | Domain + Service | `UserService`, `PaymentService` |
| Controller | Domain + Controller | `UserController` |

---

### 11.5 Logging Standards

| Level | Use Case | Example |
|-------|----------|---------|
| `ERROR` | Failures requiring attention | `log.error("Payment failed for order: {}", orderId, exception)` |
| `WARN` | Recoverable issues | `log.warn("Retry attempt {} for service call", retryCount)` |
| `INFO` | Key business events | `log.info("Order {} placed by user {}", orderId, userId)` |
| `DEBUG` | Development diagnostics | `log.debug("Query returned {} results", results.size())` |

**Do's and Don'ts:**

```java
// ✅ DO: Use parameterized logging
log.info("Processing order: {}", orderId);

// ❌ DON'T: Use string concatenation
log.info("Processing order: " + orderId); // Wasteful if level is above INFO

// ✅ DO: Log exceptions properly
log.error("Failed to process payment for order: {}", orderId, exception);

// ❌ DON'T: Swallow exceptions silently
try { /* ... */ } catch (Exception e) { } // NEVER do this

// ✅ DO: Log at method boundaries for services
log.info("→ createUser(email={})", email);
log.info("← createUser completed, userId={}", user.getId());

// ❌ DON'T: Log sensitive data
log.info("User login: password={}", password); // NEVER log passwords, tokens, etc.
```

---

### 11.6 Exception Handling Standards

```java
// Base business exception
public abstract class BusinessException extends RuntimeException {
    private final String errorCode;
    private final HttpStatus status;

    protected BusinessException(String message, String errorCode, HttpStatus status) {
        super(message);
        this.errorCode = errorCode;
        this.status = status;
    }

    public String getErrorCode() { return errorCode; }
    public HttpStatus getStatus() { return status; }
}

// Specific exceptions
public class ResourceNotFoundException extends BusinessException {
    public ResourceNotFoundException(String resource, String field, Object value) {
        super(String.format("%s not found with %s: '%s'", resource, field, value),
              "RESOURCE_NOT_FOUND", HttpStatus.NOT_FOUND);
    }
}

public class DuplicateResourceException extends BusinessException {
    public DuplicateResourceException(String resource, String field, Object value) {
        super(String.format("%s already exists with %s: '%s'", resource, field, value),
              "DUPLICATE_RESOURCE", HttpStatus.CONFLICT);
    }
}

public class InvalidOperationException extends BusinessException {
    public InvalidOperationException(String message) {
        super(message, "INVALID_OPERATION", HttpStatus.BAD_REQUEST);
    }
}

// Standardized error response
public class ApiError {
    private LocalDateTime timestamp;
    private int status;
    private String errorCode;
    private String message;
    private String path;
    private Map<String, String> validationErrors;

    // Constructor, getters, builder pattern
}

// Global handler
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(BusinessException.class)
    public ResponseEntity<ApiError> handleBusinessException(BusinessException ex, HttpServletRequest request) {
        ApiError error = ApiError.builder()
                .timestamp(LocalDateTime.now())
                .status(ex.getStatus().value())
                .errorCode(ex.getErrorCode())
                .message(ex.getMessage())
                .path(request.getRequestURI())
                .build();
        return ResponseEntity.status(ex.getStatus()).body(error);
    }
}
```

**Exception Handling Rules:**

| Rule | Details |
|------|---------|
| Use unchecked exceptions | Extend `RuntimeException` for business exceptions |
| Be specific | Create domain-specific exceptions, not generic ones |
| Include context | Error messages should help identify the problem |
| Centralize handling | Use `@RestControllerAdvice` for consistent responses |
| Never swallow | Always log or re-throw exceptions |
| Fail fast | Validate inputs early, throw immediately |
| Standardize response | Use a consistent `ApiError` format across all APIs |

---

## 📝 Summary & Learning Path

| Phase | Duration | Topics |
|-------|----------|--------|
| **Phase 1: Foundation** | Weeks 1–3 | Java prerequisites, Spring Core, DI, Bean lifecycle |
| **Phase 2: Spring Boot** | Weeks 4–6 | Auto-config, starters, properties, DevTools, Actuator |
| **Phase 3: REST APIs** | Weeks 7–9 | Controllers, validation, DTOs, exception handling, logging |
| **Phase 4: Data Layer** | Weeks 10–12 | JPA entities, repositories, queries, pagination, transactions |
| **Phase 5: Security** | Weeks 13–15 | Spring Security, JWT, roles, method security |
| **Phase 6: Testing** | Weeks 16–18 | JUnit 5, Mockito, MockMvc, integration tests, Testcontainers |
| **Phase 7: Microservices** | Weeks 19–22 | Eureka, Feign, Gateway, Config Server, Circuit breaker |
| **Phase 8: Production** | Weeks 23–26 | Caching, async, AOP, Docker, K8s, CI/CD, performance |

---

> **🎓 This guide is designed for serious learners aiming for enterprise-level Spring Boot mastery. Build projects at each phase. Write tests. Read source code. Practice interview questions. Master one concept before moving to the next.**

---

*Generated as an enterprise training manual — structured for 3–6 months of deep, production-quality learning.*
