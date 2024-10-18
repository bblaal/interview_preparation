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

## 4. REST Services in Spring Boot

- **@RestController**: Simplifies creating REST endpoints. Automatically serializes Java objects to JSON/XML.
- **HTTP Mappings**: 
  - `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`
  - Maps URLs to controller methods.
- **Dependency Injection**: Services and repositories are injected into controllers for handling business logic.
- **Exception Handling**: Use `@ControllerAdvice` and `@ExceptionHandler` for global error handling.

