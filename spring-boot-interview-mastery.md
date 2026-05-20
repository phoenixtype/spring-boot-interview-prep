# Spring Boot Interview Mastery
### A Comprehensive Q&A Guide — Based on JavaTechie's 6-Part Series

> Covers all topics from **Parts 1–6** of the *Spring Boot Interview Mastery* series, supplemented with additional industry-standard questions. Includes clear explanations, simple analogies, and code snippets.

---

## Table of Contents

1. [Part 1 — Core Spring Boot Foundations](#part-1--core-spring-boot-foundations)
2. [Part 2 — Dependency Injection & Bean Management](#part-2--dependency-injection--bean-management)
3. [Part 3 — Bean Scopes & Lifecycle](#part-3--bean-scopes--lifecycle)
4. [Part 4 — Data Access, JPA & Exception Handling](#part-4--data-access-jpa--exception-handling)
5. [Part 5 — Spring Boot Testing & Actuator](#part-5--spring-boot-testing--actuator)
6. [Part 6 — Security, REST API Design & Profiles](#part-6--security-rest-api-design--profiles)
7. [Additional High-Value Interview Questions](#additional-high-value-interview-questions)
8. [Final Quiz](#final-quiz)

---

## Part 1 — Core Spring Boot Foundations


> **🎯 FAANG Interview Tip — Part 1**
> Interviewers at Google, Amazon, and Netflix expect you to explain auto-configuration *mechanistically* — not just "it configures things automatically." Know the conditional annotation chain, the `spring.factories` / `AutoConfiguration.imports` file, and how to debug with `--debug`. Being able to whiteboard the `SpringApplication.run()` lifecycle is a strong differentiator.

```
┌─────────────────────────────────────────────────────────────────┐
│              SPRING BOOT AUTO-CONFIGURATION FLOW                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  @SpringBootApplication                                         │
│  ├── @SpringBootConfiguration  (@Configuration)                 │
│  ├── @EnableAutoConfiguration                                   │
│  │     │                                                        │
│  │     ▼                                                        │
│  │   META-INF/spring/                                           │
│  │   org.springframework.boot.autoconfigure.AutoConfiguration   │
│  │   .imports                                                   │
│  │     │                                                        │
│  │     ▼                                                        │
│  │   Load all AutoConfiguration classes                         │
│  │     │                                                        │
│  │     ▼                                                        │
│  │   Evaluate @Conditional annotations                          │
│  │   ┌──────────────────────────────────────┐                   │
│  │   │ @ConditionalOnClass        ✓/✗       │                   │
│  │   │ @ConditionalOnMissingBean  ✓/✗       │                   │
│  │   │ @ConditionalOnProperty     ✓/✗       │                   │
│  │   └──────────────────────────────────────┘                   │
│  │     │                                                        │
│  │     ▼                                                        │
│  │   Register matching beans in ApplicationContext              │
│  │                                                              │
│  └── @ComponentScan                                             │
│        │                                                        │
│        ▼                                                        │
│      Scan base package + sub-packages for                       │
│      @Component, @Service, @Repository, @Controller             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```



---

### Q1. Why would you choose Spring Boot over the Spring Framework?

**Short answer:** Spring Boot removes the boilerplate configuration burden of Spring so you can focus on writing business logic.

**Analogy:** Traditional Spring is like building a car from scratch — you source every part. Spring Boot is like buying a car that already has the engine, seats, and GPS — you just need to drive.

| Feature | Spring Framework | Spring Boot |
|---|---|---|
| Configuration | Manual XML / Java config | Auto-configuration |
| Embedded server | External (Tomcat/JBoss required) | Built-in (Tomcat/Jetty/Undertow) |
| Deployment descriptor | web.xml required | Not required |
| Starter dependencies | Manual per-version management | `spring-boot-starter-*` |
| Production monitoring | Custom setup | Actuator built-in |

**Key advantages of Spring Boot:**
- Convention over configuration — sensible defaults out of the box
- Embedded servers (Tomcat, Jetty, Undertow) — run as a standalone JAR
- `spring-boot-starter-*` POMs reduce dependency conflicts
- Actuator provides production-ready health & metrics endpoints
- Spring Initializr bootstraps a project in seconds

---

### Q2. What Spring Boot starters have you used? What do they do?

**Analogy:** Starters are like meal kits — instead of going to 10 different stores for ingredients, you get one box with everything pre-measured.

A starter is a pre-packaged set of dependencies for a specific feature. You include one artifact in `pom.xml` and Spring Boot pulls in all transitive dependencies at compatible versions.

**Commonly used starters:**

```xml
<!-- Web / REST APIs -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- JPA / Database -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- Security -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>

<!-- Actuator (monitoring) -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<!-- Testing -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>

<!-- Validation -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

---

### Q3. How do you run a Spring Boot application?

There are four main ways:

**1. From your IDE** — Right-click the main class → Run.

**2. Maven plugin:**
```bash
mvn spring-boot:run
```

**3. As an executable JAR:**
```bash
mvn clean package
java -jar target/my-app-1.0.0.jar
```

**4. Gradle:**
```bash
./gradlew bootRun
```

Spring Boot packages everything — your code, configs, and embedded server — into a single "fat JAR." You do not need an external Tomcat installation.

---

### Q4. What is the purpose of `@SpringBootApplication`?

**Analogy:** It's a power strip — three separate plugs combined into one convenient socket.

`@SpringBootApplication` is a convenience annotation that is equivalent to declaring all three of these:

```java
@Configuration        // This class defines Spring Beans
@EnableAutoConfiguration // Turn on auto-configuration
@ComponentScan        // Scan this package and sub-packages for components
```

**Example:**
```java
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

---

### Q5. Can you use `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan` separately instead of `@SpringBootApplication`?

**Yes**, and the application will work exactly the same way. `@SpringBootApplication` is purely a shortcut.

```java
@Configuration
@EnableAutoConfiguration
@ComponentScan(basePackages = "com.myapp")
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

You might choose to split them up when you want to:
- Exclude certain packages from component scanning
- Apply different scanning rules
- Fine-tune the auto-configuration explicitly

---

### Q6. What is AutoConfiguration in Spring Boot?

**Analogy:** When you plug a USB device into a modern computer, the OS auto-detects it and installs the right driver. You didn't do anything — the system figured it out. AutoConfiguration works the same way.

When Spring Boot starts, it reads `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` (or `spring.factories` in older versions), finds hundreds of auto-configuration classes, and applies only the ones whose `@Conditional` conditions are satisfied.

**Example — how it works internally:**

If `spring-boot-starter-data-jpa` is on the classpath:
- `DataSourceAutoConfiguration` detects `javax.sql.DataSource` on the classpath → configures a default DataSource
- `HibernateJpaAutoConfiguration` detects Hibernate → creates an `EntityManagerFactory`
- You did zero manual configuration

**Conditional annotations that control this:**

```java
@ConditionalOnClass(DataSource.class)      // Only if DataSource class exists
@ConditionalOnMissingBean(DataSource.class) // Only if you haven't defined your own
@ConditionalOnProperty(name = "spring.datasource.url") // Only if property is set
```

---

### Q7. How do you disable a specific auto-configuration class?

Use the `exclude` attribute on `@SpringBootApplication` or `@EnableAutoConfiguration`:

```java
// Option 1 — Using @SpringBootApplication
@SpringBootApplication(exclude = { DataSourceAutoConfiguration.class })
public class MyApplication { ... }

// Option 2 — In application.properties
spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
```

**When would you do this?**
- Your app doesn't have a database but JPA is on the classpath (e.g., pulled in transitively).
- You are providing a completely custom configuration for a component and want Spring Boot to stay out of the way.

---

### Q8. How do you customize the default configuration in Spring Boot?

Three main approaches:

**1. `application.properties` / `application.yml`** — The most common way:
```properties
server.port=9090
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=secret
logging.level.com.myapp=DEBUG
```

**2. Define your own Bean** — Spring Boot's auto-configuration backs off when you define your own:
```java
@Configuration
public class MyDataSourceConfig {
    @Bean
    public DataSource dataSource() {
        // Your custom DataSource replaces the auto-configured one
        HikariDataSource ds = new HikariDataSource();
        ds.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
        return ds;
    }
}
```

**3. `@ConfigurationProperties`** — Bind a whole group of properties to a POJO:
```java
@ConfigurationProperties(prefix = "app")
@Component
public class AppProperties {
    private String name;
    private int maxRetries;
    // getters and setters
}
```
```properties
app.name=MyService
app.max-retries=3
```

---

### Q9. How does `SpringApplication.run()` work internally?

When you call `SpringApplication.run(MyApplication.class, args)`, the following happens step by step:

1. **Creates a `SpringApplication` instance** — detects the application type (SERVLET, REACTIVE, or NONE)
2. **Loads `SpringApplicationRunListeners`** — for event broadcasting
3. **Prepares the environment** — reads `application.properties`, environment variables
4. **Prints the banner** (the ASCII art you see in console)
5. **Creates the `ApplicationContext`** — `AnnotationConfigServletWebServerApplicationContext` for web apps
6. **Refreshes the context** — triggers component scanning, auto-configuration, bean instantiation
7. **Starts the embedded server** (Tomcat, Jetty, etc.)
8. **Calls `CommandLineRunner` and `ApplicationRunner` beans** — any post-startup logic
9. **Broadcasts `ApplicationReadyEvent`** — app is live

---

### Q10. What is `CommandLineRunner`? When do you use it?

**Analogy:** It's the checklist a pilot runs *after* the plane is fully ready but *before* passengers board — startup tasks after everything is initialized.

`CommandLineRunner` is a functional interface with a single `run(String... args)` method. Spring Boot calls it after the application context is fully loaded.

```java
@Component
public class DataSeeder implements CommandLineRunner {

    @Autowired
    private UserRepository userRepository;

    @Override
    public void run(String... args) throws Exception {
        // This runs once at startup
        if (userRepository.count() == 0) {
            userRepository.save(new User("admin", "admin@example.com"));
            System.out.println("Seeded initial admin user");
        }
    }
}
```

**Use cases:**
- Seeding a database on startup
- Performing startup validation / health checks
- Pre-loading caches
- Printing a startup message with dynamic data

**Difference from `ApplicationRunner`:** `ApplicationRunner` receives `ApplicationArguments` instead of a raw `String[]`, making it easier to parse named arguments like `--name=value`.

---


---

<details>
<summary><strong>✅ Check Yourself — Part 1: Core Foundations</strong></summary>

1. What three annotations does `@SpringBootApplication` combine?
2. How does Spring Boot decide which auto-configuration classes to apply?
3. What is the difference between `@Configuration` and `@Component`?
4. Name three ways to run a Spring Boot application.
5. What happens inside `SpringApplication.run()` before your code executes?
6. When would you use `CommandLineRunner` vs `ApplicationRunner`?
7. How do you disable a specific auto-configuration class?

</details>

> **📚 Deep Dive — Read More (Part 1)**
> - [Spring Boot Auto-Configuration — Baeldung](https://www.baeldung.com/spring-boot-auto-configuration)
> - [Spring Boot Reference — Official Docs](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/)
> - [How SpringApplication.run() Works — DZone](https://dzone.com/articles/how-springapplication-runs)
> - [Spring Boot Starters List — Official](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using.build-systems.starters)


## Part 2 — Dependency Injection & Bean Management


> **🎯 FAANG Interview Tip — Part 2**
> At Meta and Amazon, DI questions focus on *why constructor injection is preferred* (immutability, testability, fail-fast), not just how to use `@Autowired`. Be ready to explain how Spring's BeanFactory resolves circular dependencies (only for field/setter injection via early references) and why constructor-based circular deps throw `BeanCurrentlyInCreationException`.

```
┌─────────────────────────────────────────────────────────────────┐
│           DEPENDENCY INJECTION — THE BIG PICTURE                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌────────────┐       injects        ┌───────────────┐        │
│   │  Spring    │ ───────────────────▶  │  Your Class   │        │
│   │  Container │                       │  (Bean)       │        │
│   └────────────┘                       └───────────────┘        │
│         │                                                       │
│         │  Three injection styles:                              │
│         │                                                       │
│   ┌─────┼──────────────────────────────────────────────┐        │
│   │     │                                              │        │
│   │  1. CONSTRUCTOR (✅ Preferred)                     │        │
│   │     @Service                                       │        │
│   │     class OrderService {                           │        │
│   │       private final PaymentService pay;            │        │
│   │       OrderService(PaymentService pay) {           │        │
│   │         this.pay = pay;  // immutable, testable    │        │
│   │       }                                            │        │
│   │     }                                              │        │
│   │                                                    │        │
│   │  2. SETTER (for optional dependencies)             │        │
│   │     @Autowired                                     │        │
│   │     void setCache(CacheService c) { ... }          │        │
│   │                                                    │        │
│   │  3. FIELD (❌ Avoid in production)                 │        │
│   │     @Autowired                                     │        │
│   │     private PaymentService pay; // hard to test    │        │
│   │                                                    │        │
│   └────────────────────────────────────────────────────┘        │
│                                                                 │
│   Resolving Ambiguity (multiple beans of same type):            │
│   ┌──────────────┐  ┌──────────────┐  ┌──────────────┐        │
│   │  @Primary    │  │  @Qualifier   │  │  @Profile    │        │
│   │  (default)   │  │  (by name)   │  │  (by env)    │        │
│   └──────────────┘  └──────────────┘  └──────────────┘        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```



---

### Q11. What is Dependency Injection (DI)?

**Analogy:** Imagine a coffee shop. Instead of each barista going to find their own coffee machine, the coffee machine is *delivered* to them when they start their shift. The barista doesn't care where the machine came from — they just use it. That delivery is DI.

DI is a design pattern where an object's dependencies are *provided by an external framework* rather than created inside the object itself. This promotes loose coupling and testability.

**Without DI (tight coupling):**
```java
public class OrderService {
    private PaymentService paymentService = new PaymentService(); // Hard-coded!
}
```

**With DI (loose coupling):**
```java
@Service
public class OrderService {
    private final PaymentService paymentService;

    @Autowired // Spring injects the correct implementation
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

---

### Q12. What are the three types of Dependency Injection in Spring?

**1. Constructor Injection (Recommended)**
```java
@Service
public class UserService {
    private final UserRepository userRepository;

    @Autowired // Optional in modern Spring — implied for single constructor
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```
*Best practice:* Use constructor injection. It makes dependencies explicit and supports immutability.

**2. Setter Injection**
```java
@Service
public class UserService {
    private UserRepository userRepository;

    @Autowired
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```
*Use when:* The dependency is optional.

**3. Field Injection**
```java
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository; // Injected by reflection
}
```
*Avoid in production:* Cannot be used without a Spring context, makes unit testing harder.

---

### Q13. What is IoC (Inversion of Control)?

**Analogy:** Traditional cooking — you control every step. IoC is like a restaurant — you tell the chef *what* you want (a dependency), and the kitchen (Spring container) decides *how* to make it and brings it to you.

IoC is the broader principle: control of object creation and lifecycle is *inverted* from your code to the framework. Spring's `ApplicationContext` is the IoC container. DI is the mechanism through which IoC is achieved.

---

### Q14. What is the difference between `@Component`, `@Service`, `@Repository`, and `@Controller`?

All four are stereotypes — they tell Spring to create a managed bean. The differences are semantic and behavioural:

| Annotation | Layer | Extra Behaviour |
|---|---|---|
| `@Component` | Generic | None — just a bean |
| `@Service` | Business Logic | None (semantic marker for clarity) |
| `@Repository` | Data Access | Translates `SQLException` → Spring `DataAccessException` |
| `@Controller` | Web (MVC) | Marks it for request mapping, returns views |
| `@RestController` | Web (REST) | `@Controller` + `@ResponseBody` — returns JSON/XML |

```java
@Repository
public class UserRepository {
    // SQL exceptions here are auto-translated to DataAccessException
}

@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;
}

@RestController
@RequestMapping("/users")
public class UserController {
    @Autowired
    private UserService userService;
}
```

---

### Q15. What is `@Autowired` and how does it work?

`@Autowired` tells Spring to resolve and inject a matching bean from the ApplicationContext automatically.

**Resolution order:**
1. **By type** — Spring looks for a bean matching the field/parameter type
2. **By `@Qualifier`** — if multiple beans of the same type exist, `@Qualifier` picks one
3. **By `@Primary`** — the default bean when no qualifier is specified

```java
public interface NotificationService {
    void send(String message);
}

@Service
@Primary
public class EmailNotificationService implements NotificationService { ... }

@Service
public class SmsNotificationService implements NotificationService { ... }

@Service
public class AlertManager {

    @Autowired
    @Qualifier("smsNotificationService")
    private NotificationService notificationService; // Gets SMS, not Email
}
```

---

### Q16. What happens when there are multiple beans of the same type? How do you resolve ambiguity?

**Analogy:** You order "a car" from a dealership that has both a sedan and an SUV. Without specifying, they don't know which to deliver. You need to be explicit.

**Solutions:**

**Option 1 — `@Primary`:** Marks the default when no other instruction is given.
```java
@Service
@Primary
public class EmailNotificationService implements NotificationService { }
```

**Option 2 — `@Qualifier`:** Explicitly names which bean to inject.
```java
@Autowired
@Qualifier("smsNotificationService")
private NotificationService notificationService;
```

**Option 3 — Inject as a `List`:** Receive all implementations.
```java
@Autowired
private List<NotificationService> allServices; // Gets both Email and SMS
```

---

### Q17. What is the `@Bean` annotation? How is it different from `@Component`?

| | `@Component` | `@Bean` |
|---|---|---|
| Applied to | Class | Method inside a `@Configuration` class |
| Purpose | Auto-detected via classpath scan | Explicitly declared in code |
| Use case | Your own classes | Third-party classes you can't annotate |

```java
@Configuration
public class AppConfig {

    @Bean
    public RestTemplate restTemplate() {
        // RestTemplate is a third-party class — you can't add @Component to it
        return new RestTemplate();
    }

    @Bean
    public ObjectMapper objectMapper() {
        ObjectMapper mapper = new ObjectMapper();
        mapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
        return mapper;
    }
}
```

---

### Q18. What is the `ApplicationContext` vs `BeanFactory`?

**Analogy:** `BeanFactory` is a basic toolbox. `ApplicationContext` is a fully equipped workshop with power tools, safety systems, and an intercom.

| Feature | BeanFactory | ApplicationContext |
|---|---|---|
| Bean creation | Lazy (on first request) | Eager (all singletons at startup) |
| Internationalization (i18n) | No | Yes |
| Event propagation | No | Yes |
| AOP integration | Limited | Full |
| Preferred in modern apps? | No | **Yes** |

In Spring Boot, the `ApplicationContext` used is `AnnotationConfigServletWebServerApplicationContext` for web applications.

---

### Q19. What is `@Value` and how do you use it?

`@Value` injects a single property value from `application.properties`, system properties, or an expression.

```properties
# application.properties
app.greeting=Hello, World!
app.max-connections=10
```

```java
@Service
public class GreetingService {

    @Value("${app.greeting}")
    private String greeting;

    @Value("${app.max-connections:5}") // 5 is the default if not set
    private int maxConnections;

    @Value("#{2 * 3}") // SpEL expression
    private int result; // = 6

    public String greet() {
        return greeting;
    }
}
```

---

### Q20. What is `@ConfigurationProperties`? Why is it preferred over `@Value` for groups of properties?

**Analogy:** `@Value` is like reading one ingredient at a time from a recipe. `@ConfigurationProperties` is like handing the entire recipe card to someone and they fill in all the blanks at once.

```properties
# application.properties
mail.host=smtp.gmail.com
mail.port=587
mail.username=noreply@example.com
mail.password=secret
```

```java
@ConfigurationProperties(prefix = "mail")
@Component
@Validated
public class MailProperties {

    @NotBlank
    private String host;

    private int port;
    private String username;
    private String password;

    // getters and setters
}
```

**Advantages over `@Value`:**
- Strongly typed — validation via `@Validated` and JSR-303 annotations
- IDE autocompletion support
- Type-safe binding (lists, maps, nested objects)
- Cleaner refactoring

---


---

<details>
<summary><strong>✅ Check Yourself — Part 2: Dependency Injection & Bean Management</strong></summary>

1. Why is constructor injection preferred over field injection?
2. What happens if there are two beans of the same type and no `@Primary` or `@Qualifier`?
3. Explain the difference between `@Bean` and `@Component`.
4. When would you use `BeanFactory` over `ApplicationContext`?
5. What is the difference between `@Value` and `@ConfigurationProperties`?
6. Can you inject a prototype-scoped bean into a singleton? What problem arises?

</details>

> **📚 Deep Dive — Read More (Part 2)**
> - [Spring Dependency Injection — Baeldung](https://www.baeldung.com/spring-dependency-injection)
> - [Constructor vs Field Injection — Why & How](https://www.baeldung.com/constructor-injection-in-spring)
> - [@ConfigurationProperties Guide — Baeldung](https://www.baeldung.com/configuration-properties-in-spring-boot)
> - [Understanding ApplicationContext — Spring Docs](https://docs.spring.io/spring-framework/reference/core/beans/context-introduction.html)


## Part 3 — Bean Scopes & Lifecycle


> **🎯 FAANG Interview Tip — Part 3**
> Netflix and Uber interviewers love bean lifecycle questions because they reveal whether you understand Spring's internals or just use annotations. Know the exact order: constructor → `@PostConstruct` → `afterPropertiesSet()` → custom init → ready → `@PreDestroy` → `destroy()` → custom destroy. Also know that prototype beans are NOT managed after creation — Spring won't call `@PreDestroy` on them.

```
┌─────────────────────────────────────────────────────────────────┐
│                  SPRING BEAN LIFECYCLE                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Container starts                                               │
│       │                                                         │
│       ▼                                                         │
│  ┌─────────────────────┐                                        │
│  │ Instantiate Bean    │  (constructor called)                  │
│  └────────┬────────────┘                                        │
│           ▼                                                     │
│  ┌─────────────────────┐                                        │
│  │ Populate Properties │  (DI: @Autowired, @Value)              │
│  └────────┬────────────┘                                        │
│           ▼                                                     │
│  ┌─────────────────────┐                                        │
│  │ BeanNameAware       │  setBeanName()                         │
│  │ BeanFactoryAware    │  setBeanFactory()                      │
│  │ ApplicationContext  │  setApplicationContext()                │
│  │   Aware             │                                        │
│  └────────┬────────────┘                                        │
│           ▼                                                     │
│  ┌─────────────────────┐                                        │
│  │ BeanPostProcessor   │  postProcessBeforeInitialization()     │
│  └────────┬────────────┘                                        │
│           ▼                                                     │
│  ┌─────────────────────┐                                        │
│  │ @PostConstruct      │  (JSR-250)                             │
│  │ InitializingBean    │  afterPropertiesSet()                  │
│  │ Custom init-method  │                                        │
│  └────────┬────────────┘                                        │
│           ▼                                                     │
│  ┌─────────────────────┐                                        │
│  │ BeanPostProcessor   │  postProcessAfterInitialization()      │
│  └────────┬────────────┘                                        │
│           ▼                                                     │
│  ╔═════════════════════╗                                        │
│  ║   BEAN IS READY     ║  ◀── available for injection           │
│  ╚════════╤════════════╝                                        │
│           ▼                                                     │
│  ┌─────────────────────┐                                        │
│  │ @PreDestroy         │  (on container shutdown)               │
│  │ DisposableBean      │  destroy()                             │
│  │ Custom destroy      │                                        │
│  └─────────────────────┘                                        │
│                                                                 │
│  ⚠️  Prototype beans: Spring creates but does NOT manage        │
│      destruction — @PreDestroy is never called automatically.   │
│                                                                 │
│  SCOPE COMPARISON:                                              │
│  ┌────────────┬──────────────────┬────────────────────────┐     │
│  │ Scope      │ Instances        │ Use Case               │     │
│  ├────────────┼──────────────────┼────────────────────────┤     │
│  │ singleton  │ 1 per container  │ Stateless services     │     │
│  │ prototype  │ new per request  │ Stateful / short-lived │     │
│  │ request    │ 1 per HTTP req   │ Request-scoped data    │     │
│  │ session    │ 1 per HTTP sess  │ User session data      │     │
│  │ websocket  │ 1 per WS session │ WebSocket handlers     │     │
│  └────────────┴──────────────────┴────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```



---

### Q21. What are the different bean scopes in Spring Boot?

**Analogy:** Think of scopes as hotel room policies:
- **Singleton** = one room, everyone shares it
- **Prototype** = a new room for every guest
- **Request** = a new room per check-in request
- **Session** = one room per guest for their entire stay

| Scope | Description | Use Case |
|---|---|---|
| `singleton` | One instance per Spring container (default) | Stateless services |
| `prototype` | New instance every time it's requested | Stateful objects |
| `request` | One per HTTP request (web only) | Per-request state |
| `session` | One per HTTP session (web only) | Shopping cart, user preferences |
| `application` | One per `ServletContext` | App-wide shared data |

---

### Q22. What is Singleton scope? Demonstrate it.

```java
@Component
@Scope("singleton") // This is the default — you don't even need to specify it
public class SingletonBean {
    public SingletonBean() {
        System.out.println("SingletonBean created: " + this.hashCode());
    }
}

// In a test:
SingletonBean b1 = context.getBean(SingletonBean.class);
SingletonBean b2 = context.getBean(SingletonBean.class);
System.out.println(b1 == b2); // true — same instance
```

---

### Q23. What is Prototype scope? Demonstrate it.

```java
@Component
@Scope("prototype")
public class PrototypeBean {
    public PrototypeBean() {
        System.out.println("PrototypeBean created: " + this.hashCode());
    }
}

// In a test:
PrototypeBean p1 = context.getBean(PrototypeBean.class);
PrototypeBean p2 = context.getBean(PrototypeBean.class);
System.out.println(p1 == p2); // false — different instances
```

**Warning:** Spring does not manage the destruction lifecycle of prototype beans. You're responsible for cleanup.

---

### Q24. What is Request scope?

Each HTTP request gets its own fresh bean instance. Ideal for holding per-request data.

```java
@Component
@Scope(value = WebApplicationContext.SCOPE_REQUEST,
       proxyMode = ScopedProxyMode.TARGET_CLASS)
public class RequestContext {
    private String requestId = UUID.randomUUID().toString();
    // getters
}
```

The `proxyMode = ScopedProxyMode.TARGET_CLASS` is required when injecting a request-scoped bean into a singleton — Spring creates a proxy that fetches the real bean for each request.

---

### Q25. What is Session scope?

One bean instance per HTTP session — lives until the session is invalidated.

```java
@Component
@Scope(value = WebApplicationContext.SCOPE_SESSION,
       proxyMode = ScopedProxyMode.TARGET_CLASS)
public class ShoppingCart {
    private List<String> items = new ArrayList<>();

    public void addItem(String item) { items.add(item); }
    public List<String> getItems() { return items; }
}
```

---

### Q26. What is the Bean Lifecycle in Spring?

**Analogy:** A bean is like a restaurant employee. Spring does the hiring (instantiation), onboarding (property injection), first-day briefing (`@PostConstruct`), daily work (usage), and eventually the exit interview (`@PreDestroy`).

The full lifecycle:
1. **Instantiation** — Spring creates the object via constructor
2. **Property Population** — `@Autowired` dependencies injected
3. **`BeanNameAware`, `BeanFactoryAware` callbacks** (if implemented)
4. **`@PostConstruct`** — custom initialization logic runs
5. **Ready to use** — the bean serves requests
6. **`@PreDestroy`** — custom cleanup runs before destruction
7. **Destruction** — object is garbage collected

```java
@Component
public class DatabaseConnectionPool {

    @PostConstruct
    public void init() {
        System.out.println("Connection pool initialized!");
        // Open connections, warm up cache, etc.
    }

    @PreDestroy
    public void cleanup() {
        System.out.println("Connection pool shutting down!");
        // Close connections, release resources
    }
}
```

---

### Q27. What is `@Lazy` in Spring Boot?

By default, all singleton beans are created eagerly at startup. `@Lazy` delays creation until the bean is first accessed.

```java
@Component
@Lazy
public class ExpensiveService {
    public ExpensiveService() {
        System.out.println("ExpensiveService is now created (lazily)");
    }
}
```

You can also enable lazy initialization globally:
```properties
spring.main.lazy-initialization=true
```

**Trade-off:** Faster startup time, but the first request to a lazy bean may be slower.

---

### Q28. What is the difference between `@Scope("prototype")` and `@Lazy`?

| | `@Scope("prototype")` | `@Lazy` |
|---|---|---|
| When created? | On every `getBean()` call | On first access only |
| How many instances? | One per request | One (then cached as singleton) |
| Use case | Stateful, per-use objects | Expensive startup, rarely used |

---

### Q29. What is `@Conditional`? Give a real-world example.

`@Conditional` allows a bean to be created only when a specific condition is met.

```java
// Custom condition: only create this bean if running on Windows
public class WindowsCondition implements Condition {
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        return context.getEnvironment().getProperty("os.name").contains("Windows");
    }
}

@Configuration
public class OsConfig {

    @Bean
    @Conditional(WindowsCondition.class)
    public FilePathService windowsFilePathService() {
        return new WindowsFilePathService();
    }
}
```

Spring Boot's built-in conditionals (`@ConditionalOnClass`, `@ConditionalOnProperty`, `@ConditionalOnMissingBean`) power the entire auto-configuration system.

---

### Q30. What is the difference between `BeanFactory` and `ApplicationContext` initialization?

```java
// BeanFactory — lazy: beans created on demand
BeanFactory factory = new XmlBeanFactory(new ClassPathResource("beans.xml"));
// No beans created yet at this point!
MyBean bean = factory.getBean(MyBean.class); // Created NOW

// ApplicationContext — eager: all singletons created at startup
ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
// All singleton beans already created by the time this line finishes
```

---


---

<details>
<summary><strong>✅ Check Yourself — Part 3: Bean Scopes & Lifecycle</strong></summary>

1. List all five bean scopes and give a use case for each.
2. What is the exact order of bean lifecycle callbacks?
3. Will `@PreDestroy` be called on a prototype-scoped bean? Why or why not?
4. What does `@Lazy` do and when should you use it?
5. Explain the difference between `@Scope("prototype")` and `@Lazy`.
6. Give a real-world scenario where `@Conditional` is useful.

</details>

> **📚 Deep Dive — Read More (Part 3)**
> - [Spring Bean Scopes — Baeldung](https://www.baeldung.com/spring-bean-scopes)
> - [Spring Bean Lifecycle — Baeldung](https://www.baeldung.com/spring-bean-lifecycle)
> - [@Lazy Initialization — Baeldung](https://www.baeldung.com/spring-lazy-annotation)
> - [Spring @Conditional — Baeldung](https://www.baeldung.com/spring-conditional-annotations)


## Part 4 — Data Access, JPA & Exception Handling


> **🎯 FAANG Interview Tip — Part 4**
> The N+1 problem (Q40) and `@Transactional` propagation (Q35) are among the top 5 most-asked Spring Boot questions at FAANG companies. For N+1, know three solutions: `JOIN FETCH`, `@EntityGraph`, and `@BatchSize`. For transactions, be able to explain `REQUIRED` vs `REQUIRES_NEW` with a concrete scenario (e.g., order placement + audit logging where audit must persist even if order rolls back).

```
┌─────────────────────────────────────────────────────────────────┐
│              SPRING DATA JPA REPOSITORY HIERARCHY               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│                    Repository<T, ID>                             │
│                          │                                      │
│                          ▼                                      │
│               CrudRepository<T, ID>                             │
│               ┌──────────────────┐                              │
│               │ save()           │                              │
│               │ findById()       │                              │
│               │ findAll()        │                              │
│               │ deleteById()     │                              │
│               │ count()          │                              │
│               │ existsById()     │                              │
│               └────────┬─────────┘                              │
│                        ▼                                        │
│          PagingAndSortingRepository<T, ID>                      │
│               ┌──────────────────┐                              │
│               │ findAll(Sort)    │                              │
│               │ findAll(Pageable)│                              │
│               └────────┬─────────┘                              │
│                        ▼                                        │
│              JpaRepository<T, ID>                               │
│               ┌──────────────────┐                              │
│               │ flush()          │                              │
│               │ saveAndFlush()   │                              │
│               │ deleteInBatch()  │                              │
│               │ getById()        │  ◀── adds JPA-specific ops   │
│               └──────────────────┘                              │
│                                                                 │
│                                                                 │
│   @TRANSACTIONAL PROPAGATION CHEAT SHEET                        │
│   ┌──────────────────┬───────────────────────────────────────┐  │
│   │ Type             │ Behavior                              │  │
│   ├──────────────────┼───────────────────────────────────────┤  │
│   │ REQUIRED (def.)  │ Join existing or create new           │  │
│   │ REQUIRES_NEW     │ Always create new (suspend current)   │  │
│   │ NESTED           │ Savepoint inside current              │  │
│   │ SUPPORTS         │ Join if exists, else non-transactional│  │
│   │ NOT_SUPPORTED    │ Suspend current, run without          │  │
│   │ MANDATORY        │ Must have existing, else exception    │  │
│   │ NEVER            │ Must NOT have existing, else exception│  │
│   └──────────────────┴───────────────────────────────────────┘  │
│                                                                 │
│   THE N+1 PROBLEM VISUALIZED                                    │
│                                                                 │
│   ❌ Without fix (N+1):                                        │
│   SELECT * FROM department;           -- 1 query                │
│   SELECT * FROM employee WHERE dept=1; -- +1                    │
│   SELECT * FROM employee WHERE dept=2; -- +1                    │
│   SELECT * FROM employee WHERE dept=3; -- +1  ... N queries!    │
│                                                                 │
│   ✅ With JOIN FETCH:                                           │
│   SELECT d.*, e.* FROM department d                             │
│     LEFT JOIN employee e ON d.id = e.dept_id;  -- 1 query!      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```



---

### Q31. What is Spring Data JPA?

**Analogy:** JPA gives you the rules for talking to a database. Hibernate implements those rules. Spring Data JPA wraps all of it with a magic layer — you declare *what* queries you want, it figures out *how* to run them.

Spring Data JPA is a Spring module that makes data access layers almost effortless by:
- Providing `JpaRepository` / `CrudRepository` interfaces with built-in CRUD methods
- Auto-generating queries from method names
- Supporting custom JPQL / native SQL queries

```java
// Entity
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;
    // getters/setters
}

// Repository — no implementation needed!
public interface UserRepository extends JpaRepository<User, Long> {

    // Spring generates: SELECT * FROM users WHERE email = ?
    Optional<User> findByEmail(String email);

    // Spring generates: SELECT * FROM users WHERE name LIKE %?%
    List<User> findByNameContaining(String keyword);

    // Custom JPQL
    @Query("SELECT u FROM User u WHERE u.email = :email AND u.active = true")
    Optional<User> findActiveUserByEmail(@Param("email") String email);
}
```

---

### Q32. What is the difference between `JpaRepository`, `CrudRepository`, and `PagingAndSortingRepository`?

```
CrudRepository
    └── PagingAndSortingRepository
            └── JpaRepository
```

| Repository | Key Methods Added |
|---|---|
| `CrudRepository` | `save`, `findById`, `findAll`, `delete`, `count` |
| `PagingAndSortingRepository` | `findAll(Pageable)`, `findAll(Sort)` |
| `JpaRepository` | `flush`, `saveAndFlush`, `deleteInBatch`, `getOne` |

**In practice, always extend `JpaRepository`** unless you have a reason to use a simpler interface.

---

### Q33. How do you implement pagination and sorting?

```java
// Controller
@GetMapping("/users")
public Page<User> getUsers(
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "10") int size,
    @RequestParam(defaultValue = "name") String sortBy
) {
    Pageable pageable = PageRequest.of(page, size, Sort.by(sortBy).ascending());
    return userRepository.findAll(pageable);
}
```

The `Page<T>` response includes:
- `content` — the data
- `totalElements` — total record count
- `totalPages` — total page count
- `number` — current page

---

### Q34. What is `@Transactional`? How does it work?

**Analogy:** Think of a bank transfer. You debit Account A and credit Account B. If the credit fails, the debit must be rolled back. That atomicity guarantee is what `@Transactional` provides.

```java
@Service
public class TransferService {

    @Autowired
    private AccountRepository accountRepository;

    @Transactional // Everything inside runs in one transaction
    public void transfer(Long fromId, Long toId, BigDecimal amount) {
        Account from = accountRepository.findById(fromId).orElseThrow();
        Account to = accountRepository.findById(toId).orElseThrow();

        from.setBalance(from.getBalance().subtract(amount));
        to.setBalance(to.getBalance().add(amount));

        accountRepository.save(from);
        accountRepository.save(to);
        // If any exception occurs here, both saves are rolled back
    }
}
```

Spring uses AOP proxies — when you call a `@Transactional` method, Spring wraps it in a transaction, commits on success, and rolls back on `RuntimeException`.

---

### Q35. What are transaction propagation types?

| Propagation | Behaviour |
|---|---|
| `REQUIRED` (default) | Use existing transaction or create new one |
| `REQUIRES_NEW` | Always create a new transaction; suspend the current |
| `SUPPORTS` | Use existing transaction if available; else run without |
| `MANDATORY` | Must have existing transaction; throws if none |
| `NEVER` | Must NOT have a transaction; throws if one exists |
| `NESTED` | Execute within a nested transaction if one exists |

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void auditLog(String action) {
    // This always runs in its own transaction
    // Won't be rolled back even if the outer transaction fails
    auditRepository.save(new AuditLog(action));
}
```

---

### Q36. What is exception handling in Spring Boot? How do you implement it?

**Analogy:** Think of a customer service hotline. Instead of every employee (controller) dealing with angry customers (exceptions) individually, there's a dedicated support team (`@ControllerAdvice`) that handles all complaints centrally.

**Method 1 — `@ExceptionHandler` on a controller:**
```java
@RestController
public class UserController {

    @GetMapping("/user/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id)
            .orElseThrow(() -> new UserNotFoundException("User not found: " + id));
    }

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<String> handleNotFound(UserNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
    }
}
```

**Method 2 — Global `@ControllerAdvice` (Recommended):**
```java
// Custom exception
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String message) {
        super(message);
    }
}

// Global error response structure
public class ErrorResponse {
    private int status;
    private String message;
    private LocalDateTime timestamp;
    // constructor, getters
}

// Global handler
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFound(UserNotFoundException ex) {
        ErrorResponse error = new ErrorResponse(404, ex.getMessage(), LocalDateTime.now());
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGenericException(Exception ex) {
        ErrorResponse error = new ErrorResponse(500, "Internal Server Error", LocalDateTime.now());
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
    }
}
```

---

### Q37. What is the difference between `@ControllerAdvice` and `@RestControllerAdvice`?

`@RestControllerAdvice` = `@ControllerAdvice` + `@ResponseBody`

Use `@RestControllerAdvice` for REST APIs (JSON responses). Use `@ControllerAdvice` for MVC apps returning views.

---

### Q38. What is `@ResponseStatus`?

Marks an exception class with the HTTP status code it should map to:

```java
@ResponseStatus(HttpStatus.NOT_FOUND)
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```

When thrown, Spring automatically returns a 404 response without needing an `@ExceptionHandler`.

---

### Q39. How do you configure Spring Boot to use a MySQL database?

**`pom.xml`:**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <scope>runtime</scope>
</dependency>
```

**`application.properties`:**
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=secret
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
```

---

### Q40. What is the N+1 problem in JPA and how do you fix it?

**Analogy:** You go to a store and ask for a list of 100 orders. Then for each order, you make a *separate trip* to ask who placed it. That's 101 trips instead of 1.

```java
// N+1 problem: 1 query for all orders + N queries for each customer
List<Order> orders = orderRepository.findAll();
for (Order order : orders) {
    System.out.println(order.getCustomer().getName()); // Triggers a separate query!
}

// Fix 1 — Fetch Join (JPQL)
@Query("SELECT o FROM Order o JOIN FETCH o.customer")
List<Order> findAllWithCustomer();

// Fix 2 — @EntityGraph
@EntityGraph(attributePaths = {"customer"})
List<Order> findAll();
```

---


---

<details>
<summary><strong>✅ Check Yourself — Part 4: Data Access, JPA & Exception Handling</strong></summary>

1. What is the difference between `JpaRepository`, `CrudRepository`, and `PagingAndSortingRepository`?
2. Explain `REQUIRED` vs `REQUIRES_NEW` transaction propagation with an example.
3. What is the N+1 problem? Give three ways to fix it.
4. How does `@ControllerAdvice` work for global exception handling?
5. What is the difference between `save()` and `saveAndFlush()`?
6. When would you use `@ResponseStatus` on a custom exception?

</details>

> **🔥 Real-World Interview Scenario — Amazon**
> *"You have an order service that creates an order and sends an audit event. If the order creation fails, the audit event should still be persisted. How would you design the transaction boundaries?"*
>
> **Answer:** Use `REQUIRES_NEW` propagation for the audit service method. This creates a separate transaction that commits independently. Even if the outer order transaction rolls back, the audit record persists.
> ```java
> @Service
> public class OrderService {
>     @Transactional  // REQUIRED (default)
>     public void placeOrder(Order order) {
>         orderRepo.save(order);
>         auditService.logAudit("ORDER_PLACED", order.getId()); // separate tx
>         // if this throws, order rolls back but audit persists
>     }
> }
> @Service
> public class AuditService {
>     @Transactional(propagation = Propagation.REQUIRES_NEW)
>     public void logAudit(String event, Long entityId) {
>         auditRepo.save(new AuditLog(event, entityId));
>     }
> }
> ```

> **📚 Deep Dive — Read More (Part 4)**
> - [Spring Data JPA — Baeldung](https://www.baeldung.com/the-persistence-layer-with-spring-data-jpa)
> - [Transaction Propagation — Baeldung](https://www.baeldung.com/spring-transactional-propagation-isolation)
> - [N+1 Problem Solutions — Vlad Mihalcea](https://vladmihalcea.com/n-plus-1-query-problem/)
> - [Exception Handling in Spring Boot — Baeldung](https://www.baeldung.com/exception-handling-for-rest-with-spring)


## Part 5 — Spring Boot Testing & Actuator


> **🎯 FAANG Interview Tip — Part 5**
> At Google and Stripe, you'll be asked about the *testing pyramid* in Spring Boot. Know when to use each: `@WebMvcTest` (controller layer only, fast), `@DataJpaTest` (repository layer, embedded DB), `@SpringBootTest` (full context, slow — use sparingly). Explain *why* `@MockBean` replaces a real bean in the context and when `@SpyBean` is better.

```
┌─────────────────────────────────────────────────────────────────┐
│             SPRING BOOT TESTING PYRAMID                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│                          ╱╲                                     │
│                         ╱  ╲                                    │
│                        ╱ E2E╲      @SpringBootTest              │
│                       ╱ Tests╲     (full context, slow)         │
│                      ╱────────╲                                 │
│                     ╱Integration╲   @WebMvcTest                 │
│                    ╱   Tests     ╲  @DataJpaTest                │
│                   ╱───────────────╲ @WebFluxTest                │
│                  ╱   Unit Tests    ╲  @MockBean + Mockito       │
│                 ╱   (fast, isolated)╲ No Spring context          │
│                ╱─────────────────────╲                          │
│                                                                 │
│   TEST ANNOTATION DECISION TREE                                 │
│                                                                 │
│   What are you testing?                                         │
│   │                                                             │
│   ├── Controller endpoint?                                      │
│   │   └── @WebMvcTest(MyController.class)                       │
│   │       + @MockBean for service dependencies                  │
│   │                                                             │
│   ├── Repository / JPA query?                                   │
│   │   └── @DataJpaTest                                          │
│   │       (auto-configures embedded H2 + test EntityManager)    │
│   │                                                             │
│   ├── Service logic?                                            │
│   │   └── Plain JUnit 5 + @ExtendWith(MockitoExtension.class)  │
│   │       + @Mock / @InjectMocks  (no Spring context!)          │
│   │                                                             │
│   └── Full integration / cross-layer?                           │
│       └── @SpringBootTest(webEnvironment = RANDOM_PORT)         │
│           + TestRestTemplate or WebTestClient                   │
│                                                                 │
│                                                                 │
│   ACTUATOR ARCHITECTURE                                         │
│                                                                 │
│   Application ──▶ /actuator/health   ──▶ Health indicators      │
│               ──▶ /actuator/metrics  ──▶ Micrometer registry    │
│               ──▶ /actuator/info     ──▶ Build/git info         │
│               ──▶ /actuator/env      ──▶ Environment props      │
│                        │                                        │
│                        ▼                                        │
│              ┌──────────────────┐                               │
│              │   Prometheus     │ ──▶ Grafana Dashboard         │
│              │   (scrapes /     │                               │
│              │    actuator/     │                               │
│              │    prometheus)   │                               │
│              └──────────────────┘                               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```



---

### Q41. What testing annotations does Spring Boot provide?

| Annotation | Purpose |
|---|---|
| `@SpringBootTest` | Loads the full application context — integration test |
| `@WebMvcTest` | Only loads the web layer (controller + MVC) |
| `@DataJpaTest` | Only loads JPA layer — uses in-memory H2 by default |
| `@MockBean` | Creates a Mockito mock and adds it to context |
| `@SpyBean` | Creates a Mockito spy of a real bean |

---

### Q42. How do you write a unit test for a Spring Boot service?

```java
// UserService
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;

    public User createUser(String name, String email) {
        User user = new User(name, email);
        return userRepository.save(user);
    }
}

// Unit Test (no Spring context — pure Mockito)
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    @Test
    void createUser_shouldSaveAndReturnUser() {
        User expectedUser = new User("Samuel", "samuel@example.com");
        when(userRepository.save(any(User.class))).thenReturn(expectedUser);

        User result = userService.createUser("Samuel", "samuel@example.com");

        assertThat(result.getName()).isEqualTo("Samuel");
        verify(userRepository, times(1)).save(any(User.class));
    }
}
```

---

### Q43. How do you test a REST controller using `@WebMvcTest`?

```java
@WebMvcTest(UserController.class)
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private UserService userService; // Mock the service layer

    @Test
    void getUser_shouldReturn200WithUser() throws Exception {
        User user = new User(1L, "Samuel", "samuel@example.com");
        when(userService.findById(1L)).thenReturn(Optional.of(user));

        mockMvc.perform(get("/users/1")
                .contentType(MediaType.APPLICATION_JSON))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("Samuel"))
            .andExpect(jsonPath("$.email").value("samuel@example.com"));
    }

    @Test
    void getUser_shouldReturn404WhenNotFound() throws Exception {
        when(userService.findById(99L)).thenReturn(Optional.empty());

        mockMvc.perform(get("/users/99"))
            .andExpect(status().isNotFound());
    }
}
```

---

### Q44. How do you write a JPA repository test?

```java
@DataJpaTest // Configures in-memory H2, scans only JPA components
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    void findByEmail_shouldReturnUser() {
        User user = new User("Samuel", "samuel@example.com");
        userRepository.save(user);

        Optional<User> found = userRepository.findByEmail("samuel@example.com");

        assertThat(found).isPresent();
        assertThat(found.get().getName()).isEqualTo("Samuel");
    }
}
```

---

### Q45. What is Spring Boot Actuator?

**Analogy:** Actuator is like a car's dashboard — while the engine is running, it gives you live readings on speed, fuel level, temperature, and alerts. You didn't build those gauges yourself; they came with the car.

Spring Boot Actuator provides production-ready HTTP endpoints to monitor and manage your application.

**Setup:**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

```properties
# Expose all endpoints (restrict in production!)
management.endpoints.web.exposure.include=health,info,metrics,env,beans,loggers
management.endpoint.health.show-details=always
```

---

### Q46. What are the key Actuator endpoints?

| Endpoint | Description |
|---|---|
| `/actuator/health` | App health status |
| `/actuator/info` | Custom app info |
| `/actuator/metrics` | JVM, HTTP, custom metrics |
| `/actuator/env` | Environment properties |
| `/actuator/beans` | All Spring beans |
| `/actuator/loggers` | View/change log levels at runtime |
| `/actuator/threaddump` | JVM thread dump |
| `/actuator/heapdump` | JVM heap dump |

---

### Q47. How do you create a custom health indicator?

```java
@Component
public class ExternalApiHealthIndicator implements HealthIndicator {

    @Autowired
    private RestTemplate restTemplate;

    @Override
    public Health health() {
        try {
            ResponseEntity<String> response = restTemplate
                .getForEntity("https://api.example.com/ping", String.class);
            if (response.getStatusCode().is2xxSuccessful()) {
                return Health.up()
                    .withDetail("ExternalAPI", "Reachable")
                    .build();
            }
        } catch (Exception e) {
            return Health.down()
                .withDetail("ExternalAPI", "Unreachable")
                .withException(e)
                .build();
        }
        return Health.unknown().build();
    }
}
```

---

### Q48. How do you integrate Actuator with Prometheus and Grafana?

```xml
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
```

```properties
management.endpoints.web.exposure.include=prometheus,health
management.endpoint.prometheus.enabled=true
```

Prometheus scrapes `/actuator/prometheus` on a schedule. Grafana visualises the time-series data. This is the industry standard for Spring Boot observability.

---

### Q49. What is `@SpringBootTest` and when should you use it?

`@SpringBootTest` loads the complete `ApplicationContext` — essentially boots the entire app for the test. It's an **integration test**, not a unit test.

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class UserIntegrationTest {

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void createUser_endToEnd() {
        UserRequest request = new UserRequest("Samuel", "samuel@example.com");
        ResponseEntity<User> response = restTemplate.postForEntity("/users", request, User.class);

        assertThat(response.getStatusCode()).isEqualTo(HttpStatus.CREATED);
        assertThat(response.getBody().getName()).isEqualTo("Samuel");
    }
}
```

**When to use:**
- End-to-end integration tests
- Testing cross-layer interactions (controller → service → repository → DB)

**Avoid for:** Unit tests — they're slow. Use `@WebMvcTest` or plain Mockito instead.

---

### Q50. What is the `@MockBean` annotation?

`@MockBean` creates a Mockito mock and registers it as a Spring bean in the test context. Use it when you're using `@SpringBootTest` or `@WebMvcTest` and need to stub out a dependency:

```java
@WebMvcTest(OrderController.class)
class OrderControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private OrderService orderService; // Replaces real bean in context

    @MockBean
    private PaymentService paymentService; // Also replaced

    // ...
}
```

---


---

<details>
<summary><strong>✅ Check Yourself — Part 5: Testing & Actuator</strong></summary>

1. When should you use `@WebMvcTest` vs `@SpringBootTest`?
2. What does `@MockBean` do that `@Mock` from Mockito does not?
3. How does `@DataJpaTest` configure the test environment?
4. What is the difference between `@SpringBootTest(webEnvironment = MOCK)` and `RANDOM_PORT`?
5. Name four Actuator endpoints and what they expose.
6. How do you create a custom health indicator?

</details>

> **📚 Deep Dive — Read More (Part 5)**
> - [Testing in Spring Boot — Baeldung](https://www.baeldung.com/spring-boot-testing)
> - [@WebMvcTest vs @SpringBootTest — Baeldung](https://www.baeldung.com/spring-boot-testing-web-layer)
> - [Spring Boot Actuator — Official Guide](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#actuator)
> - [Prometheus + Grafana + Spring Boot — Baeldung](https://www.baeldung.com/spring-boot-self-hosted-monitoring)


## Part 6 — Security, REST API Design & Profiles


> **🎯 FAANG Interview Tip — Part 6**
> JWT authentication flow is asked at virtually every FAANG interview involving Spring Boot. You must be able to draw the complete flow: login → validate credentials → generate JWT → return token → client sends `Authorization: Bearer <token>` → filter extracts token → validate signature + expiry → set `SecurityContext` → proceed. Also know the Spring Security filter chain order.

```
┌─────────────────────────────────────────────────────────────────┐
│             SPRING SECURITY FILTER CHAIN                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  HTTP Request                                                   │
│       │                                                         │
│       ▼                                                         │
│  ┌─────────────────────────────────────────────────┐            │
│  │            DelegatingFilterProxy                │            │
│  │  (bridges Servlet Filter → Spring Security)     │            │
│  └────────────────────┬────────────────────────────┘            │
│                       ▼                                         │
│  ┌─────────────────────────────────────────────────┐            │
│  │          FilterChainProxy                       │            │
│  │  ┌───────────────────────────────────────────┐  │            │
│  │  │ 1. SecurityContextPersistenceFilter       │  │            │
│  │  │ 2. CsrfFilter                            │  │            │
│  │  │ 3. LogoutFilter                           │  │            │
│  │  │ 4. UsernamePasswordAuthenticationFilter   │  │            │
│  │  │ 5. ★ JwtAuthenticationFilter (custom)     │  │            │
│  │  │ 6. ExceptionTranslationFilter             │  │            │
│  │  │ 7. FilterSecurityInterceptor (authz)      │  │            │
│  │  └───────────────────────────────────────────┘  │            │
│  └────────────────────┬────────────────────────────┘            │
│                       ▼                                         │
│              DispatcherServlet                                  │
│                       │                                         │
│                       ▼                                         │
│                  Controller                                     │
│                                                                 │
│                                                                 │
│   JWT AUTHENTICATION FLOW                                       │
│                                                                 │
│   Client                    Server                              │
│     │                         │                                 │
│     │  POST /auth/login       │                                 │
│     │  {user, password}       │                                 │
│     │────────────────────────▶│                                 │
│     │                         │── validate credentials          │
│     │                         │── generate JWT (secret + expiry)│
│     │   { "token": "eyJ..." } │                                 │
│     │◀────────────────────────│                                 │
│     │                         │                                 │
│     │  GET /api/orders         │                                 │
│     │  Authorization:          │                                 │
│     │  Bearer eyJ...           │                                 │
│     │────────────────────────▶│                                 │
│     │                         │── JwtFilter extracts token      │
│     │                         │── validate signature + expiry   │
│     │                         │── set SecurityContext            │
│     │                         │── proceed to controller         │
│     │  { orders: [...] }      │                                 │
│     │◀────────────────────────│                                 │
│                                                                 │
│                                                                 │
│   PROFILE ACTIVATION HIERARCHY                                  │
│                                                                 │
│   ┌─────────────────────────────────────────────┐               │
│   │ Priority (highest to lowest):               │               │
│   │ 1. Command line: --spring.profiles.active=  │               │
│   │ 2. System property: -Dspring.profiles.active│               │
│   │ 3. OS env: SPRING_PROFILES_ACTIVE           │               │
│   │ 4. application.properties/yml               │               │
│   │ 5. @ActiveProfiles (tests only)             │               │
│   └─────────────────────────────────────────────┘               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```



---

### Q51. How do you secure a Spring Boot application with Spring Security?

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Adding this dependency alone enables:
- HTTP Basic authentication on all endpoints
- A generated password printed to console at startup

**Custom security configuration:**
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(AbstractHttpConfigurer::disable)
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/public/**").permitAll()
                .requestMatchers("/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .httpBasic(Customizer.withDefaults());

        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

---

### Q52. How do you implement JWT authentication in Spring Boot?

**Flow:**
1. Client sends credentials to `/auth/login`
2. Server validates and returns a JWT token
3. Client includes token in `Authorization: Bearer <token>` header
4. Server validates token on every request via a filter

```java
// JWT Utility
@Component
public class JwtUtil {
    private final String SECRET = "your-secret-key";

    public String generateToken(String username) {
        return Jwts.builder()
            .setSubject(username)
            .setIssuedAt(new Date())
            .setExpiration(new Date(System.currentTimeMillis() + 86400000)) // 24h
            .signWith(SignatureAlgorithm.HS256, SECRET)
            .compact();
    }

    public String extractUsername(String token) {
        return Jwts.parser().setSigningKey(SECRET)
            .parseClaimsJws(token).getBody().getSubject();
    }
}

// JWT Filter
@Component
public class JwtFilter extends OncePerRequestFilter {

    @Autowired
    private JwtUtil jwtUtil;

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain chain) throws IOException, ServletException {
        String authHeader = request.getHeader("Authorization");
        if (authHeader != null && authHeader.startsWith("Bearer ")) {
            String token = authHeader.substring(7);
            String username = jwtUtil.extractUsername(token);
            // Set authentication in SecurityContext
        }
        chain.doFilter(request, response);
    }
}
```

---

### Q53. What are Spring Boot Profiles?

**Analogy:** Profiles are like different costumes for the same actor. The `dev` costume has a debug vest and test props. The `prod` costume is formal with real tools. Same application, different outfit.

Profiles allow you to segregate configuration for different environments:

```
src/main/resources/
├── application.properties         (shared)
├── application-dev.properties     (development)
├── application-prod.properties    (production)
└── application-test.properties    (testing)
```

```properties
# application.properties
spring.profiles.active=dev

# application-dev.properties
spring.datasource.url=jdbc:h2:mem:devdb
logging.level.com.myapp=DEBUG

# application-prod.properties
spring.datasource.url=jdbc:mysql://prod-server:3306/mydb
logging.level.com.myapp=WARN
```

**Activating a profile:**
```bash
# Command line
java -jar app.jar --spring.profiles.active=prod

# Environment variable
export SPRING_PROFILES_ACTIVE=prod
```

---

### Q54. How do you create profile-specific beans?

```java
@Configuration
public class DatabaseConfig {

    @Bean
    @Profile("dev")
    public DataSource devDataSource() {
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.H2)
            .build();
    }

    @Bean
    @Profile("prod")
    public DataSource prodDataSource() {
        HikariDataSource ds = new HikariDataSource();
        ds.setJdbcUrl("jdbc:mysql://prod:3306/mydb");
        return ds;
    }
}
```

---

### Q55. What is the difference between `@Controller` and `@RestController`?

```java
// @Controller — returns a view name (for Thymeleaf, JSP, etc.)
@Controller
public class PageController {
    @GetMapping("/home")
    public String homePage(Model model) {
        model.addAttribute("name", "Samuel");
        return "home"; // Resolves to home.html template
    }
}

// @RestController — @Controller + @ResponseBody
// Returns data directly (JSON, XML) — no view resolution
@RestController
@RequestMapping("/api/users")
public class UserApiController {
    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id); // Auto-serialised to JSON
    }
}
```

---

### Q56. What is the difference between `@RequestMapping` and `@GetMapping`?

```java
// @RequestMapping — generic, can handle any HTTP method
@RequestMapping(value = "/users", method = RequestMethod.GET)
public List<User> getUsers() { ... }

// @GetMapping — shortcut for @RequestMapping(method = GET)
@GetMapping("/users")
public List<User> getUsers() { ... }
```

Prefer the specific shortcuts: `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@PatchMapping` — they're more readable and self-documenting.

---

### Q57. What is the difference between `@PathVariable` and `@RequestParam`?

```java
// @PathVariable — extracts value from the URL path
// URL: GET /users/42
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) { ... }

// @RequestParam — extracts value from query string
// URL: GET /users?page=2&size=10
@GetMapping("/users")
public List<User> getUsers(
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "10") int size
) { ... }
```

---

### Q58. What is `@RequestBody` and `@ResponseBody`?

```java
// @RequestBody — deserializes JSON from request body into a Java object
@PostMapping("/users")
public ResponseEntity<User> createUser(@RequestBody @Valid UserRequest request) {
    User saved = userService.save(request);
    return ResponseEntity.status(HttpStatus.CREATED).body(saved);
}

// @ResponseBody — serializes Java object to JSON in response
// (Implicit in @RestController — you rarely write it explicitly)
@GetMapping("/users/{id}")
@ResponseBody
public User getUser(@PathVariable Long id) {
    return userService.findById(id);
}
```

---

### Q59. How do you handle validation in Spring Boot?

```java
// Request DTO with validation constraints
public class UserRequest {
    @NotBlank(message = "Name is required")
    private String name;

    @Email(message = "Invalid email format")
    @NotBlank
    private String email;

    @Min(value = 18, message = "Age must be at least 18")
    private int age;
}

// Controller — @Valid triggers validation
@PostMapping("/users")
public ResponseEntity<User> createUser(@RequestBody @Valid UserRequest request,
                                        BindingResult bindingResult) {
    if (bindingResult.hasErrors()) {
        // Handle validation errors
    }
    return ResponseEntity.ok(userService.save(request));
}

// In @RestControllerAdvice — handle validation failures globally
@ExceptionHandler(MethodArgumentNotValidException.class)
public ResponseEntity<Map<String, String>> handleValidationErrors(
        MethodArgumentNotValidException ex) {
    Map<String, String> errors = new HashMap<>();
    ex.getBindingResult().getFieldErrors()
        .forEach(error -> errors.put(error.getField(), error.getDefaultMessage()));
    return ResponseEntity.badRequest().body(errors);
}
```

---

### Q60. What is CORS and how do you configure it in Spring Boot?

**Analogy:** CORS is like a building's visitor policy. Your browser (visitor) comes from Domain A and tries to access an API on Domain B. By default, the building (browser) blocks this unless Domain B explicitly says "visitors from Domain A are welcome."

```java
// Option 1 — On a specific controller or method
@CrossOrigin(origins = "https://myfrontend.com")
@RestController
public class UserController { ... }

// Option 2 — Global configuration (preferred)
@Configuration
public class CorsConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
            .allowedOrigins("https://myfrontend.com", "http://localhost:3000")
            .allowedMethods("GET", "POST", "PUT", "DELETE")
            .allowedHeaders("*")
            .allowCredentials(true)
            .maxAge(3600);
    }
}
```

---


---

<details>
<summary><strong>✅ Check Yourself — Part 6: Security, REST & Profiles</strong></summary>

1. Draw the complete JWT authentication flow from login to secured endpoint access.
2. What is the Spring Security filter chain? Name at least 4 filters in order.
3. How do profiles work and what is the activation priority order?
4. What is the difference between `@Controller` and `@RestController`?
5. When would you use `@PathVariable` vs `@RequestParam`?
6. How do you configure CORS globally in Spring Boot?
7. How does `@Valid` / `@Validated` trigger bean validation?

</details>

> **🔥 Real-World Interview Scenario — Google**
> *"You're building a multi-tenant SaaS API with Spring Boot. Each tenant has different rate limits and feature flags. How do you design the request processing pipeline?"*
>
> **Answer:** Use a custom `HandlerInterceptor` (or filter) that extracts the tenant ID from the JWT claims or a custom header. Look up tenant config from a cache-backed service. Use `@Scope("request")` beans to hold tenant context. Apply rate limiting per-tenant using a `RateLimiter` bean resolved by tenant ID. Feature flags can use `@ConditionalOnProperty` for static flags or a runtime feature flag service (LaunchDarkly/Unleash) for dynamic ones.

> **📚 Deep Dive — Read More (Part 6)**
> - [Spring Security Architecture — Official](https://docs.spring.io/spring-security/reference/servlet/architecture.html)
> - [JWT with Spring Boot — Baeldung](https://www.baeldung.com/spring-security-oauth-jwt)
> - [Spring Boot Profiles — Baeldung](https://www.baeldung.com/spring-profiles)
> - [REST API Best Practices — Baeldung](https://www.baeldung.com/rest-with-spring-series)


## Additional High-Value Interview Questions


> **🎯 FAANG Interview Tip — Advanced Topics**
> Questions Q61–Q100 are what separate senior candidates from mid-level. At Amazon and Apple, you might be asked to design a custom Spring Boot starter from scratch (Q97), explain the difference between `@Component` and `@Configuration` proxy mode (Q98), or implement rate limiting (Q94). These topics demonstrate architectural maturity.

```
┌─────────────────────────────────────────────────────────────────┐
│           SPRING MVC REQUEST PROCESSING FLOW                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Client HTTP Request                                            │
│       │                                                         │
│       ▼                                                         │
│  ┌──────────────────┐                                           │
│  │ DispatcherServlet│  (front controller)                       │
│  └────────┬─────────┘                                           │
│           ▼                                                     │
│  ┌──────────────────┐    Which controller handles this URL?     │
│  │ HandlerMapping   │────────────────────────────┐              │
│  └────────┬─────────┘                            │              │
│           ▼                                      │              │
│  ┌──────────────────┐                            │              │
│  │ HandlerAdapter   │  Invokes the right method  │              │
│  └────────┬─────────┘                            │              │
│           ▼                                      │              │
│  ┌──────────────────┐                            │              │
│  │ Controller       │  @GetMapping, @PostMapping │              │
│  │ (@RestController)│  Business logic here       │              │
│  └────────┬─────────┘                            │              │
│           ▼                                      │              │
│  ┌──────────────────┐                            │              │
│  │ HttpMessage      │  Jackson converts          │              │
│  │ Converter        │  Object → JSON             │              │
│  └────────┬─────────┘                            │              │
│           ▼                                      │              │
│  HTTP Response (JSON)                            │              │
│                                                  │              │
│  (For @Controller with view:)                    │              │
│  Controller → ViewResolver → View → HTML         │              │
│                                                                 │
│                                                                 │
│   MICROSERVICES ARCHITECTURE WITH SPRING BOOT                   │
│                                                                 │
│   ┌──────────┐     ┌──────────────────────────┐                │
│   │  Client  │────▶│    API Gateway            │                │
│   └──────────┘     │  (Spring Cloud Gateway)   │                │
│                    └─────────┬────────────────┘                 │
│                              │                                  │
│              ┌───────────────┼───────────────┐                  │
│              ▼               ▼               ▼                  │
│     ┌──────────────┐ ┌────────────┐ ┌──────────────┐           │
│     │ Order Service│ │User Service│ │Payment Svc   │           │
│     │ (Spring Boot)│ │(Spring Boot│ │(Spring Boot) │           │
│     └──────┬───────┘ └─────┬──────┘ └──────┬───────┘           │
│            │               │               │                    │
│            └───────────────┼───────────────┘                    │
│                            ▼                                    │
│                   ┌──────────────────┐                          │
│                   │ Service Registry │                          │
│                   │ (Eureka Server)  │                          │
│                   └──────────────────┘                          │
│                                                                 │
│   Cross-cutting: Config Server │ Circuit Breaker │ Zipkin       │
│                                                                 │
│                                                                 │
│   CIRCUIT BREAKER STATE MACHINE (Resilience4j)                  │
│                                                                 │
│      ┌──────────┐  failure rate    ┌──────────┐                 │
│      │  CLOSED  │ ─── exceeds ───▶ │   OPEN   │                │
│      │ (normal) │   threshold      │ (reject) │                │
│      └──────────┘                  └────┬─────┘                │
│           ▲                             │                       │
│           │                        wait duration                │
│      success                            │                       │
│      rate OK                            ▼                       │
│           │                    ┌───────────────┐                │
│           └────────────────────│  HALF-OPEN    │                │
│                                │ (trial calls) │                │
│                                └───────────────┘                │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```



---

### Q61. What is the difference between `application.properties` and `application.yml`?

Both serve the same purpose but differ in syntax. YAML supports nested structures more cleanly:

```properties
# application.properties
spring.datasource.url=jdbc:mysql://localhost/mydb
spring.datasource.username=root
spring.jpa.show-sql=true
```

```yaml
# application.yml
spring:
  datasource:
    url: jdbc:mysql://localhost/mydb
    username: root
  jpa:
    show-sql: true
```

YAML is more readable for nested configs. Properties is simpler for flat configs.

---

### Q62. What is Spring Boot DevTools?

DevTools is a developer-experience module that enables:
- **Automatic restart** — app restarts when classpath files change (faster than full restarts)
- **LiveReload** — browser auto-refreshes on static resource changes
- **Disabled caching** — Thymeleaf/FreeMarker caching is turned off in development

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```

DevTools is automatically excluded from production builds.

---

### Q63. What is `spring-boot-starter-parent`?

`spring-boot-starter-parent` is a special Maven parent POM that:
- Manages dependency versions (no need to specify versions for Spring Boot dependencies)
- Configures default Java version, encoding, and resource filtering
- Sets up the Maven plugin for building executable JARs

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.0</version>
</parent>
```

---

### Q64. How do you change the default embedded server from Tomcat to Jetty?

```xml
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
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```

---

### Q65. What is the difference between `@SpringBootTest(webEnvironment = ...)` options?

| Option | Description |
|---|---|
| `MOCK` (default) | Loads context but uses a mock servlet env — no real HTTP |
| `RANDOM_PORT` | Starts a real embedded server on a random port |
| `DEFINED_PORT` | Starts a real server on the port in `application.properties` |
| `NONE` | Loads context without any servlet environment |

---

### Q66. How does Spring Boot handle logging?

Spring Boot uses **SLF4J** as the API and **Logback** as the default implementation.

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@Service
public class UserService {
    private static final Logger log = LoggerFactory.getLogger(UserService.class);

    public User findUser(Long id) {
        log.debug("Looking up user with id: {}", id);
        log.info("User found: {}", id);
        log.warn("User not found, returning empty");
        log.error("Database error occurred", exception);
    }
}
```

```properties
# Configure log levels
logging.level.root=INFO
logging.level.com.myapp=DEBUG
logging.level.org.hibernate.SQL=DEBUG

# Log to file
logging.file.name=logs/application.log
```

---

### Q67. What is `@Scheduled`? How do you use it?

`@Scheduled` enables background task scheduling. Enable it with `@EnableScheduling` on a configuration class:

```java
@Configuration
@EnableScheduling
public class SchedulerConfig { }

@Component
public class ReportScheduler {

    // Run every 5 seconds
    @Scheduled(fixedDelay = 5000)
    public void generateReport() {
        System.out.println("Generating report at: " + LocalDateTime.now());
    }

    // Run every day at midnight using cron expression
    @Scheduled(cron = "0 0 0 * * ?")
    public void dailyCleanup() {
        System.out.println("Daily cleanup running...");
    }

    // Run at fixed rate regardless of execution time
    @Scheduled(fixedRate = 60000) // Every 60 seconds
    public void heartbeat() {
        System.out.println("Heartbeat ping...");
    }
}
```

---

### Q68. What is `@Async` in Spring Boot?

`@Async` runs a method in a background thread, freeing the caller to continue.

```java
@Configuration
@EnableAsync
public class AsyncConfig { }

@Service
public class EmailService {

    @Async
    public CompletableFuture<String> sendEmail(String to) {
        // This runs in a separate thread pool
        Thread.sleep(3000); // Simulate slow email send
        return CompletableFuture.completedFuture("Email sent to: " + to);
    }
}

// Controller doesn't wait for email to finish
@PostMapping("/register")
public ResponseEntity<String> register(@RequestBody UserRequest request) {
    userService.save(request);
    emailService.sendEmail(request.getEmail()); // Non-blocking
    return ResponseEntity.ok("User registered successfully");
}
```

---

### Q69. What is AOP (Aspect-Oriented Programming) in Spring Boot?

**Analogy:** AOP is like a security camera system in a building. The camera (aspect) monitors all activity (methods) without the individual employees (code) being aware of it. Security, logging, and monitoring are cross-cutting concerns handled outside the main code.

```java
@Aspect
@Component
public class LoggingAspect {

    // Before any method in the service layer
    @Before("execution(* com.myapp.service.*.*(..))")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("Calling: " + joinPoint.getSignature().getName());
    }

    // After method execution, regardless of outcome
    @AfterReturning(
        pointcut = "execution(* com.myapp.service.*.*(..))",
        returning = "result"
    )
    public void logAfterReturning(Object result) {
        System.out.println("Method returned: " + result);
    }

    // Around advice — full control
    @Around("execution(* com.myapp.service.*.*(..))")
    public Object measureTime(ProceedingJoinPoint pjp) throws Throwable {
        long start = System.currentTimeMillis();
        Object result = pjp.proceed();
        long end = System.currentTimeMillis();
        System.out.println("Execution time: " + (end - start) + "ms");
        return result;
    }
}
```

---

### Q70. What is the difference between `@RestController` and returning `ResponseEntity`?

```java
// Without ResponseEntity — simple but no control over status code
@GetMapping("/user/{id}")
public User getUser(@PathVariable Long id) {
    return userService.findById(id);
}

// With ResponseEntity — full control over status, headers, body
@GetMapping("/user/{id}")
public ResponseEntity<User> getUser(@PathVariable Long id) {
    return userService.findById(id)
        .map(user -> ResponseEntity.ok(user))
        .orElse(ResponseEntity.notFound().build());
}

// POST with 201 Created
@PostMapping("/users")
public ResponseEntity<User> createUser(@RequestBody UserRequest request) {
    User saved = userService.save(request);
    URI location = URI.create("/users/" + saved.getId());
    return ResponseEntity.created(location).body(saved);
}
```

---

### Q71. What is Spring Boot's `@ConditionalOnProperty`?

Enables/disables a bean based on a property value:

```java
@Configuration
public class FeatureConfig {

    @Bean
    @ConditionalOnProperty(name = "feature.notifications.enabled", havingValue = "true")
    public NotificationService notificationService() {
        return new NotificationService();
    }
}
```

```properties
feature.notifications.enabled=true
```

---

### Q72. What is Caching in Spring Boot?

```java
@SpringBootApplication
@EnableCaching
public class MyApplication { ... }

@Service
public class ProductService {

    @Cacheable(value = "products", key = "#id")
    public Product findById(Long id) {
        // DB query only runs if result isn't cached
        return productRepository.findById(id).orElseThrow();
    }

    @CacheEvict(value = "products", key = "#id")
    public void updateProduct(Long id, Product product) {
        productRepository.save(product);
        // Cache cleared for this key
    }

    @CachePut(value = "products", key = "#result.id")
    public Product createProduct(Product product) {
        return productRepository.save(product); // Updates cache
    }
}
```

By default, Spring Boot uses a `ConcurrentHashMap` as the cache. For production, integrate Redis:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

---

### Q73. What is the difference between `save()` and `saveAndFlush()` in JPA?

| Method | Behaviour |
|---|---|
| `save()` | Writes to persistence context (in-memory) — flushed to DB at end of transaction |
| `saveAndFlush()` | Immediately writes to DB (bypasses batching) |

Use `saveAndFlush()` when you need the DB to be updated immediately — e.g., when another part of the same transaction needs to read what you just wrote.

---

### Q74. What is Flyway and how is it used in Spring Boot?

**Analogy:** Flyway is like Git for your database schema — every change is versioned and applied in order.

```xml
<dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
</dependency>
```

```sql
-- src/main/resources/db/migration/V1__create_users_table.sql
CREATE TABLE users (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL
);

-- V2__add_phone_column.sql
ALTER TABLE users ADD COLUMN phone VARCHAR(20);
```

Spring Boot auto-detects Flyway on the classpath and runs migrations at startup.

---

### Q75. What is the difference between `@Entity`, `@Table`, `@Column`?

```java
@Entity                           // Marks this class as a JPA entity
@Table(name = "tbl_users")        // Maps to the database table "tbl_users"
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "full_name", nullable = false, length = 100)
    private String name;          // Maps to "full_name" column

    @Column(unique = true)
    private String email;

    @Transient                    // Not persisted to DB
    private String temporaryToken;
}
```

---

### Q76. What is `@OneToMany` and `@ManyToOne`? How do you model them?

```java
// One Department has many Employees
@Entity
public class Department {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @OneToMany(mappedBy = "department", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private List<Employee> employees;
}

@Entity
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @ManyToOne
    @JoinColumn(name = "department_id")
    private Department department;
}
```

**FetchType.LAZY** — load employees only when accessed (preferred for performance).
**FetchType.EAGER** — load employees immediately with the department (can cause performance issues).

---

### Q77. What is `@ManyToMany`?

```java
@Entity
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @ManyToMany
    @JoinTable(
        name = "student_course",
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id")
    )
    private Set<Course> courses;
}

@Entity
public class Course {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String title;

    @ManyToMany(mappedBy = "courses")
    private Set<Student> students;
}
```

---

### Q78. What is `@EnableAutoConfiguration` and how is it different from `@SpringBootApplication`?

`@EnableAutoConfiguration` is a component of `@SpringBootApplication`. It tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings.

`@SpringBootApplication` = `@Configuration` + `@EnableAutoConfiguration` + `@ComponentScan`.

You'd use `@EnableAutoConfiguration` alone only if you need very precise control over which annotations are applied.

---

### Q79. How do you externalise configuration using environment variables?

Spring Boot automatically maps environment variables to properties. Use underscores for dots and uppercase for letters:

```bash
# Environment variable
export SPRING_DATASOURCE_URL=jdbc:mysql://prod:3306/mydb
export SPRING_DATASOURCE_PASSWORD=supersecret
```

These override `application.properties` values. The property resolution order (highest wins):
1. Command-line arguments
2. Environment variables
3. `application-{profile}.properties`
4. `application.properties`

---

### Q80. What is Spring Boot's embedded server and how do you configure its port?

Spring Boot includes Tomcat by default. To change port:

```properties
server.port=9090

# Run on a random available port (useful in tests)
server.port=0
```

To access the actual port when using random:
```java
@Autowired
Environment environment;

int port = Integer.parseInt(environment.getProperty("local.server.port"));
```

---

### Q81. What is `@EnableJpaRepositories`?

Enables JPA repositories in a specific package. Usually not needed when using `@SpringBootApplication` (which triggers auto-configuration), but useful when you need custom configuration:

```java
@Configuration
@EnableJpaRepositories(basePackages = "com.myapp.repositories")
@EntityScan(basePackages = "com.myapp.entities")
public class JpaConfig { }
```

---

### Q82. What are the differences between `PATCH` and `PUT` in REST APIs?

| Method | Semantics |
|---|---|
| `PUT` | Replace the entire resource |
| `PATCH` | Partially update the resource |

```java
// PUT — must include all fields
@PutMapping("/users/{id}")
public User updateUser(@PathVariable Long id, @RequestBody User user) {
    user.setId(id);
    return userRepository.save(user);
}

// PATCH — only include fields to update
@PatchMapping("/users/{id}")
public User patchUser(@PathVariable Long id, @RequestBody Map<String, Object> updates) {
    User user = userRepository.findById(id).orElseThrow();
    if (updates.containsKey("email")) user.setEmail((String) updates.get("email"));
    if (updates.containsKey("name")) user.setName((String) updates.get("name"));
    return userRepository.save(user);
}
```

---

### Q83. What is a `Filter` vs `Interceptor` in Spring Boot?

| | Filter (Servlet) | Interceptor (Spring) |
|---|---|---|
| Level | Servlet container | Spring MVC |
| Access to Spring beans | Limited | Full |
| Applied to | All requests | Only Spring MVC requests |
| Interface | `javax.servlet.Filter` | `HandlerInterceptor` |

```java
// Filter — runs before Spring even sees the request
@Component
public class RequestLoggingFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        System.out.println("Request received at: " + LocalDateTime.now());
        chain.doFilter(request, response);
    }
}

// Interceptor — runs within Spring MVC
@Component
public class AuthInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
                             Object handler) throws Exception {
        // Return false to abort the request
        return validateToken(request.getHeader("Authorization"));
    }
}
```

---

### Q84. What is the role of `@EnableWebSecurity`?

`@EnableWebSecurity` activates Spring Security's web security support and provides the Spring MVC integration. In Spring Boot 3.x, simply having the security dependency on the classpath triggers auto-configuration, but `@EnableWebSecurity` on a `@Configuration` class gives you full control.

---

### Q85. How do you run code at specific events in the Spring lifecycle?

```java
@Component
public class AppEventListener {

    // Fires when ApplicationContext is fully loaded
    @EventListener(ApplicationReadyEvent.class)
    public void onApplicationReady() {
        System.out.println("Application is ready to serve!");
    }

    // Fires when context starts refreshing
    @EventListener(ContextRefreshedEvent.class)
    public void onContextRefreshed() {
        System.out.println("Context refreshed!");
    }

    // Custom event
    @EventListener
    public void handleUserCreated(UserCreatedEvent event) {
        System.out.println("New user: " + event.getUser().getEmail());
    }
}
```

---

### Q86. What is Spring Boot's `@ConditionalOnMissingBean`?

Registers a bean only if no bean of that type already exists — the pattern used throughout auto-configuration:

```java
@Bean
@ConditionalOnMissingBean(DataSource.class) // Only auto-configure if user hasn't defined their own
public DataSource defaultDataSource() {
    return new EmbeddedDatabaseBuilder().setType(H2).build();
}
```

This is why defining your own `DataSource` bean causes Spring Boot to back off.

---

### Q87. What is `@Transient` in JPA?

Marks a field that should NOT be persisted to the database:

```java
@Entity
public class Product {
    @Id
    private Long id;
    private double price;
    private double taxRate;

    @Transient
    private double totalPrice; // Calculated, not stored

    public double getTotalPrice() {
        return price * (1 + taxRate);
    }
}
```

---

### Q88. What is circuit breaking and how does it relate to Spring Boot microservices?

**Analogy:** A circuit breaker in electrical systems trips when there's an overload, preventing damage. In microservices, if Service B is failing, the circuit breaker in Service A stops calling it (trips open), returns a fallback, and periodically tests if B has recovered.

Spring Boot integrates with **Resilience4j** (successor to Netflix Hystrix):

```xml
<dependency>
    <groupId>io.github.resilience4j</groupId>
    <artifactId>resilience4j-spring-boot3</artifactId>
</dependency>
```

```java
@Service
public class InventoryService {

    @CircuitBreaker(name = "inventoryService", fallbackMethod = "fallbackInventory")
    @Retry(name = "inventoryService")
    public InventoryResponse checkStock(String productCode) {
        return inventoryClient.getInventory(productCode); // Might fail
    }

    public InventoryResponse fallbackInventory(String productCode, Throwable t) {
        return new InventoryResponse(productCode, 0, "Service unavailable — try again later");
    }
}
```

---

### Q89. What is service discovery in microservices with Spring Boot?

With Spring Cloud Netflix Eureka:

```java
// Eureka Server
@SpringBootApplication
@EnableEurekaServer
public class DiscoveryServer { ... }

// Eureka Client
@SpringBootApplication
@EnableDiscoveryClient
public class UserService { ... }
```

```properties
# User service registers itself
eureka.client.service-url.defaultZone=http://localhost:8761/eureka/
spring.application.name=user-service
```

Services discover each other by name, not by IP — enabling horizontal scaling.

---

### Q90. What is an API Gateway in Spring Boot microservices?

Spring Cloud Gateway acts as the single entry point for all client requests, routing them to the appropriate microservice:

```java
@SpringBootApplication
@EnableDiscoveryClient
public class ApiGateway { }
```

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: user-service
          uri: lb://USER-SERVICE    # lb:// = load balanced via Eureka
          predicates:
            - Path=/api/users/**
          filters:
            - StripPrefix=1
        - id: order-service
          uri: lb://ORDER-SERVICE
          predicates:
            - Path=/api/orders/**
```

---

### Q91. What is `@Cacheable` vs `@CacheEvict` vs `@CachePut`?

| Annotation | Behaviour |
|---|---|
| `@Cacheable` | Returns cached result if exists; else calls method and caches result |
| `@CacheEvict` | Removes entry from cache |
| `@CachePut` | Always calls method AND updates the cache |

---

### Q92. What is the difference between `Optional.orElse()` and `Optional.orElseGet()`?

```java
// orElse — default value always evaluated (even if not needed)
User user = userRepository.findById(id).orElse(new User("default")); // Calls new User() always

// orElseGet — default value only evaluated when needed (lazy)
User user = userRepository.findById(id).orElseGet(() -> new User("default")); // Only when absent
```

Prefer `orElseGet()` when the default is expensive to create.

---

### Q93. What is the difference between Spring MVC and Spring WebFlux?

| Feature | Spring MVC | Spring WebFlux |
|---|---|---|
| Model | Blocking / Synchronous | Non-blocking / Reactive |
| Thread per request? | Yes | No |
| Server | Tomcat | Netty (or Tomcat in reactive mode) |
| Data types | Regular objects | `Mono<T>`, `Flux<T>` |
| Best for | Standard CRUD apps | High-concurrency, streaming, microservices |

```java
// Spring WebFlux example
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping
    public Flux<User> getAllUsers() {
        return userRepository.findAll(); // Returns stream, not blocking list
    }

    @GetMapping("/{id}")
    public Mono<User> getUser(@PathVariable Long id) {
        return userRepository.findById(id); // Reactive single item
    }
}
```

---

### Q94. How do you implement rate limiting in Spring Boot?

Using Resilience4j's rate limiter or at the API Gateway level:

```java
@RateLimiter(name = "basicRateLimit", fallbackMethod = "rateLimitFallback")
@GetMapping("/limited")
public String limitedEndpoint() {
    return "This endpoint is rate-limited";
}

public String rateLimitFallback(RequestNotPermitted ex) {
    return "Too many requests — please try again later";
}
```

```properties
resilience4j.ratelimiter.instances.basicRateLimit.limit-for-period=10
resilience4j.ratelimiter.instances.basicRateLimit.limit-refresh-period=1s
resilience4j.ratelimiter.instances.basicRateLimit.timeout-duration=0
```

---

### Q95. What is `@Transactional(readOnly = true)` and when should you use it?

```java
@Transactional(readOnly = true)
public List<User> findAllUsers() {
    return userRepository.findAll();
    // Hibernate skips dirty checking — significant performance gain
}
```

Use `readOnly = true` for all query-only methods. Benefits:
- Hibernate disables dirty checking and snapshot recording
- Some databases / JDBC drivers can apply read optimisations
- Clear code signal — this method will not modify data

---

### Q96. What is Spring Boot's auto-reload / hot swap?

Two mechanisms:
1. **DevTools automatic restart** — uses two classloaders; restarts only your code classes, keeping Spring's classloader intact (faster than full restart)
2. **JRebel** — commercial tool for true hot-swap without restart

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
```

---

### Q97. How do you create a custom Spring Boot starter?

A custom starter packages your reusable auto-configuration into a library:

```
my-custom-starter/
├── my-custom-autoconfigure/
│   └── src/main/resources/
│       └── META-INF/spring/
│           └── org.springframework.boot.autoconfigure.AutoConfiguration.imports
└── my-custom-starter/
    └── pom.xml (depends on autoconfigure module)
```

```java
// Auto-configuration class
@AutoConfiguration
@ConditionalOnClass(MyLibraryService.class)
@EnableConfigurationProperties(MyLibraryProperties.class)
public class MyLibraryAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean
    public MyLibraryService myLibraryService(MyLibraryProperties props) {
        return new MyLibraryService(props.getApiKey());
    }
}
```

---

### Q98. What is the difference between `@Component` and `@Configuration`?

| | `@Component` | `@Configuration` |
|---|---|---|
| Purpose | Register a single bean | Declare multiple bean definitions |
| `@Bean` methods | Can have, but no proxying | Full proxy — ensures singleton semantics |
| CGLIB proxy? | No | Yes |

```java
@Configuration
public class AppConfig {

    @Bean
    public ServiceA serviceA() {
        return new ServiceA(serviceB()); // serviceB() is proxied — returns same instance
    }

    @Bean
    public ServiceB serviceB() {
        return new ServiceB();
    }
}
```

Without `@Configuration` (using `@Component`), calling `serviceB()` inside `serviceA()` would create a *new* `ServiceB` instance, breaking singleton behaviour.

---

### Q99. What is `@EnableConfigurationProperties`?

Registers a `@ConfigurationProperties` class as a Spring bean without needing `@Component` on it:

```java
@ConfigurationProperties(prefix = "app.mail")
public class MailProperties {
    private String host;
    private int port;
    // getters/setters
}

@Configuration
@EnableConfigurationProperties(MailProperties.class) // Registers it
public class MailConfig {
    // MailProperties can now be @Autowired
}
```

---

### Q100. What is Spring Boot Actuator's `/health` endpoint and how do you customise it?

The `/health` endpoint reports application health to load balancers, Kubernetes liveness probes, and monitoring systems.

```properties
management.endpoint.health.show-details=always
management.health.db.enabled=true
management.health.diskspace.enabled=true
```

Custom health indicator:
```java
@Component
public class PaymentGatewayHealthIndicator implements HealthIndicator {

    @Autowired
    private PaymentGatewayClient client;

    @Override
    public Health health() {
        boolean isUp = client.ping();
        return isUp
            ? Health.up().withDetail("gateway", "Stripe is reachable").build()
            : Health.down().withDetail("gateway", "Stripe is unreachable").build();
    }
}
```

Response:
```json
{
  "status": "UP",
  "components": {
    "db": { "status": "UP" },
    "paymentGateway": { "status": "UP", "details": { "gateway": "Stripe is reachable" } },
    "diskSpace": { "status": "UP" }
  }
}
```

---


---

<details>
<summary><strong>✅ Check Yourself — Advanced Topics (Q61–Q100)</strong></summary>

1. What is the difference between `application.properties` and `application.yml`?
2. How does Spring Boot DevTools enable hot reload?
3. Explain AOP: what is a pointcut, advice, and aspect?
4. What are the three states of a circuit breaker?
5. How does Eureka service discovery work?
6. What does an API Gateway do in a microservices architecture?
7. Explain the difference between `@Cacheable`, `@CacheEvict`, and `@CachePut`.
8. What is the difference between Spring MVC and Spring WebFlux?
9. How do you create a custom Spring Boot starter?
10. What is the difference between `@Component` and `@Configuration` (CGLIB proxy)?

</details>

> **🔥 Real-World Interview Scenario — Netflix**
> *"You're building a Spring Boot microservices system with 15 services. Service A calls Service B, which calls Service C. Service C is experiencing intermittent failures. How do you prevent cascading failures?"*
>
> **Answer:**
> 1. **Circuit Breaker** (Resilience4j) on Service B's call to C — stops hammering a failing service
> 2. **Fallback method** — return cached/default data when circuit is open
> 3. **Timeout** — don't let B wait forever for C (e.g., 2s timeout)
> 4. **Bulkhead** — isolate C calls into a separate thread pool so B's other work isn't blocked
> 5. **Retry with backoff** — for transient failures in HALF_OPEN state
> 6. **Health check** — Actuator `/health` exposes circuit state to monitoring
>
> ```java
> @CircuitBreaker(name = "serviceC", fallbackMethod = "fallback")
> @Retry(name = "serviceC")
> @TimeLimiter(name = "serviceC")
> public CompletableFuture<Response> callServiceC(Request req) {
>     return CompletableFuture.supplyAsync(() -> restTemplate.getForObject(...));
> }
> public CompletableFuture<Response> fallback(Request req, Throwable t) {
>     return CompletableFuture.completedFuture(cachedResponse);
> }
> ```

> **📚 Deep Dive — Read More (Advanced Topics)**
> - [Circuit Breaker with Resilience4j — Baeldung](https://www.baeldung.com/spring-cloud-circuit-breaker)
> - [Spring Cloud Gateway — Official](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/)
> - [Service Discovery with Eureka — Baeldung](https://www.baeldung.com/spring-cloud-netflix-eureka)
> - [Spring WebFlux Guide — Baeldung](https://www.baeldung.com/spring-webflux)
> - [Caching in Spring Boot — Baeldung](https://www.baeldung.com/spring-cache-tutorial)
> - [Creating a Custom Starter — Baeldung](https://www.baeldung.com/spring-boot-custom-starter)


## Final Quiz

Test your knowledge! Answer all questions, then check your score using the answer key at the bottom.

---

### Section A — Multiple Choice (Questions 1–25)

**1.** Which annotation is a combination of `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan`?

- A) `@EnableSpringBoot`
- B) `@SpringBootApplication`
- C) `@BootstrapApplication`
- D) `@SpringApplication`

---

**2.** What is the default port of a Spring Boot embedded Tomcat server?

- A) 80
- B) 443
- C) 8080
- D) 9090

---

**3.** Which interface do you implement to run code after the Spring Boot application has fully started?

- A) `ApplicationInitializer`
- B) `SpringRunner`
- C) `CommandLineRunner`
- D) `StartupHook`

---

**4.** Which bean scope creates a new instance every time the bean is requested from the container?

- A) `singleton`
- B) `session`
- C) `prototype`
- D) `request`

---

**5.** What is the purpose of `@MockBean` in Spring Boot tests?

- A) It mocks HTTP endpoints
- B) It creates a Mockito mock and registers it as a Spring bean in the test context
- C) It replaces `@Autowired` in test classes
- D) It mocks database connections

---

**6.** Which annotation would you use to prevent a specific auto-configuration class from loading?

- A) `@DisableAutoConfig`
- B) `@IgnoreAutoConfig`
- C) `@SpringBootApplication(exclude = {...})`
- D) `@SuppressAutoConfig`

---

**7.** What does `@Transactional(readOnly = true)` do?

- A) Makes the database connection read-only permanently
- B) Allows only SELECT statements to run
- C) Disables Hibernate's dirty checking and snapshot — optimises read operations
- D) Throws an exception if a write operation is attempted

---

**8.** Which of the following is NOT a valid Spring bean scope?

- A) `singleton`
- B) `prototype`
- C) `global`
- D) `session`

---

**9.** What does the Actuator `/health` endpoint return when all components are working?

- A) `{"state": "RUNNING"}`
- B) `{"status": "UP"}`
- C) `{"health": "OK"}`
- D) `{"alive": true}`

---

**10.** Which type of Dependency Injection is considered best practice in Spring Boot?

- A) Field Injection (`@Autowired` on field)
- B) Setter Injection
- C) Constructor Injection
- D) Interface Injection

---

**11.** What is the correct file location for database migration scripts when using Flyway?

- A) `src/main/resources/migrations/`
- B) `src/main/resources/db/migration/`
- C) `src/main/resources/flyway/`
- D) `src/main/java/migrations/`

---

**12.** What annotation would you use to map HTTP request body JSON to a Java object in a controller method?

- A) `@RequestParam`
- B) `@PathVariable`
- C) `@RequestBody`
- D) `@RequestData`

---

**13.** Which Spring Boot test annotation loads ONLY the web layer (controllers, filters, etc.)?

- A) `@SpringBootTest`
- B) `@DataJpaTest`
- C) `@WebMvcTest`
- D) `@IntegrationTest`

---

**14.** What does `@Cacheable` do when the key already exists in the cache?

- A) Throws a `CacheException`
- B) Calls the method and overwrites the cache
- C) Returns the cached value without calling the method
- D) Deletes the old cached value and returns null

---

**15.** What is the purpose of `@ControllerAdvice`?

- A) Provides advice to developers via IDE
- B) Implements AOP around all controllers
- C) Provides centralized exception handling across all controllers
- D) Injects a custom view resolver

---

**16.** Which HTTP method is semantically correct for partially updating a resource?

- A) `POST`
- B) `PUT`
- C) `PATCH`
- D) `UPDATE`

---

**17.** What annotation enables Spring's caching mechanism?

- A) `@CacheEnabled`
- B) `@EnableCaching`
- C) `@CacheOn`
- D) `@ActivateCache`

---

**18.** Which propagation type ensures a method ALWAYS runs in its own NEW transaction, suspending any existing one?

- A) `REQUIRED`
- B) `SUPPORTS`
- C) `MANDATORY`
- D) `REQUIRES_NEW`

---

**19.** What does `@Profile("dev")` on a `@Bean` method mean?

- A) The bean is only available in development IDEs
- B) The bean is created only when the `dev` profile is active
- C) The bean runs development mode checks
- D) The bean is excluded from production builds automatically

---

**20.** Which annotation lifecycle runs immediately after dependency injection on the bean?

- A) `@AfterInit`
- B) `@InitBean`
- C) `@PostConstruct`
- D) `@BeanReady`

---

**21.** In JPA, what does `FetchType.LAZY` mean?

- A) Related entities are always loaded with the parent
- B) Related entities are loaded only when explicitly accessed
- C) Related entities are never loaded
- D) Loading is delayed by 5 seconds

---

**22.** What is the correct way to activate a Spring profile when running a JAR?

- A) `java -jar app.jar -profile=prod`
- B) `java -jar app.jar --spring.profiles.active=prod`
- C) `java -spring:profiles=prod -jar app.jar`
- D) `java -jar app.jar -Dprofile=prod`

---

**23.** Which annotation would you use to exclude a field from being persisted to the database in a JPA entity?

- A) `@Exclude`
- B) `@Ignore`
- C) `@Transient`
- D) `@NotPersisted`

---

**24.** What is the N+1 problem in JPA?

- A) Running N queries instead of 1 for a single entity
- B) Fetching 1 entity and then making N separate queries for its relationships
- C) Having N database connections open at once
- D) Loading N entities more than once from the same query

---

**25.** What does `@ConditionalOnMissingBean` do?

- A) Creates a bean only if no other beans exist in the context
- B) Creates a bean only if a bean of the specified type does NOT already exist
- C) Prevents a bean from being auto-wired if it's missing
- D) Throws an exception if the required bean is missing

---

### Section B — True or False (Questions 26–40)

**26.** `@RestController` is equivalent to `@Controller` + `@ResponseBody`. ___

**27.** `prototype`-scoped beans are destroyed by the Spring container at shutdown. ___

**28.** `@DataJpaTest` uses an in-memory H2 database by default. ___

**29.** Spring Boot DevTools is included in production builds by default. ___

**30.** `@Primary` marks the default bean to inject when multiple candidates of the same type exist. ___

**31.** You must explicitly add `@Lazy` on every bean you want to initialise lazily — there is no global setting. ___

**32.** A `@Transactional` method that catches an exception internally will still roll back the transaction. ___

**33.** `application.yml` and `application.properties` can coexist in the same project. ___

**34.** `@PathVariable` is used to extract values from the HTTP query string. ___

**35.** The Flyway migration script naming convention is `V{version}__{description}.sql`. ___

**36.** `@EnableAutoConfiguration` is contained within `@SpringBootApplication`. ___

**37.** `@Async` methods run on the main application thread by default. ___

**38.** `@WebMvcTest` loads the full application context including JPA repositories. ___

**39.** Spring Security's `BCryptPasswordEncoder` is the recommended password encoder. ___

**40.** The `@Scheduled` annotation requires `@EnableScheduling` to be active somewhere in the application. ___

---

### Section C — Short Answer (Questions 41–50)

**41.** Name the three annotations that `@SpringBootApplication` is composed of.

**42.** What property would you set in `application.properties` to change the server port to 9090?

**43.** What is the difference between `@Cacheable` and `@CachePut`?

**44.** Name two ways to resolve bean ambiguity when multiple implementations of an interface exist.

**45.** What lifecycle annotation runs cleanup code before a bean is destroyed?

**46.** What does `proxyMode = ScopedProxyMode.TARGET_CLASS` enable when using request/session scope?

**47.** What command runs a Spring Boot app without packaging it first?

**48.** What is the purpose of `@Valid` in a controller method parameter?

**49.** What Spring Cloud component acts as the single-entry-point router for all microservice requests?

**50.** What interface must you implement to create a custom health check for Spring Boot Actuator?

---

### Answer Key

**Section A — Multiple Choice:**

| Q | Answer | Q | Answer |
|---|---|---|---|
| 1 | B | 14 | C |
| 2 | C | 15 | C |
| 3 | C | 16 | C |
| 4 | C | 17 | B |
| 5 | B | 18 | D |
| 6 | C | 19 | B |
| 7 | C | 20 | C |
| 8 | C | 21 | B |
| 9 | B | 22 | B |
| 10 | C | 23 | C |
| 11 | B | 24 | B |
| 12 | C | 25 | B |
| 13 | C | | |

**Section B — True or False:**

| Q | Answer | Q | Answer |
|---|---|---|---|
| 26 | True | 34 | False (`@PathVariable` is for URL path — `@RequestParam` is for query string) |
| 27 | False (Spring does not manage destruction of prototype beans) | 35 | True |
| 28 | True | 36 | True |
| 29 | False (DevTools is `optional` and excluded from prod builds) | 37 | False (`@Async` runs on a separate thread pool) |
| 30 | True | 38 | False (`@WebMvcTest` only loads the web layer) |
| 31 | False (`spring.main.lazy-initialization=true` applies globally) | 39 | True |
| 32 | False (If the exception is caught, the rollback won't happen unless you rethrow or use `TransactionAspectSupport.currentTransactionStatus().setRollbackOnly()`) | 40 | True |
| 33 | True | | |

**Section C — Short Answer:**

| Q | Answer |
|---|---|
| 41 | `@Configuration`, `@EnableAutoConfiguration`, `@ComponentScan` |
| 42 | `server.port=9090` |
| 43 | `@Cacheable` returns cached value without calling method if found. `@CachePut` always calls the method AND updates the cache. |
| 44 | `@Primary` on the preferred implementation; `@Qualifier("beanName")` at the injection point |
| 45 | `@PreDestroy` |
| 46 | It allows a request/session-scoped bean to be injected into a singleton — Spring creates a proxy that resolves the real bean per request/session |
| 47 | `mvn spring-boot:run` or `./gradlew bootRun` |
| 48 | Triggers JSR-303/Bean Validation on the object, applying constraints like `@NotBlank`, `@Email`, etc. |
| 49 | API Gateway (Spring Cloud Gateway) |
| 50 | `HealthIndicator` |

---

### Score Interpretation

| Score | Rating |
|---|---|
| 45–50 | Expert — you're ready for senior-level interviews |
| 38–44 | Strong — solid Spring Boot fundamentals with minor gaps |
| 28–37 | Intermediate — review the sections where you missed questions |
| Below 28 | Foundational — revisit the Q&A sections and practice with code |

---

*This document was compiled from JavaTechie's Spring Boot Interview Mastery Series (Parts 1–6) and supplemented with industry-standard interview questions from GeeksforGeeks, InterviewBit, Baeldung, and real-world enterprise Spring Boot patterns.*
