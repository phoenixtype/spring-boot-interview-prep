# Spring Boot Interview Prep

A comprehensive Spring Boot interview guide with 100 questions and detailed answers covering core foundations through microservices, based on JavaTechie's 6-part interview series plus additional high-value topics.

---

## Table of Contents

### Part 1 — Core Spring Boot Foundations
- [Q1. Why choose Spring Boot over Spring Framework?](spring-boot-interview-mastery.md#q1-why-would-you-choose-spring-boot-over-the-spring-framework)
- [Q2. Spring Boot Starters](spring-boot-interview-mastery.md#q2-what-spring-boot-starters-have-you-used-what-do-they-do)
- [Q3. Running a Spring Boot Application](spring-boot-interview-mastery.md#q3-how-do-you-run-a-spring-boot-application)
- [Q4. `@SpringBootApplication`](spring-boot-interview-mastery.md#q4-what-is-the-purpose-of-springbootapplication)
- [Q5. `@Configuration`, `@EnableAutoConfiguration`, `@ComponentScan`](spring-boot-interview-mastery.md#q5-can-you-use-configuration-enableautoconfiguration-and-componentscan-separately-instead-of-springbootapplication)
- [Q6. AutoConfiguration](spring-boot-interview-mastery.md#q6-what-is-autoconfiguration-in-spring-boot)
- [Q7. Disabling Auto-Configuration](spring-boot-interview-mastery.md#q7-how-do-you-disable-a-specific-auto-configuration-class)
- [Q8. Customizing Default Configuration](spring-boot-interview-mastery.md#q8-how-do-you-customize-the-default-configuration-in-spring-boot)
- [Q9. `SpringApplication.run()` Internals](spring-boot-interview-mastery.md#q9-how-does-springapplicationrun-work-internally)
- [Q10. `CommandLineRunner`](spring-boot-interview-mastery.md#q10-what-is-commandlinerunner-when-do-you-use-it)

### Part 2 — Dependency Injection & Bean Management
- [Q11. Dependency Injection](spring-boot-interview-mastery.md#q11-what-is-dependency-injection-di)
- [Q12. Three Types of DI](spring-boot-interview-mastery.md#q12-what-are-the-three-types-of-dependency-injection-in-spring)
- [Q13. IoC (Inversion of Control)](spring-boot-interview-mastery.md#q13-what-is-ioc-inversion-of-control)
- [Q14. `@Component` vs `@Service` vs `@Repository` vs `@Controller`](spring-boot-interview-mastery.md#q14-what-is-the-difference-between-component-service-repository-and-controller)
- [Q15. `@Autowired`](spring-boot-interview-mastery.md#q15-what-is-autowired-and-how-does-it-work)
- [Q16. Resolving Bean Ambiguity](spring-boot-interview-mastery.md#q16-what-happens-when-there-are-multiple-beans-of-the-same-type-how-do-you-resolve-ambiguity)
- [Q17. `@Bean` vs `@Component`](spring-boot-interview-mastery.md#q17-what-is-the-bean-annotation-how-is-it-different-from-component)
- [Q18. `ApplicationContext` vs `BeanFactory`](spring-boot-interview-mastery.md#q18-what-is-the-applicationcontext-vs-beanfactory)
- [Q19. `@Value`](spring-boot-interview-mastery.md#q19-what-is-value-and-how-do-you-use-it)
- [Q20. `@ConfigurationProperties`](spring-boot-interview-mastery.md#q20-what-is-configurationproperties-why-is-it-preferred-over-value-for-groups-of-properties)

### Part 3 — Bean Scopes & Lifecycle
- [Q21. Bean Scopes](spring-boot-interview-mastery.md#q21-what-are-the-different-bean-scopes-in-spring-boot)
- [Q22. Singleton Scope](spring-boot-interview-mastery.md#q22-what-is-singleton-scope-demonstrate-it)
- [Q23. Prototype Scope](spring-boot-interview-mastery.md#q23-what-is-prototype-scope-demonstrate-it)
- [Q24. Request Scope](spring-boot-interview-mastery.md#q24-what-is-request-scope)
- [Q25. Session Scope](spring-boot-interview-mastery.md#q25-what-is-session-scope)
- [Q26. Bean Lifecycle](spring-boot-interview-mastery.md#q26-what-is-the-bean-lifecycle-in-spring)
- [Q27. `@Lazy`](spring-boot-interview-mastery.md#q27-what-is-lazy-in-spring-boot)
- [Q28. `@Scope("prototype")` vs `@Lazy`](spring-boot-interview-mastery.md#q28-what-is-the-difference-between-scopeprototype-and-lazy)
- [Q29. `@Conditional`](spring-boot-interview-mastery.md#q29-what-is-conditional-give-a-real-world-example)
- [Q30. `BeanFactory` vs `ApplicationContext` Initialization](spring-boot-interview-mastery.md#q30-what-is-the-difference-between-beanfactory-and-applicationcontext-initialization)

### Part 4 — Data Access, JPA & Exception Handling
- [Q31. Spring Data JPA](spring-boot-interview-mastery.md#q31-what-is-spring-data-jpa)
- [Q32. `JpaRepository` vs `CrudRepository` vs `PagingAndSortingRepository`](spring-boot-interview-mastery.md#q32-what-is-the-difference-between-jparepository-crudrepository-and-pagingandsortingrepository)
- [Q33. Pagination and Sorting](spring-boot-interview-mastery.md#q33-how-do-you-implement-pagination-and-sorting)
- [Q34. `@Transactional`](spring-boot-interview-mastery.md#q34-what-is-transactional-how-does-it-work)
- [Q35. Transaction Propagation Types](spring-boot-interview-mastery.md#q35-what-are-transaction-propagation-types)
- [Q36. Exception Handling](spring-boot-interview-mastery.md#q36-what-is-exception-handling-in-spring-boot-how-do-you-implement-it)
- [Q37. `@ControllerAdvice` vs `@RestControllerAdvice`](spring-boot-interview-mastery.md#q37-what-is-the-difference-between-controlleradvice-and-restcontrolleradvice)
- [Q38. `@ResponseStatus`](spring-boot-interview-mastery.md#q38-what-is-responsestatus)
- [Q39. MySQL Configuration](spring-boot-interview-mastery.md#q39-how-do-you-configure-spring-boot-to-use-a-mysql-database)
- [Q40. N+1 Problem in JPA](spring-boot-interview-mastery.md#q40-what-is-the-n1-problem-in-jpa-and-how-do-you-fix-it)

### Part 5 — Testing & Actuator
- [Q41. Testing Annotations](spring-boot-interview-mastery.md#q41-what-testing-annotations-does-spring-boot-provide)
- [Q42. Unit Testing a Service](spring-boot-interview-mastery.md#q42-how-do-you-write-a-unit-test-for-a-spring-boot-service)
- [Q43. Testing Controllers with `@WebMvcTest`](spring-boot-interview-mastery.md#q43-how-do-you-test-a-rest-controller-using-webmvctest)
- [Q44. JPA Repository Testing](spring-boot-interview-mastery.md#q44-how-do-you-write-a-jpa-repository-test)
- [Q45. Spring Boot Actuator](spring-boot-interview-mastery.md#q45-what-is-spring-boot-actuator)
- [Q46. Key Actuator Endpoints](spring-boot-interview-mastery.md#q46-what-are-the-key-actuator-endpoints)
- [Q47. Custom Health Indicator](spring-boot-interview-mastery.md#q47-how-do-you-create-a-custom-health-indicator)
- [Q48. Actuator + Prometheus + Grafana](spring-boot-interview-mastery.md#q48-how-do-you-integrate-actuator-with-prometheus-and-grafana)
- [Q49. `@SpringBootTest`](spring-boot-interview-mastery.md#q49-what-is-springboottest-and-when-should-you-use-it)
- [Q50. `@MockBean`](spring-boot-interview-mastery.md#q50-what-is-the-mockbean-annotation)

### Part 6 — Security, REST API Design & Profiles
- [Q51. Spring Security](spring-boot-interview-mastery.md#q51-how-do-you-secure-a-spring-boot-application-with-spring-security)
- [Q52. JWT Authentication](spring-boot-interview-mastery.md#q52-how-do-you-implement-jwt-authentication-in-spring-boot)
- [Q53. Profiles](spring-boot-interview-mastery.md#q53-what-are-spring-boot-profiles)
- [Q54. Profile-Specific Beans](spring-boot-interview-mastery.md#q54-how-do-you-create-profile-specific-beans)
- [Q55. `@Controller` vs `@RestController`](spring-boot-interview-mastery.md#q55-what-is-the-difference-between-controller-and-restcontroller)
- [Q56. `@RequestMapping` vs `@GetMapping`](spring-boot-interview-mastery.md#q56-what-is-the-difference-between-requestmapping-and-getmapping)
- [Q57. `@PathVariable` vs `@RequestParam`](spring-boot-interview-mastery.md#q57-what-is-the-difference-between-pathvariable-and-requestparam)
- [Q58. `@RequestBody` and `@ResponseBody`](spring-boot-interview-mastery.md#q58-what-is-requestbody-and-responsebody)
- [Q59. Validation](spring-boot-interview-mastery.md#q59-how-do-you-handle-validation-in-spring-boot)
- [Q60. CORS Configuration](spring-boot-interview-mastery.md#q60-what-is-cors-and-how-do-you-configure-it-in-spring-boot)

### Additional High-Value Questions
- [Q61. `application.properties` vs `application.yml`](spring-boot-interview-mastery.md#q61-what-is-the-difference-between-applicationproperties-and-applicationyml)
- [Q62–Q70. Advanced REST, Scheduling, Async, Interceptors](spring-boot-interview-mastery.md#q62-what-is-springbootservletinitializer)
- [Q71. `@ConditionalOnProperty`](spring-boot-interview-mastery.md#q71-what-is-spring-boots-conditionalonproperty)
- [Q72. Caching](spring-boot-interview-mastery.md#q72-what-is-caching-in-spring-boot)
- [Q73. `save()` vs `saveAndFlush()`](spring-boot-interview-mastery.md#q73-what-is-the-difference-between-save-and-saveandflush-in-jpa)
- [Q74. Flyway Migrations](spring-boot-interview-mastery.md#q74-what-is-flyway-and-how-is-it-used-in-spring-boot)
- [Q75. `@Entity`, `@Table`, `@Column`](spring-boot-interview-mastery.md#q75-what-is-the-difference-between-entity-table-column)
- [Q76. `@OneToMany` and `@ManyToOne`](spring-boot-interview-mastery.md#q76-what-is-onetomany-and-manytoone-how-do-you-model-them)
- [Q77. `@ManyToMany`](spring-boot-interview-mastery.md#q77-what-is-manytomany)
- [Q78–Q80. Auto-Configuration, Environment Variables, Embedded Server](spring-boot-interview-mastery.md#q78-what-is-enableautoconfiguration-and-how-is-it-different-from-springbootapplication)

### Microservices & Advanced Topics
- [Q81. `@EnableJpaRepositories`](spring-boot-interview-mastery.md#q81-what-is-enablejparepositories)
- [Q82. `PATCH` vs `PUT`](spring-boot-interview-mastery.md#q82-what-are-the-differences-between-patch-and-put-in-rest-apis)
- [Q83. Filter vs Interceptor](spring-boot-interview-mastery.md#q83-what-is-a-filter-vs-interceptor-in-spring-boot)
- [Q84. `@EnableWebSecurity`](spring-boot-interview-mastery.md#q84-what-is-the-role-of-enablewebsecurity)
- [Q85. Spring Lifecycle Events](spring-boot-interview-mastery.md#q85-how-do-you-run-code-at-specific-events-in-the-spring-lifecycle)
- [Q86. `@ConditionalOnMissingBean`](spring-boot-interview-mastery.md#q86-what-is-spring-boots-conditionalonmissingbean)
- [Q87. `@Transient` in JPA](spring-boot-interview-mastery.md#q87-what-is-transient-in-jpa)
- [Q88. Circuit Breaking](spring-boot-interview-mastery.md#q88-what-is-circuit-breaking-and-how-does-it-relate-to-spring-boot-microservices)
- [Q89. Service Discovery](spring-boot-interview-mastery.md#q89-what-is-service-discovery-in-microservices-with-spring-boot)
- [Q90. API Gateway](spring-boot-interview-mastery.md#q90-what-is-an-api-gateway-in-spring-boot-microservices)
- [Q91. `@Cacheable` vs `@CacheEvict` vs `@CachePut`](spring-boot-interview-mastery.md#q91-what-is-cacheable-vs-cacheevict-vs-cacheput)
- [Q92. `Optional.orElse()` vs `Optional.orElseGet()`](spring-boot-interview-mastery.md#q92-what-is-the-difference-between-optionalorelse-and-optionalorelseget)
- [Q93. Spring MVC vs Spring WebFlux](spring-boot-interview-mastery.md#q93-what-is-the-difference-between-spring-mvc-and-spring-webflux)
- [Q94. Rate Limiting](spring-boot-interview-mastery.md#q94-how-do-you-implement-rate-limiting-in-spring-boot)
- [Q95. `@Transactional(readOnly = true)`](spring-boot-interview-mastery.md#q95-what-is-transactionalreadonly--true-and-when-should-you-use-it)
- [Q96. Hot Swap / Auto-Reload](spring-boot-interview-mastery.md#q96-what-is-spring-boots-auto-reload--hot-swap)
- [Q97. Custom Spring Boot Starter](spring-boot-interview-mastery.md#q97-how-do-you-create-a-custom-spring-boot-starter)
- [Q98. `@Component` vs `@Configuration`](spring-boot-interview-mastery.md#q98-what-is-the-difference-between-component-and-configuration)
- [Q99. `@EnableConfigurationProperties`](spring-boot-interview-mastery.md#q99-what-is-enableconfigurationproperties)
- [Q100. Actuator `/health` Customization](spring-boot-interview-mastery.md#q100-what-is-spring-boot-actuators-health-endpoint-and-how-do-you-customise-it)

### Quiz
- [Final Quiz](spring-boot-interview-mastery.md#final-quiz)
  - [Section A — Multiple Choice (Q1–Q25)](spring-boot-interview-mastery.md#section-a--multiple-choice-questions-125)
  - [Section B — True or False (Q26–Q40)](spring-boot-interview-mastery.md#section-b--true-or-false-questions-2640)
  - [Section C — Short Answer (Q41–Q50)](spring-boot-interview-mastery.md#section-c--short-answer-questions-4150)
  - [Answer Key](spring-boot-interview-mastery.md#answer-key)
  - [Score Interpretation](spring-boot-interview-mastery.md#score-interpretation)

---

## Quick Reference by Topic

| Topic | Questions |
|-------|-----------|
| Core Foundations & Auto-Configuration | Q1–Q10 |
| Dependency Injection & Beans | Q11–Q20 |
| Bean Scopes & Lifecycle | Q21–Q30 |
| JPA, Transactions & Exceptions | Q31–Q40 |
| Testing & Actuator | Q41–Q50 |
| Security, REST & Profiles | Q51–Q60 |
| Advanced Configuration & JPA | Q61–Q80 |
| Microservices (Circuit Breakers, Service Discovery, API Gateway) | Q88–Q90 |
| Reactive & Performance | Q93–Q96 |

## How to Use

1. **Follow the parts in order.** Each part builds on the previous — start with Part 1 for core concepts before moving to data access or security.
2. **Pay special attention to Q34, Q40, Q52, and Q88–Q90** — `@Transactional`, N+1 problem, JWT auth, and microservices patterns are the most commonly asked topics.
3. **Take the final quiz** before your interview to identify gaps.
4. **Run the code examples** — every question includes working Java/Spring code you can paste into a project.
