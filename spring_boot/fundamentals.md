# Spring Boot Interview Prep

## 1. Spring Boot Internal Working

- **Starters**: Spring Boot provides starter dependencies like `spring-boot-starter-web` that simplify dependency management.
- **Auto-Configuration**: Configures components automatically based on the classpath and project settings.
- **Embedded Servers**: Provides embedded servers (Tomcat, Jetty), enabling standalone application deployment.
- **SpringApplication**: The entry point to launch Spring Boot apps. Initializes the application context, sets up the environment, and starts the embedded server.

## 2. Key Concepts

### Bean
- An object managed by Spring's IoC container.
- Created, wired, and destroyed by Spring.

### Inversion of Control (IoC)
- Spring manages the object creation and lifecycle instead of the programmer (via container).

### Dependency Injection (DI)
- Automatically injecting dependencies into a class through:
  - Constructor Injection (recommended)
  - Setter Injection
  - Field Injection

### ApplicationContext
- Central interface for Spring IoC container.
- Manages beans, handles dependency injection, and provides features like event propagation, AOP, and resource loading.

## 3. Spring Boot Architecture

- **Starter Dependencies**: Bundled dependencies for specific functionalities (e.g., web, data).
- **Auto-Configuration**: Automatically configures the application based on classpath dependencies.
- **Embedded Server**: Runs with embedded Tomcat or Jetty, simplifying deployments.
- **SpringApplication**: Starts the `ApplicationContext`, triggers auto-configuration, and runs the embedded server.
- **Layered Architecture**: 
  - **Presentation**: `@RestController`
  - **Business**: `@Service`
  - **Persistence**: `@Repository`
  - **Configuration**: `@Configuration`


# Spring Dependency Injection Overview

## 1. Dependency Injection in Spring
In Spring, dependency injection can be achieved using three primary methods:
- **Field Injection**
- **Constructor Injection** (Preferred)
- **Setter Injection**

---

## 2. Field Injection

Field injection uses the `@Autowired` annotation directly on a class field to inject dependencies.

### Example:
```java
@RestController
public class MyController {

    @Autowired
    private MyService myService;

    @GetMapping("/hello")
    public String sayHello() {
        return myService.getMessage();
    }
}
```

### Characteristics:
- **Visibility**: The field is directly injected without using constructors or setters.
- **Convenience**: Simple and concise, but less flexible.
- **Testability**: Harder to mock or test since fields are private and final after injection.
- **Immutability**: Breaks immutability because fields can be modified outside the constructor.

### Usage:
Field injection is convenient, but it's generally discouraged due to the issues with immutability and testability. **Constructor injection** is preferred for better design and testing practices.

---

## 3. Constructor Injection (Preferred)

Constructor injection uses `@Autowired` on a constructor to inject dependencies. If a class has only one constructor, Spring 4.3+ can inject dependencies without explicitly using `@Autowired`.

### Example:
```java
@RestController
public class MyController {

    private final MyService myService;

    // Constructor Injection
    @Autowired
    public MyController(MyService myService) {
        this.myService = myService;
    }

    @GetMapping("/hello")
    public String sayHello() {
        return myService.getMessage();
    }
}
```

### Characteristics:
- **Immutability**: Ensures immutability since dependencies are injected only once at the time of object creation.
- **Testability**: Makes unit testing easier, as dependencies are clearly visible and can be passed during testing.
- **Best Practice**: This is the **preferred** approach for dependency injection in Spring.

### Key Points:
- Constructor injection ensures that all required dependencies are set at the time of object creation.
- If a class has only one constructor, you don't need to explicitly use `@Autowired`; Spring automatically injects dependencies.
  
---

## 4. Setter Injection

Setter injection uses the `@Autowired` annotation on a setter method to inject dependencies.

### Example:
```java
@RestController
public class MyController {

    private MyService myService;

    // Setter Injection
    @Autowired
    public void setMyService(MyService myService) {
        this.myService = myService;
    }

    @GetMapping("/hello")
    public String sayHello() {
        return myService.getMessage();
    }
}
```

### Characteristics:
- **Flexibility**: Allows dependencies to be set after object creation.
- **Optional Dependencies**: Useful when the dependency is optional or can be changed.
- **Testability**: Easier to mock dependencies for unit testing compared to field injection.

---

## 5. Comparison of Dependency Injection Methods

| Injection Type     | Immutability | Testability   | Readability  | Best Practice  |
|--------------------|--------------|---------------|--------------|----------------|
| **Field Injection** | Low          | Harder to mock| High         | Not Preferred  |
| **Constructor Injection** | High  | Easier to mock| Very High    | **Preferred**  |
| **Setter Injection** | Medium     | Medium         | Medium       | Use selectively|

---

## 6. Best Practices
- Prefer **constructor injection** for mandatory dependencies to ensure immutability and clear visibility of dependencies.
- Use **setter injection** for optional dependencies or when you need to reconfigure the dependency after object creation.
- Avoid **field injection** in most cases, as it makes testing harder and can lead to design issues like tight coupling.

