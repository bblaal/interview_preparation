
---

# JWT Authentication Flow: React Frontend and Spring Boot Backend

This document provides an overview of how JSON Web Tokens (JWT) are used to handle authentication in a microservices setup with a React frontend and a Spring Boot backend. This setup is ideal for securing endpoints while maintaining a stateless authentication approach.

## 1. Overview

The authentication flow consists of:
- **Frontend (React)** sending login credentials to the backend.
- **Backend (Spring Boot)** verifying credentials, generating a JWT token upon successful login, and sending it back to the frontend.
- **Token Validation** on each secure request, ensuring that only authorized users access protected endpoints.

## 2. Flow of Authentication

### Step 1: Token Creation and Transmission

1. **React Frontend Login Request:**
   - User submits login credentials (username and password) through a login form.
   - Credentials are sent to the authentication microservice (typically using `axios.post()` in React).

2. **Backend Authentication and JWT Generation:**
   - The Spring Boot backend verifies the credentials against stored user details.
   - If valid, a JWT token is generated containing:
     - **Payload** with user information (e.g., `username`, `userID`).
     - **Claims** like roles or permissions.
     - **Expiration time** to control token lifespan.
     - **Signature** to verify authenticity.
   - The JWT token is sent back to the React frontend in the JSON response.

3. **Storing the JWT on the Frontend:**
   - The frontend stores the JWT in a secure location (e.g., `localStorage`, `sessionStorage`, or HTTP-only cookies).
   - For subsequent requests to protected endpoints, the React app includes the JWT token in the HTTP headers:
     ```js
     headers: { Authorization: `Bearer ${token}` }
     ```

### Step 2: Token Validation in Spring Boot JWT Filter

1. **Intercepting Requests with a JWT Filter:**
   - The backend uses a filter (e.g., `JwtAuthenticationFilter`) to intercept all incoming requests to protected endpoints.
   - This filter checks for a JWT token in the `Authorization` header.

2. **Extracting and Validating the JWT:**
   - The filter extracts the token from the `Authorization` header.
   - Using a JWT library (like `jjwt`), it validates:
     - **Token Integrity**: Ensures the token is signed using the correct secret/public key.
     - **Expiration**: Checks that the token hasn’t expired.
   - If valid, the filter extracts user information from the token’s claims (e.g., roles) and creates an `Authentication` object.

3. **Granting or Denying Access:**
   - Spring Security uses the `Authentication` object to authorize access based on user roles/permissions.
   - If the token is invalid or missing, the filter rejects the request with an HTTP 401 Unauthorized response.

### Example: JWT Filter Code Snippet in Spring Boot

The following is a sample JWT filter implementation:

```java
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.JwtException;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.web.filter.OncePerRequestFilter;
import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.List;

public class JwtAuthenticationFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain chain)
            throws ServletException, IOException {
        String authorizationHeader = request.getHeader("Authorization");

        if (authorizationHeader != null && authorizationHeader.startsWith("Bearer ")) {
            String token = authorizationHeader.substring(7);
            try {
                // Validate token and extract claims
                Claims claims = Jwts.parser()
                    .setSigningKey("secret-key") // use your secret key or public key here
                    .parseClaimsJws(token)
                    .getBody();

                String username = claims.getSubject();
                List<SimpleGrantedAuthority> authorities = // Extract authorities based on roles

                UsernamePasswordAuthenticationToken authToken =
                    new UsernamePasswordAuthenticationToken(username, null, authorities);
                SecurityContextHolder.getContext().setAuthentication(authToken);
            } catch (JwtException e) {
                // Handle invalid token
                response.sendError(HttpServletResponse.SC_UNAUTHORIZED, "Invalid JWT Token");
                return;
            }
        }
        chain.doFilter(request, response);
    }
}
```

## Summary

- **React Frontend**: Sends JWT in the `Authorization` header with each request.
- **Spring Boot Backend**: Uses a JWT filter to validate the token and authorize requests based on roles.
- **Security Context**: Grants or denies access based on the user’s roles/permissions in the JWT.

This approach provides a secure, token-based authentication mechanism for microservices using React and Spring Boot.

--- 
