# Design Decisions

## 1. Monetary Representation

Salary amounts are stored as integers in minor currency units to avoid floating-point precision issues.

- Salary fields are stored as integer values.
- The employee's currency determines how salary amounts are formatted and displayed.
- The backend and frontend must consistently use minor units when exchanging salary values through the API.
- Salary analytics must group values by currency. Salaries from different currencies must not be summed without conversion.
- Currency conversion is outside the MVP scope.

Fields:
- `currentSalaryMinorUnits`
- `previousSalaryMinorUnits`
- `revisedSalaryMinorUnits`

## 2. Employee Creation

Employee creation captures employee details, department, joining date, currency and initial salary.

- Joining date defaults to the current date but can be changed by HR.
- Employee code must be unique.
- Required employee fields must be validated.
- Optional fields such as personal email and work email may be omitted.
- The employee's initial salary is stored in `Employee.currentSalaryMinorUnits`.
- An initial `SalaryHistory` record is created with:
  - `previousSalaryMinorUnits = null`
  - `revisedSalaryMinorUnits = initial salary`
  - `effectiveDate = employee joining date`
  - `reason = "Initial Salary"`

Employee creation and initial salary history creation must happen within a single database transaction. If either operation fails, both operations are rolled back to prevent an employee record from being created without its initial salary history.

## 3. Salary Revision Rules

Salary revisions are recorded in `SalaryHistory`.

- The employee must exist.
- Revised salary must be greater than zero.
- Effective date must be today or later when creating or editing an upcoming revision.
- A revision reason is required.
- Each employee can have a maximum of two upcoming salary revisions at a time.
- Duplicate effective dates for the same employee are not allowed.
- No minimum interval between salary revisions is enforced.
- Salary history entries are always presented in effective-date order.

### Upcoming Revision Editing

Upcoming salary revisions can be edited before they become effective.

The following fields are editable:
- Revised salary
- Effective date
- Reason

When editing an upcoming revision:
- The existing record is updated instead of creating another record.
- The effective date must remain today or later.
- The new effective date must not conflict with another salary revision for the employee.
- The maximum of two upcoming revisions per employee still applies.
- Editing an existing upcoming revision does not count as creating an additional upcoming revision.

Effective salary history is immutable through the application.

## 4. Salary Revision Behavior

- If a revision is effective today, the employee's current salary is updated along with the salary history record.
- If a revision is effective in the future, the revision is recorded but the employee's current salary remains unchanged.
- Future salary revisions are stored as upcoming revisions and are not automatically activated in the MVP.
- Upcoming revision status is derived from the effective date and current date.
- Salary revision operations must be transactional.
- If a write fails, rollback the transaction and return an appropriate error response.

## 5. Salary History

Salary history is maintained to preserve previous and revised salary information.

- Initial salary is recorded at employee creation.
- Subsequent salary revisions are recorded as new history entries.
- Upcoming salary revisions can be edited before they become effective.
- Effective salary history cannot be edited through the application.
- Salary revisions cannot be deleted through the application in the MVP.
- Salary history entries are displayed in effective-date order.
- Upcoming revisions are derived from their effective dates rather than stored as a separate flag on the employee.

## 6. Future Salary Revisions

- Future salary revisions are stored as scheduled salary history records.
- Upcoming status is derived from the effective date.
- Future revisions do not immediately update current salary.
- Revisions are not automatically activated when their effective date arrives.
- `currentSalaryMinorUnits` represents the last applied salary.
- Current compensation analytics use the last applied employee salary,
  not unapplied scheduled revisions.
- Scheduled revisions remain planning records until an activation
  mechanism is implemented.
- Automatic or manual salary activation is outside the MVP scope.

## 7. Employee Data Updates

Employee details can be updated independently of salary revisions.

- Employee profile updates must not directly modify salary fields.
- Salary changes must be recorded through the salary revision workflow.
- Updated employee details must maintain validation and uniqueness constraints.

## 8. Transactions

The following operations must be atomic:

### Employee Creation
- Insert employee.
- Insert initial salary history.
- Commit both operations together.

### Salary Revision
- Insert or update salary history.
- Update current employee salary when the revision is effective today.
- Commit all changes together.

If an operation fails, rollback all changes belonging to that operation.

## 9. Analytics

- Analytics are calculated from employee and salary history data.
- Salary amounts must be grouped by currency.
- Currency conversion is not included in the MVP.
- Upcoming revisions are identified from salary history effective dates.

## 10. API Design

The API follows REST conventions and separates employee management, salary revision operations, and analytics.

Employee salary changes must only occur through the salary revision workflow to maintain salary history consistency.

Detailed API contracts and error codes will be defined during implementation.