# Tech Stack

## Technology Stack Table

| Category | Technology | Version | Purpose | Rationale |
| :------------------ | :---------- | :------ | :-------- | :-------- |
| Frontend Language | TypeScript | 5.9.3 | Primary language for Angular UI | Type safety, maintainability, industry standard for Angular |
| Frontend Framework | Angular | 21.1.2 (LTS) | Robust framework for complex SPAs | Explicitly required by NFR3 in PRD; LTS version ensures long-term support and stability |
| UI Component Library | Angular Material | 21.1.2 | Pre-built UI components | Consistent UI/UX, accessibility, integrates well with Angular (matched to Angular 21.x as it would be the next major version) |
| State Management | NgRx | 21.0.1 | Reactive state management for Angular | Scalable state management, predictable state changes, large community support (aligned with Angular 21.x) |
| Node.js | Node.js | 24.13.0 (LTS) | JS runtime for frontend build tools | LTS version provides stability for the development and build environment |
| Backend Language | C# | 14 | Primary language for .NET Web API and Azure Function | Explicitly required by NFR4 in PRD; strong typing, performance |
| Backend Framework | .NET Web API | 10.0 (LTS) | RESTful API development | Explicitly required by NFR4 in PRD; LTS version ensures long-term support and stability |
| API Style | REST | v1 | Standard for web service communication | Widely adopted, flexible, well-documented. Versioning will be via API endpoint (e.g., `/api/v1/`) |
| Database | Azure Cosmos DB | Managed by Azure (SDK 3.57.0 for .NET) | Document data persistence | Explicitly mentioned in NFR5; suitable for semi-structured event data, high write throughput, global distribution |
| Cache | Azure Cache for Redis | Managed by Azure (Redis 7.4 for Azure Managed Redis) | Distributed caching for API and Functions | Improve performance, reduce database load, supports real-time features |
| File Storage | Azure Blob Storage | Managed by Azure (API 2026-02-06) | Object storage for large files/assets | Scalable, cost-effective storage for non-relational data if needed |
| Authentication | Azure Active Directory B2C | Managed by Azure | User identity and access management | Enterprise-grade, secure, integrates with Azure services, supports various authentication flows |
| Frontend Testing | Jest, Angular Testing Library | 30.2.0, 19.0.0 | Unit and integration testing for Angular components | Fast feedback, robust testing of UI logic and interactions |
| Backend Testing | xUnit | v3 | Unit and integration testing for .NET backend | Industry standard for .NET testing, compatible with .NET 8 |
| Backend Testing | Moq | 4.20.72 | Mocking framework for .NET backend unit tests | Simplifies testing of dependencies |
| E2E Testing | Cypress | 15.9.0 | End-to-end testing for the full application flow | Simulate user interactions, ensure complete system functionality |
| Build Tool | Angular CLI (frontend) | 21.1.2 | Project scaffolding, building, and development tasks | Standard tooling for Angular projects (aligned with Angular 21.x) |
| Build Tool | .NET CLI (backend) | 10 (LTS) | Project scaffolding, building, and development tasks | Standard tooling for .NET projects (aligned with .NET 8) |
| Bundler | Webpack (via Angular CLI) | 5.104.1 | Module bundling for frontend assets | Included with Angular CLI, optimizes frontend for production |
| IaC Tool | Azure Bicep | 0.40.2 | Infrastructure as Code for Azure resources | Native Azure IaC, simplifies deployment and management of Azure resources (as required by Epic 3) |
| CI/CD | Azure DevOps Pipelines | Managed by Azure (Agent 3.225.0) | Automated build, test, and deployment workflows | Integrates seamlessly with Azure, supports monorepo builds, comprehensive CI/CD capabilities |
| Monitoring | Azure Application Insights | Managed by Azure | Application performance monitoring, logging, and diagnostics | Provides deep insights into application health and performance (as required by Epic 3) |
| Logging | Azure Monitor Logs (Log Analytics Workspace) | Managed by Azure (AMA 1.39.0) | Centralized log collection and analysis | Collects logs from all Azure services for comprehensive observability |
| CSS Framework | Tailwind CSS | 4.1.18 | Utility-first CSS framework | Rapid UI development, highly customizable, optimizes CSS for production builds |
| Data Migration | Custom .NET/C# Scripts | N/A | Manage database schema evolution and data transformations for Azure Cosmos DB. | Ensures controlled, version-controlled changes to Cosmos DB schema and data, integrated into CI/CD. |
