# Introduction & Scope of the Project

## Goal

The goal of this project is to build a web-application for salary management that enables efficient payroll information for 10,000+ employees of the ACME organisation.

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

# What is being left out deliberately

- Payroll processing and disbursement
- Real employee data or integrations with HR/Payroll systems
- Tax calculations across different countries
- Bonuses, stock compensations, other complex benefit calculations
- Production grade authentication and enterprise SSO.

# Future enhancements possible

- Employee onboarding requirements, progress
- Performance feedbacks, to understand salary increment trends
- Tax calculations, country-wise

# Verification & Acceptance

- 10,000 seeded employees data can be loaded, queried as expected
- HR can search, filter, paginate, view and edit salary information
- Dashboard metrics are accurate and appropriately grouped
- UI handles loading, empty, error and success states efficiently
- Core APIs and business logic have meaningful automated test coverage
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

### Notes:

- Salary mentioned above refers to annual salary, unless explicitly mentioned