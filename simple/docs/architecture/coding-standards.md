# Coding Standards

Define MINIMAL but CRITICAL standards for AI agents. Focus only on project-specific rules that prevent common mistakes. These will be used by dev agents.

## Critical Fullstack Rules

- **Type Sharing:** Always define shared types (interfaces, enums) in `libs/shared/models` and import from there. This ensures a single source of truth for data contracts.
- **API Calls:** Never make direct HTTP calls from UI components. Always use dedicated service layers (e.g., `ApiClientService`, `OrderEventsApiService`) to encapsulate API interactions and handle common concerns like authentication and error handling.
- **Environment Variables:** Access environment variables only through strongly typed configuration objects. Avoid direct access to `process.env` (frontend) or raw `IConfiguration` (backend) in application logic.
- **Error Handling:** All API routes (backend) and service calls (frontend) must utilize the standardized error handling strategy defined in the "Global Error Handling Strategy" section, ensuring consistent error responses and logging.
- **State Updates (Frontend):** Never mutate NgRx state directly. All state modifications must occur through pure reducers, triggered by dispatched actions, adhering to the one-way data flow pattern.
- **No Hardcoded Values:**
    -   **Configuration:** All environment-specific values (e.g., API URLs, connection strings) must be stored in configuration files and accessed through strongly-typed configuration objects.
    -   **Constants:** Application-wide constants (e.g., UI labels, default settings) should be defined in dedicated constant files (e.g., `constants.ts` or `Constants.cs`).
    -   **Magic Strings/Numbers:** Avoid using "magic strings" and "magic numbers" directly in the code. Use named constants or enums instead to improve readability and maintainability.

## New Library Integration

- **Justification:** Before adding a new library, provide a clear justification in the project documentation, outlining the problem it solves and why existing libraries are insufficient.
- **Vetting:** New libraries must be well-maintained, have a permissive license compatible with the project, and be vetted for security vulnerabilities using tools like Snyk or `npm audit`.
- **Testing:** Any new library must be accompanied by tests that demonstrate its correct integration and functionality within the application. These tests should cover the core use cases for which the library is being added.
- **Documentation:** Upon adding a new library, the "Tech Stack" table in this architecture document must be updated with the library's name and version.

## Pull Request (PR) Standards

- **Small and Focused:** PRs should be small, focused, and represent a single logical change. Avoid large, monolithic PRs with thousands of lines of code that address multiple concerns.
- **Meaningful Changes:** Each PR should have a clear purpose and be linked to a specific task, user story, or bug fix. The PR description should clearly explain *what* the change is and *why* it is needed.
- **Reviewability:** Small, focused PRs are easier and faster to review, leading to higher quality feedback and a more efficient development process.
- **Guideline:** As a general guideline, a PR should ideally have less than 500 lines of changes (excluding generated files). If a change is larger, consider breaking it down into multiple, smaller PRs that can be merged sequentially.
- **Self-Contained:** Each PR should be self-contained and not break the main branch. All tests must pass before a PR can be merged.

## Runtime Documentation and Code Comments

**Runtime Documentation (Self-documenting Code):**
-   **Meaningful Naming:** Use clear and descriptive names for variables, functions, classes, and methods. The name should reflect the purpose of the code element.
-   **Logging:** Use structured logging to provide runtime visibility into application behavior. Log key events, decisions, and outcomes, especially for complex business logic.
-   **API Documentation:** The OpenAPI specification serves as the runtime documentation for the API, providing clear contracts and usage examples for all endpoints.

**Code Comments:**
-   **When to Comment:** Add comments to explain *why* something is done, not *what* is being done. The code itself should explain what it does.
-   **Complex Logic:** Use comments to explain complex algorithms, business rules, or workarounds.
-   **TODOs/FIXMEs:** Use `// TODO:` or `// FIXME:` comments to mark areas that require future attention, and include a brief explanation of what needs to be done.
-   **Avoid Clutter:** Avoid unnecessary or obvious comments that just add noise to the code.

## SQL Coding Standards

- **Keywords:** Use uppercase for all SQL keywords (e.g., `SELECT`, `FROM`, `WHERE`, `JOIN`).
- **Identifiers:** Use `snake_case` for table and column names for consistency.
- **Formatting:** Use indentation and line breaks to improve the readability of complex queries.
- **Comments:** Use comments (`--`) to explain complex logic, joins, or `WHERE` clauses.
- **Performance:** Avoid using `SELECT *`; explicitly list the columns you need. Ensure efficient `JOIN`s and use `WHERE` clauses to filter data as early as possible.

## Angular Coding Standards

- **File Structure:** Adhere to the Angular style guide for file and folder organization, including feature modules and a shared module.
- **Components:**
    -   Use `OnPush` change detection strategy where possible to optimize performance.
    -   Favor standalone components for better modularity and reduced reliance on `NgModule`.
    -   Keep components focused on presentation logic. Delegate complex business logic to services.
- **Services:**
    -   Use services for business logic, API calls, and shared state.
    -   Provide services at the appropriate level (e.g., `providedIn: 'root'` for singletons).
- **RxJS:**
    -   Use RxJS for managing asynchronous operations.
    -   Always unsubscribe from observables to prevent memory leaks (e.g., using `takeUntil`, the `async` pipe, or `take(1)`).
- **Typing:** Leverage TypeScript's strong typing. Avoid using the `any` type whenever possible.

## .NET Coding Standards

- **Naming:** Follow Microsoft's C# Naming Conventions (e.g., `PascalCase` for classes, methods, and properties; `camelCase` for local variables and method parameters).
- **Async/Await:** Use `async` and `await` for all I/O-bound operations (e.g., database calls, HTTP requests). Use `ConfigureAwait(false)` in library code where context is not required to avoid potential deadlocks.
- **LINQ:** Use LINQ for querying collections where it improves readability and maintainability. For complex queries, consider the performance implications.
- **Dependency Injection:** Use dependency injection for all services, repositories, and other dependencies. Register services with the appropriate lifetime in the DI container.
- **Error Handling:** Use exceptions for exceptional circumstances. Adhere to the "Global Error Handling Strategy" defined in this document.
- **File Organization:** Organize files and folders by feature or responsibility, mirroring the overall project structure.

## Naming Conventions

| Element | Frontend | Backend | Example |
|---|---|---|---|
| Components | PascalCase | - | `UserProfileComponent.ts` |
| Services | PascalCase | PascalCase | `AuthService.ts`, `EventSubmissionService.cs` |
| Interfaces | PascalCase | PascalCase | `OrderEvent.ts`, `IOrderEventRepository.cs` |
| API Controllers | - | PascalCase | `EventsController.cs` |
| API Endpoints/Routes | - | kebab-case | `/api/v1/events/order` |
| Database Tables/Collections | PascalCase | - | `OrderEvents` (Cosmos DB) |
| Azure Functions | - | PascalCase | `ProcessOrderEvent` |

