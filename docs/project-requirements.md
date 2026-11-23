# Introduction & Scope of the Project

## Goal

The goal of this project is to build a web-application for salary management that enables efficient payroll information management and analysis for 10,000+ employees of the ACME organisation.

# System overview

The web application will provide an HR manager with a centralized interface to view, manage and analyze employee salary information across departments and countries. The system will support employee record management, salary history tracking, compensation insights and filtering by organizational attributes.

# Technical Scope of the project
 
- React + TypeScript frontend
- NodeJs + Express REST API
- Relational DB using SQLite
- Seed data generator for 10,000 realistic but synthetic employees
- Server-side pagination, filtering, search functionalities
- Automated tests for core logic and API functionality

# Core features

## Salary dashboard

- Total employees and payroll summary
- Salary distribution, department level comparison
- Breakdown by country, department and currency

## Employee Salary Management

- View employee salary and compensation details.
- View salary revision history, including previous salary, revised salary, effective date, increment percentage and reason.
- Update employee compensation while preserving historical records.
- View employees in a searchable paginated table
- Search employees by name or employee ID
- Filter by country, department, role, employment status, etc.
- view employment details
- Add new employees
- Validate inputs and provide clear success/error feedback

## Compensation Insights

- View organization-wide compensation metrics, including total payroll, average salary, median salary, and salary ranges.
- View and analyze salary distributions across the organization using salary ranges and employee counts.
- Compare compensation across departments and job roles using metrics such as average, median, minimum, and maximum salary.
- Explore compensation by country and currency.
- Filter compensation insights by department, country, job role, and employment status where applicable.
- Identify compensation patterns and differences across organizational groups to support HR decision-making.
- Ensure compensation comparisons are performed within the same currency and avoid misleading cross-currency aggregations without conversion.

### Upcoming Salary Revisions

- Record future-dated salary revisions for employees.
- Allow HR to edit upcoming revisions before they become effective.
- Allow editing the salary amount, effective date and reason.
- Support a maximum of two upcoming revisions per employee.
- Require the effective date of a new or edited upcoming revision to be today or later.
- Prevent duplicate effective dates for the same employee.
- Display upcoming revisions in effective-date order.
- Keep the employee's current salary unchanged when a future revision is recorded.
- Do not automatically activate future salary revisions in the MVP.

# What is being left out deliberately

- Payroll processing and disbursement
- Real employee data or integrations with HR/Payroll systems
- Tax calculations across different countries
- Bonuses, stock compensations, other complex benefit calculations
- Production grade authentication and enterprise SSO.
- Automatic activation of future salary revisions
- Background scheduling infrastructure for salary revision activation
- Automatic or manual activation of scheduled salary revisions.

# Future enhancements possible

- Employee onboarding requirements and progress tracking
- Performance feedback to understand salary increment trends
- Country-specific tax calculations
- Automated activation of salary revisions through background processing
- Authentication, authorization and role-based access
- External HR and payroll integrations

# Verification & Acceptance

- 10,000 seeded employee records can be loaded and queried.
- HR can search, filter, paginate, view and edit employee information.
- HR can create an employee with an initial salary history record.
- Employee creation and initial salary history creation are atomic.
- HR can record salary revisions and preserve salary history.
- HR can edit upcoming salary revisions before they become effective.
- No more than two upcoming salary revisions can exist per employee.
- New and edited upcoming revisions cannot have effective dates before today.
- Duplicate salary revision dates for the same employee are rejected.
- Upcoming revisions are displayed in effective-date order.
- Effective salary history cannot be edited through the application.
- Dashboard metrics are accurate and appropriately grouped.
- UI handles loading, empty, error and success states.
- Core APIs and business logic have meaningful automated test coverage.
- Search and filters work together correctly.
- Pagination works without loading all 10,000 records into the UI.
- Salary updates persist and create history records.
- Dashboard metrics reflect the underlying employee dataset.
- Salary calculations and analytics correctly account for currencies.
- API validation rejects invalid inputs.
- Core functionality has automated tests.

## Scope & Design Considerations

- The application focuses on employee compensation management and analysis rather than payroll processing or disbursement.
- Synthetic employee data will be used to simulate a realistic organizational dataset without exposing sensitive employee information.
- Server-side pagination, filtering, and search will be used to support efficient access to employee records at the expected data volume.
- Compensation analytics will account for currency differences to avoid misleading cross-country salary comparisons.
- The technical implementation will prioritize simplicity, maintainability, and practical scalability over unnecessary architectural complexity.
- Salary amounts are stored as integers in minor currency units to avoid floating-point precision issues.
- Salary amounts must not be aggregated across currencies without conversion.
- Future salary revisions are stored with effective dates but are not automatically activated in the MVP.
- Upcoming salary revision status is derived from the effective date; it is not stored as a separate employee-level boolean.
- An employee can have at most two upcoming salary revisions.
- Upcoming salary revisions can be edited before becoming effective.
- The salary, effective date and reason of an upcoming revision are editable.
- Effective dates for new or edited upcoming revisions cannot be earlier than the current date.
- Upcoming revisions must have unique effective dates per employee.
- Salary history must be preserved. Effective salary revisions are immutable through the application.
- Employee creation and initial salary-history creation must happen within a single database transaction.
- Salary revision writes and any related current-salary updates must happen within a single database transaction.
- Automatic future salary activation is outside the MVP scope. It can be implemented later using a background-processing mechanism such as an AWS scheduled or event-driven service.
- Salary updates must be atomic. If an operation fails, the transaction must roll back to prevent partial changes. The API must return an appropriate response and the UI must clearly inform HR of the outcome. Technical errors should be logged on the backend while user-facing messages remain meaningful and safe.
- `currentSalaryMinorUnits` represents the last applied salary.
- Scheduled salary revisions do not automatically become applied when
  their effective dates arrive.
- Current salary metrics use the last applied salary.
- Scheduled revisions are considered planning records until a future
  salary activation workflow is implemented.

### Notes:

- Salary mentioned above refers to annual salary, unless explicitly mentioned