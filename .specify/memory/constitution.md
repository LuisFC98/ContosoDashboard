<!-- 
📋 SYNC IMPACT REPORT
Version Change: 0.0.0 → 1.0.0 (MAJOR - Initial constitution creation)
Modified Principles: N/A (initial)
Added Sections: Core Principles (5), Security & Architecture, Development Workflow, Governance
Removed Sections: N/A
Templates Updated:
  ✅ plan-template.md - Constitution Check section aligned with principles
  ✅ spec-template.md - Requirements aligned with security principles
  ✅ tasks-template.md - Task types reflect testing and security principles
  ⚠️ Manual review recommended: Code review process in PRs
Follow-up TODOs: None
-->

# ContosoDashboard Constitution

This constitution governs the development, maintenance, and evolution of the ContosoDashboard application, a training-focused Blazor Server application demonstrating spec-driven development and secure architecture patterns.

## Core Principles

### I. Spec-Driven Development (SDD)
Every feature begins with a specification in `specs/[###-feature]/spec.md` that includes user stories, acceptance scenarios, and success criteria. Implementation follows from specifications, not vice versa. Specifications are approved before development begins. This ensures alignment with requirements and enables independent, testable feature delivery.

### II. Service-Layer Security (NON-NEGOTIABLE)
Authorization MUST be enforced at the service layer, not just at page/controller level. Every service method must verify user permissions before returning data or modifying state. This prevents Insecure Direct Object Reference (IDOR) vulnerabilities and ensures multi-layered defense. Service-level authorization is the primary enforcement mechanism; page-level `[Authorize]` attributes provide a secondary gate.

### III. Test-First Architecture
Tests are written first and MUST fail before implementation begins (Red-Green-Refactor). Test categories: contract tests (API contracts), integration tests (cross-service workflows), and unit tests (service logic). All security-critical paths require integration tests. Database interactions require integration tests. Tests serve as living documentation of expected behavior.

### IV. Interface Abstraction for Cloud Migration
All infrastructure dependencies (database, file storage, authentication) are accessed through C# interfaces. Training implementations use local/offline implementations (SQL Server LocalDB, file system). Production implementations use cloud services (Azure SQL Database, Azure Blob Storage, Microsoft Entra ID). Business logic MUST NOT reference concrete infrastructure. This enables seamless migration without code changes.

### V. Clean Separation of Concerns
Code is organized into distinct layers with clear responsibilities: Models (entities), Services (business logic & authorization), Data (EF Core context), Pages (Blazor UI). Services depend on interfaces, not concrete implementations. Data access flows through DbContext and services only. This structure enables testability, maintainability, and compliance with all other principles.

## Security & Architecture

### Authentication & Authorization
- Cookie-based authentication with 8-hour sliding expiration (training implementation)
- Claims-based identity with user roles (Administrator, Project Manager, Team Lead, Employee)
- All protected pages enforce `[Authorize]` attribute
- Service layer performs authorization checks on all data access methods
- Role-based access control (RBAC) with hierarchical permissions
- User isolation enforced at service level: users see only authorized data

### Data Protection
- IDOR (Insecure Direct Object Reference) protection: Service methods verify resource ownership before returning
- No direct URL manipulation grants access to unauthorized resources
- Database queries filtered by authenticated user context
- Security headers configured in middleware (CSP, X-Frame-Options, X-XSS-Protection, etc.)
- Mock authentication suitable for training; production deployments require identity providers (Azure AD, Auth0, OAuth 2.0/OpenID Connect)

### Development Boundaries
- This application is **training-only**, NOT for production use
- Mock authentication enables offline training without cloud subscriptions
- Architecture demonstrates security patterns; implementation includes known limitations documented in README
- Database uses SQL Server LocalDB; file storage uses local filesystem
- No external service dependencies required for local development

## Development Workflow

### Feature Implementation Sequence
1. **Specification Phase**: Write spec.md with user stories (prioritized P1–P3), acceptance scenarios, and functional requirements
2. **Design Phase**: Create research.md, data-model.md, and contract specifications as needed
3. **Test-First**: Write failing tests (contract, integration, unit) before implementation
4. **Implementation**: Implement services with authorization checks, add models, create Blazor pages
5. **Integration Testing**: Verify multi-service workflows and security checks
6. **Code Review**: PR reviewers verify SDD compliance, security checks, and test coverage

### Code Review Gates
- All PRs require verification of spec-driven approach (spec.md exists and is current)
- All security-critical paths must have integration tests
- Service-layer authorization checks visible in code review
- No hardcoded infrastructure dependencies (all dependencies via interfaces)
- Tests are written and passing before feature merge

### Task Organization
Tasks are grouped by user story in tasks.md, enabling independent implementation and testing of each story. Each story is independently deployable and demonstrates a complete slice of functionality. Tasks follow naming convention: `[ID] [P?] [Story] Description` where `[P]` indicates parallelizable tasks.

## Governance

### Constitution Authority
This constitution supersedes all other development practices, templates, and informal guidelines. When conflict arises between a principle and a proposed practice, the principle prevails unless the constitution is formally amended.

### Amendment Process
1. Changes to principles require documentation of rationale and impact
2. Version numbers follow semantic versioning:
   - **MAJOR**: Backward-incompatible principle removals or redefinitions
   - **MINOR**: New principle or significantly expanded section
   - **PATCH**: Clarifications, wording changes, non-semantic refinements
3. Amendments include "Sync Impact Report" documenting affected templates
4. All templates in `.specify/templates/` must be reviewed for alignment after amendments

### Compliance Review
- All feature branches reference spec.md in plan.md or feature description
- All service methods handling user data include authorization comments explaining access control
- All new task categories in tasks-template.md align with testing principle (test-first)
- Security-critical features (auth, IDOR protection) flagged in PRs and verified in review

### Template Maintenance
The following templates are governed by this constitution:
- `.specify/templates/plan-template.md` - Must include "Constitution Check" gate referencing all applicable principles
- `.specify/templates/spec-template.md` - User stories MUST be prioritized and independently testable
- `.specify/templates/tasks-template.md` - Tasks MUST be organized by user story; testing principle requires test-first tasks
- `.specify/templates/checklist-template.md` - Feature checklists verify principle compliance

**Version**: 1.0.0 | **Ratified**: 2026-01-28 | **Last Amended**: 2026-01-28
