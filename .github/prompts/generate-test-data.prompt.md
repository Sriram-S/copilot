---
mode: agent
description: Add test data to the TestDataFactory for a new entity type. Provide the entity schema and this prompt generates the factory methods.
---

You are a TypeScript expert creating test data factories.

Read `src/data/TestDataFactory.ts` first to understand the existing factory structure.
If it doesn't exist, create it from scratch following the pattern below.

## Entity Details (fill in below)

**Entity Name:** [EntityName]
**Description:** [What this entity represents]

**Fields:**
```
fieldName: type (required/optional) - description
fieldName: type (required/optional) - description
(add all fields)
```

**Example valid entity:**
```json
{
  "field1": "example value",
  "field2": 42
}
```

**Constraints:**
- [List any field constraints: max length, valid values, format requirements]
- [List any uniqueness requirements: email must be unique, etc.]
- [List any relationships: entity B must reference valid entity A]

## Generation Requirements

### Dependencies
Use `@faker-js/faker` for realistic data generation:
```typescript
import { faker } from '@faker-js/faker';
```

### TypeScript Interface
First generate the TypeScript interface:
```typescript
export interface [EntityName] {
  id: string;           // auto-generated, not needed in create()
  fieldName: type;
  optionalField?: type;
}

// For create operations (without server-generated fields)
export type Create[EntityName]Input = Omit<[EntityName], 'id' | 'createdAt' | 'updatedAt'>;
```

### Factory Methods to Generate

#### 1. Create Single Entity
```typescript
create[EntityName](overrides: Partial<Create[EntityName]Input> = {}): Create[EntityName]Input {
  return {
    // All required fields with realistic faker values
    fieldName: faker.relevant.method(),
    // Merge overrides last so they take priority
    ...overrides,
  };
}
```

#### 2. Create Many Entities
```typescript
createMany[EntityName](
  count: number, 
  overrides: Partial<Create[EntityName]Input> = {}
): Create[EntityName]Input[] {
  return Array.from({ length: count }, () => this.create[EntityName](overrides));
}
```

#### 3. Create Edge Case Variants (if applicable)
If the entity has meaningful variants, generate helper methods:
```typescript
create[EntityName]WithMinimalFields(): Create[EntityName]Input
create[EntityName]WithAllOptionalFields(): Create[EntityName]Input
create[EntityName]WithInvalidEmail(): Create[EntityName]Input  // for negative tests
```

### Faker Field Mapping Guidelines

| Field Type | Faker Method |
|---|---|
| Unique email | `faker.internet.email() + Date.now()` |
| Name | `faker.person.firstName()` / `faker.person.lastName()` |
| Phone | `faker.phone.number()` |
| Address | `faker.location.streetAddress()` |
| UUID | `faker.string.uuid()` |
| Integer in range | `faker.number.int({ min: 1, max: 100 })` |
| Future date | `faker.date.future().toISOString()` |
| Company name | `faker.company.name()` |
| Description | `faker.lorem.sentence()` |
| URL | `faker.internet.url()` |
| Price | `faker.commerce.price()` |

### Uniqueness for Emails
Always ensure uniqueness:
```typescript
// Good - includes timestamp for uniqueness
email: `test-${Date.now()}-${faker.internet.email()}`,

// Good - faker unique (but can exhaust)
email: faker.helpers.unique(faker.internet.email),
```

## Integration

After adding to TestDataFactory:
1. Show the complete new methods added
2. Show an example of using the factory in a test:
```typescript
const factory = new TestDataFactory();
const user = factory.createUser({ role: 'admin' }); // override specific field
const users = factory.createManyUsers(5);            // create multiple
```
3. Check if any existing tests should be updated to use the factory instead of hardcoded values

## Output

Update or create: `src/data/TestDataFactory.ts`

Show:
- New interface/type added
- New factory methods added
- Usage example
- Any existing hardcoded data in tests that should now use this factory
