# AI-Assisted Engineering Log

This document records significant AI-assisted activities during the development of the Salary Management application. It captures the prompts used, the resulting decisions, how AI-generated suggestions were evaluated, and how the implementation was verified.

AI was used as an engineering assistant for requirement analysis, design exploration, implementation support, testing, and code review. Technical decisions and final implementation choices were reviewed and validated by the developer.

## 1. Requirements Definition

### Objective: 

Define project scope, primary user, MVP functionality, constraints, and acceptance criteria.

### Prompt:

Act as a senior software engineer helping me define an MVP salary management application for an organization with 10,000 employees across multiple countries.
The primary user is an HR Manager. Help me identify the core requirements, functional scope, non-functional considerations, constraints and features that should deliberately be excluded from the MVP.
Keep the solution practical and achievable. Avoid overengineering. Explain tradeoffs and flag decisions that require my input instead of making assumptions.

### Accepted

- Employee salary management, including employee records and compensation updates
- Salary revision history to preserve previous compensation information
- Salary dashboard and compensation insights
- Search, filtering and server-side pagination
- Synthetic data representing 10,000 employees
- Currency-aware compensation analysis
- Input validation, error handling and automated testing
- Explicit scope boundaries to keep the implementation achievable

### Deferred

- Authentication and Role-Based Access Control (RBAC)
The MVP will focus on a single HR Manager workflow without implementing authentication or role/permission management.
This reduces implementation scope and allows focus on the core compensation-management functionality.
This is a deliberate limitation of the assessment application. Authentication and authorization would be required before handling real employee salary data in a production environment.

- Employee onboarding progress tracking
- Performance review and feedback history
- Country-specific tax calculations

These features may be valuable in a broader HR platform but are not required to demonstrate the core salary-management workflow.

### Deliberately Excluded from the MVP

- Payroll processing and salary disbursement
- Integration with external HR or payroll systems
- Real employee data
- Complex compensation structures involving bonuses, stock compensation, or additional benefits
- Production-grade authentication and enterprise SSO

### Engineering Decisions

- Scope prioritization: Focus on employee compensation management, salary history and analytics rather than expanding into a complete HR or payroll system.
- Data safety: Use synthetic employee data rather than real employee information.
- Data volume: Include 10,000 employee records to evaluate data retrieval, filtering and pagination under a meaningful dataset size.
- Currency handling: Avoid misleading salary comparisons across different currencies by keeping compensation analysis currency-aware.
- Implementation complexity: Prioritize a complete, maintainable and tested application over introducing infrastructure or features unnecessary for the MVP.

### Outcome

- A requirements document was defined covering the application goal, system overview, technical scope, core features, exclusions, future enhancements, and verification/acceptance criteria.
- The requirements were reviewed and refined to make the MVP achievable while maintaining the core value of employee compensation management and analysis.

### Verification

- Requirements reviewed for consistency with the MVP goal.
- Features assessed against the available assessment scope and timeline.
- Exclusions explicitly documented to prevent scope creep.
- Acceptance criteria defined to guide implementation and testing.

# 2. Architecture & Technical Decisions

### Objective

Choose a small end-to-end architecture suitable for the 10,000-employee assessment and define API and salary-revision boundaries before implementation.

### Accepted

- React + TypeScript UI calls an Express JSON API; the browser does not access SQLite directly.
- Keep a single deployable application and relational database for the expected assessment scale.
- Separate request validation, business rules, and SQLite data access so core rules can be tested independently of HTTP.
- Keep future revision activation out of scope while recording the revision and effective date.
- Group compensation analytics by currency and avoid unsupported currency conversion.

### Artifacts

- [System architecture](architecture.mmd)
- [Planning and design notes](design-decisions.md)
- [Salary revision flow](data-flow.mmd)

### Review

The choices were checked against the assignment's end-to-end, relational database, seeding, testing, and artifact expectations. A separate queue, scheduler, or analytics store was not justified for the MVP.

# 3. Data Model & Database Design

### Objective

Define the data model for employee records, departments, and salary history while keeping it simple and avoiding redundant data.

### Prompt

While discussing how upcoming salary revisions should be communicated in the employee and HR interfaces, I considered adding a boolean field (`showUpcomingSalaryRevision`) to the Employee entity to control the display of upcoming salary revision notifications.

Evaluate whether this field is necessary and suggest a simpler approach if the information can be derived from the existing data model.

### Accepted

- Department, employee, and salary history entities with one-to-many relationships.
- Unique employee/effective-date constraint to prevent duplicate salary revisions.
- Integer minor-unit storage for salary amounts and currency-aware grouping for analytics.
- A transaction for creating salary history and, for same-day revisions, updating current salary.
- Do not add `showUpcomingSalaryRevision` to `Employee`; derive whether a revision is upcoming from its `effectiveDate` and the current date.
- Use salary history as the shared source of truth. The MVP HR interface can distinguish future-dated revisions from revisions whose effective dates have arrived; a future employee-facing interface could use the same records with different presentation.
- Future-dated revisions do not immediately change the employee's current salary, and the MVP does not automatically activate them on their effective date.
- Upcoming salary revisions can be edited before they become effective.
- Effective dates must be today or later, and duplicate effective dates
  for the same employee are not allowed.
- No minimum interval between salary revisions is enforced.

### Evaluation & Outcome

The boolean would duplicate information already represented by salary history and effective dates. It could become stale when a revision is added, changed, or removed, so the UI should derive the upcoming state from the records instead of synchronizing a second field.

This keeps the database free of redundant display state while allowing HR and a possible future employee-facing UI to present the same revision data differently. The assessment MVP includes the HR Manager workflow; an employee-facing interface is not part of the current scope.

### Decision refinement: 

The initial design considered minimum intervals between salary revisions and validation against neighboring records. This was reconsidered to reduce unnecessary complexity in the MVP. The final approach allows HR to edit upcoming salary revisions, prohibits duplicate effective dates, and requires effective dates to be today or later. Effective salary history remains immutable, and no minimum interval is enforced.

### Verification

- At the data-model level, upcoming revisions can be selected from salary history by comparing `effectiveDate` with the current date; no employee boolean is needed.
- UI presentation can distinguish future-dated and already-effective records using the same history data.
- Automated checks for this derived state should be added with the implementation. Because automatic activation is out of scope, an elapsed effective date does not itself update `Employee.currentSalary`.

See [data model](data-modelling.mmd) and [design notes](design-decisions.md) for field and behavior details.

# 4. Backend Implementation

To be documented as development progresses.

# 5. Frontend Implementation

To be documented as development progresses.

# 6. Testing & Validation

To be documented as development progresses.

# 7. Code Review & Improvements

To be documented as development progresses.
