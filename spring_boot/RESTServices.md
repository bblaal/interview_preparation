# Spring Boot REST Services Overview

## 1. Key Annotations for RESTful Services

### @RestController
- Combines `@Controller` and `@ResponseBody`.
- Every method in a class annotated with `@RestController` will return data, typically JSON or XML, instead of rendering a view.

```java
@RestController
public class MyController {
    @GetMapping("/hello")
    public String sayHello() {
        return "Hello, World!";
    }
}
```

Here, the method `sayHello()` will return a simple string, but Spring will automatically convert objects to JSON or XML when needed.

### @RequestMapping
- Used at the class or method level to map web requests onto specific handler functions.
- You can use more specific annotations like `@GetMapping`, `@PostMapping`, etc., as alternatives.

```java
@RestController
@RequestMapping("/api") // Base path for all endpoints
public class MyController {
    @GetMapping("/users")
    public List<User> getUsers() {
        return userService.getAllUsers();
    }
}
```
Here, `/api/users` is the URL mapped to `getUsers()` method.

### @GetMapping
- Shorthand for `@RequestMapping(method = RequestMethod.GET)`.
- Handles HTTP GET requests, typically used for fetching data.

```java
@GetMapping("/items")
public List<Item> getItems() {
    return itemService.getAllItems();
}
```

### @PostMapping
- Shorthand for `@RequestMapping(method = RequestMethod.POST)`.
- Handles HTTP POST requests, commonly used for creating new data.

```java
@PostMapping("/items")
public Item createItem(@RequestBody Item item) {
    return itemService.saveItem(item);
}
```
Here, `@RequestBody` tells Spring to map the incoming request body (JSON) to the `Item` object.

### @PutMapping
- Shorthand for `@RequestMapping(method = RequestMethod.PUT)`.
- Handles HTTP PUT requests, often used for updating data.

```java
@PutMapping("/items/{id}")
public Item updateItem(@PathVariable Long id, @RequestBody Item updatedItem) {
    return itemService.updateItem(id, updatedItem);
}
```
`@PathVariable` is used to bind a method parameter to a path segment of the URL.

### @DeleteMapping
- Shorthand for `@RequestMapping(method = RequestMethod.DELETE)`.
- Handles HTTP DELETE requests, typically used to delete a resource.

```java
@DeleteMapping("/items/{id}")
public void deleteItem(@PathVariable Long id) {
    itemService.deleteItem(id);
}
```

### @RequestBody
- Maps the HTTP request body to a Java object.
- Primarily used with `@PostMapping` and `@PutMapping` to pass JSON data in the request.

```java
@PostMapping("/users")
public User createUser(@RequestBody User user) {
    return userService.saveUser(user);
}
```

### @PathVariable
- Extracts values from the URI path and maps them to method parameters.

```java
@GetMapping("/users/{id}")
public User getUserById(@PathVariable Long id) {
    return userService.getUserById(id);
}
```

### @RequestParam
- Extracts query parameters from the URL.

```java
@GetMapping("/search")
public List<User> searchUsers(@RequestParam String keyword) {
    return userService.searchByKeyword(keyword);
}
```

## 2. Exception Handling in REST

### @ControllerAdvice and @ExceptionHandler
- **@ControllerAdvice**: Used for global exception handling in a Spring Boot application.
- **@ExceptionHandler**: Handles specific exceptions thrown in the application.

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<String> handleNotFound(ResourceNotFoundException ex) {
        return new ResponseEntity<>(ex.getMessage(), HttpStatus.NOT_FOUND);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleGenericException(Exception ex) {
        return new ResponseEntity<>("Internal server error", HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```
This allows for centralized and consistent error handling across all controllers.

## 3. Handling HTTP Response Status

### @ResponseStatus
- Marks a method or exception class with a specific HTTP status code.

```java
@ResponseStatus(HttpStatus.NOT_FOUND)
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```
This sends a 404 response when a resource is not found.

### ResponseEntity
- Represents the whole HTTP response, allowing you to control headers, body, and status.

```java
@GetMapping("/items/{id}")
public ResponseEntity<Item> getItemById(@PathVariable Long id) {
    Item item = itemService.findItemById(id);
    if (item == null) {
        return new ResponseEntity<>(HttpStatus.NOT_FOUND);
    }
    return new ResponseEntity<>(item, HttpStatus.OK);
}
```
`ResponseEntity` gives you full control over the HTTP response.

## 4. Sample REST Controller

```java
@RestController
@RequestMapping("/api/items")
public class ItemController {

    @Autowired
    private ItemService itemService;

    // Fetch all items
    @GetMapping
    public List<Item> getAllItems() {
        return itemService.getAllItems();
    }

    // Fetch a single item by ID
    @GetMapping("/{id}")
    public ResponseEntity<Item> getItemById(@PathVariable Long id) {
        Item item = itemService.findItemById(id);
        if (item == null) {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
        return new ResponseEntity<>(item, HttpStatus.OK);
    }

    // Create a new item
    @PostMapping
    public ResponseEntity<Item> createItem(@RequestBody Item item) {
        Item newItem = itemService.saveItem(item);
        return new ResponseEntity<>(newItem, HttpStatus.CREATED);
    }

    // Update an item
    @PutMapping("/{id}")
    public ResponseEntity<Item> updateItem(@PathVariable Long id, @RequestBody Item updatedItem) {
        Item item = itemService.updateItem(id, updatedItem);
        if (item == null) {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
        return new ResponseEntity<>(item, HttpStatus.OK);
    }

    // Delete an item
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteItem(@PathVariable Long id) {
        itemService.deleteItem(id);
        return new ResponseEntity<>(HttpStatus.NO_CONTENT);
    }
}
```

