## Spring Boot Golden Template – MuleSoft Migration Program

**Context: Migrating ~170 MuleSoft services into a unified Java Spring Boot application deployed on Google Cloud Platform (GCP)**

---

# 1. Objective

You are generating a **Golden Template Spring Boot Application** that will serve as the base architecture for migrating ~170 MuleSoft APIs.

This template must:

* Enforce industry best practices
* Be secure by default
* Be compliance-ready (pharmacy domain, public APIs)
* Be observable and auditable
* Be cloud-ready (Google Cloud Platform)
* Support both pass-through and orchestration services
* Be modular and future-proof (possible microservice extraction)

This is a **single deployable modular monolith**, NOT microservices.

---

# 2. Technology Stack (MANDATORY)

## 2.1 Core Framework

* Java 21 (LTS)
* Spring Boot 3.x (latest stable)
* Spring Framework 6.x

---

## 2.2 Dependency Management

Use Maven with:

* `spring-boot-starter-parent`
* Enforce dependency convergence
* OWASP dependency-check plugin enabled

---

# 3. Required Dependencies

## 3.1 Web Layer

* `spring-boot-starter-web`
* `spring-boot-starter-validation`
* `springdoc-openapi-starter-webmvc-ui`

---

## 3.2 Security

* `spring-boot-starter-security`
* `spring-boot-starter-oauth2-resource-server`
* `jjwt` (if manual JWT handling needed)

Must support:

* JWT validation
* OAuth2 Resource Server mode
* Role-based access control
* Method-level security enabled

---

## 3.3 Data Access

If DB required:

* `spring-boot-starter-data-jpa`
* `postgresql` driver
* `flyway-core` (mandatory DB migration tool)

---

## 3.4 Observability

* `spring-boot-starter-actuator`
* `micrometer-registry-prometheus`
* `opentelemetry-spring-boot-starter`
* `opentelemetry-exporter-otlp`

Must support:

* Distributed tracing
* Prometheus metrics
* GCP Cloud Trace compatibility

---

## 3.5 Resilience

Use:

* `resilience4j-spring-boot3`

Must support:

* Circuit breaker
* Retry
* Rate limiter
* Bulkhead
* Timeout

---

## 3.6 Logging

Use:

* Logback (default)
* JSON logging via `logstash-logback-encoder`

Logs must be structured JSON.

---

## 3.7 Caching (Optional but pre-configured)

* `spring-boot-starter-cache`
* `caffeine`
* `spring-data-redis`

---

## 3.8 Testing

* `spring-boot-starter-test`
* `mockito`
* `testcontainers`
* `wiremock`
* `spring-cloud-contract` (optional but recommended)

---

# 4. Project Architecture (MANDATORY)

Use Hexagonal / Clean Architecture principles.

### Directory Structure

```
com.company.project
 ├── domain
 ├── application
 ├── inbound
 │    └── controller
 ├── outbound
 │    └── client
 ├── infrastructure
 │    ├── config
 │    ├── persistence
 │    └── security
 ├── exception
 ├── util
```

Rules:

* Controllers must not access repositories directly.
* External APIs must be accessed through outbound clients.
* Domain must not depend on Spring.

---

# 5. Mandatory Cross-Cutting Features

---

## 5.1 Centralized Exception Handling

Implement:

* `@ControllerAdvice`
* Global error response model

Standard Error Response:

```json
{
  "timestamp": "",
  "correlationId": "",
  "errorCode": "",
  "message": "",
  "details": []
}
```

---

## 5.2 Correlation ID

* Generate if not present
* Propagate via:

  * `X-Correlation-ID`
  * `traceparent`

Must be included in:

* All logs
* All error responses

---

## 5.3 Structured Logging

All logs must:

* Be JSON
* Include traceId, spanId, correlationId
* Mask sensitive fields (PII)

Sensitive fields to mask:

* SSN
* Patient ID
* Prescription ID
* Access tokens
* Authorization headers

---

## 5.4 Audit Logging (Mandatory)

Implement separate audit logger.

Audit log must record:

* Who accessed
* What endpoint
* What action
* Timestamp
* Before/After values (if data modified)

Audit logs must be immutable and retained.

---

## 5.5 Health Checks

Expose:

* `/actuator/health`
* `/actuator/prometheus`
* `/actuator/info`

Health must include:

* DB connectivity
* Downstream connectivity (if critical)

---

# 6. Security Requirements

---

## 6.1 JWT Authentication

* Validate signature
* Validate expiration
* Validate issuer
* Validate audience

---

## 6.2 Security Headers (Mandatory)

Add filter to enforce:

* X-Content-Type-Options
* X-Frame-Options
* Content-Security-Policy
* Strict-Transport-Security

---

## 6.3 CORS

* Strict origin control
* No wildcard in production

---

## 6.4 Rate Limiting

Use:

* Resilience4j RateLimiter (app-level)

Must allow:

* Per API configuration
* Per client configuration

---

## 6.5 Secrets

Must integrate with:

* Google Cloud Secret Manager

Never hardcode secrets.

---

# 7. Resilience Standards

All outbound calls must:

* Have timeout configured
* Use circuit breaker
* Use retry (with backoff)
* Fail gracefully

Do NOT allow infinite blocking calls.

---

# 8. HTTP Client Standard

Use:

* `WebClient` (preferred)

Must configure:

* Connection pooling
* Timeout
* Logging filter
* Correlation propagation

Never call WebClient directly from controller.

---

# 9. API Standards

---

## 9.1 Versioning

All APIs must be versioned:

```
/api/v1/...
```

---

## 9.2 OpenAPI

* Auto-generate OpenAPI 3 spec
* Swagger UI disabled in production

---

## 9.3 Request Validation

Use:

* `@Valid`
* Bean Validation annotations

Return standardized validation error response.

---

# 10. Database Standards

If database used:

* Flyway for migrations
* No auto-DDL in production
* Naming conventions enforced
* Index strategy documented

---

# 11. Deployment Requirements

---

## 11.1 Docker

Use:

* Distroless base image
* Non-root user
* Layered JAR
* Minimal attack surface

---

## 11.2 Configuration

Use:

```
application.yml
application-dev.yml
application-prod.yml
```

All configs externalized.

---

# 12. Compliance Considerations (Pharmacy/Public APIs)

The template must ensure:

* PII masking
* Access logging
* Audit trail
* Secure headers
* Strong authentication
* Rate limiting
* Data retention configurability

---

# 13. Code Quality Gates

Must include:

* SonarQube compatibility
* Minimum 80% unit test coverage
* No critical OWASP vulnerabilities
* No circular dependencies

---

# 14. Migration Compatibility

When replacing MuleSoft APIs:

* Preserve error codes
* Preserve response contract
* Preserve headers if required
* Maintain backward compatibility

---

# 15. Non-Goals

Do NOT:

* Build microservices
* Introduce unnecessary frameworks
* Add excessive abstraction
* Mix business logic in controllers
* Bypass validation
* Log sensitive data in plain text

---

# 16. Output Expectations (For AI Agents)

When generating the Golden Template:

You must:

1. Provide full Maven pom.xml
2. Provide base package structure
3. Provide sample controller
4. Provide sample service
5. Provide sample outbound client
6. Provide global exception handler
7. Provide security configuration
8. Provide resilience configuration
9. Provide logging configuration
10. Provide Dockerfile
11. Provide sample unit test
12. Provide README explaining architecture decisions

All configurations must follow modern Spring Boot 3 standards.

---

# End of Instructions

---
