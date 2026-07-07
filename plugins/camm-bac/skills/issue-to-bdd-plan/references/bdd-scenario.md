# BDD Scenarios

Learn to write clear, maintainable BDD scenarios that effectively capture business requirements and drive implementation.

Use bdd-scenarios when you need to:

- Define acceptance criteria for user stories
- Create comprehensive test coverage
- Identify edge cases early in development
- Communicate requirements clearly
- Define behavior before implementation
- Create living documentation from tests
- Bridge communication between developers and business
- Ensure features meet business requirements

## Writing Good Scenarios

A good scenario should be:

- **Specific**: Test one behavior
- **Declarative**: Describe what, not how
- **Business-focused**: Use domain language
- **Independent**: No dependencies on other scenarios

```gherkin
# Good scenario - specific and declarative
Scenario: Customer receives loyalty discount
  Given a customer with Gold membership status
  And a cart total of $100
  When the customer proceeds to checkout
  Then a 10% loyalty discount should be applied
  And the final total should be $90

# Bad scenario - too implementation-focused
Scenario: Apply discount
  Given I click the membership dropdown
  And I select "Gold" from the list
  When I click the checkout button
  Then the JavaScript calculates 10% off
```

## Best Practices

- Start with the happy path scenario
- Add edge cases systematically
- Use tags for organization and filtering
- Keep scenarios at 3-7 steps
- Write scenarios before implementation
- Review scenarios with stakeholders

- Write scenarios from the user's perspective
- Keep scenarios focused on single behaviors
- Use declarative language, not implementation details
- Reuse step definitions across scenarios
- Use Background for common setup steps
- Keep feature files organized by domain area

## Common Pitfalls

- Writing scenarios after implementation
- Including too many steps per scenario
- Using vague or ambiguous language
- Forgetting negative test cases
- Not organizing with tags effectively
- Writing scenarios that are too technical
- Coupling steps to specific UI implementations
- Creating overly complex scenario outlines
- Not maintaining feature files as code changes
- Mixing multiple behaviors in one scenario
