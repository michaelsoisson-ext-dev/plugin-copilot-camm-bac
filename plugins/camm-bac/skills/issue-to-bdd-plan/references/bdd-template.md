# Acceptance Criteria Format

```gherkin
Feature: [Feature name]
    As a [persona] I want to [actions] [conditions]

  # Rule-based acceptance criteria
  Rule: [Rule description]
    Scenario: [Scenario name]
    Given [context/precondition]
        When  [action/event]
        Then [expected outcome]

    Scenario:...

```

## Given-When-Then Structure

The fundamental BDD pattern uses three parts:

- **Given**: The initial context or preconditions
- **When**: The action or event being tested
- **Then**: The expected outcome or result

## Edge Case Scenarios

```gherkin
Feature: User Registration

  Scenario: Successful registration
    Given I am on the registration page
    When I submit valid registration details
    Then my account should be created

  # Edge cases
  Scenario: Registration with existing email
    Given a user exists with email "existing@example.com"
    When I try to register with email "existing@example.com"
    Then I should see "Email already registered"

  Scenario: Registration with invalid email format
    When I try to register with email "not-an-email"
    Then I should see "Please enter a valid email"

  Scenario: Registration with empty required fields
    When I submit the registration form with empty fields
    Then I should see validation errors for required fields
```
