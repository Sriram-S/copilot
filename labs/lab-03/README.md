# Lab 03: API Test Generation from OpenAPI Specification

## Objective
Generate a complete API test suite from an OpenAPI specification using Copilot.

## Duration
45 minutes

## Prerequisites
- VS Code with GitHub Copilot installed
- Node.js 18+

## Setup

```bash
mkdir copilot-lab-03 && cd copilot-lab-03
npm init -y
npm install --save-dev jest @types/jest ts-jest supertest @types/supertest typescript axios
```

---

## The Target API

We'll use the public JSONPlaceholder API (https://jsonplaceholder.typicode.com/) which has a simple REST interface. The "OpenAPI spec" we'll provide is this description:

```
Base URL: https://jsonplaceholder.typicode.com

Endpoints:
GET /posts          - List all posts (returns array of 100 posts)
GET /posts/:id      - Get single post (id: 1-100)
POST /posts         - Create a post (body: {title, body, userId})
PUT /posts/:id      - Update a post (full update)
PATCH /posts/:id    - Partial update
DELETE /posts/:id   - Delete a post (returns 200 with empty body)
GET /posts/:id/comments - Get comments for a post

Post schema:
{ id: number, title: string, body: string, userId: number }

Comment schema:
{ id: number, postId: number, name: string, email: string, body: string }
```

---

## Exercise 1: Generate API Client

In Copilot Chat:

```
Generate a TypeScript API client class for JSONPlaceholder's Posts API.

Base URL: https://jsonplaceholder.typicode.com

Include methods for all CRUD operations on /posts:
- getAllPosts(): Promise<Post[]>
- getPost(id: number): Promise<Post>
- createPost(post: Omit<Post, 'id'>): Promise<Post>
- updatePost(id: number, post: Post): Promise<Post>
- patchPost(id: number, partial: Partial<Post>): Promise<Post>
- deletePost(id: number): Promise<void>
- getPostComments(id: number): Promise<Comment[]>

Define the Post and Comment TypeScript interfaces.
Use axios for HTTP requests.
Include error handling.

Output: src/api/postsClient.ts
```

---

## Exercise 2: Generate Comprehensive Tests

In Copilot Chat:

```
Using #file:src/api/postsClient.ts, generate comprehensive API tests.

For each endpoint, test:
1. Happy path - valid request returns expected status and response schema
2. Invalid ID (9999) - appropriate error response
3. Response time - must be under 3 seconds

Additional for POST:
4. Missing required fields - what happens?
5. Extra unknown fields - are they ignored or rejected?

Use Jest + TypeScript.
Validate response schema using type checking (use type assertions).
Use beforeAll/afterAll for any setup.

Output: tests/posts.api.spec.ts
```

---

## Exercise 3: Add Response Schema Validation

```
Update #file:tests/posts.api.spec.ts to add strict response schema validation.

For each response, verify:
1. All expected fields are present
2. Field types match the interface definition
3. No extra unexpected fields (optional but good practice)

Use a helper function validatePostSchema(post: unknown): asserts post is Post
that checks every field.

Add this helper to: src/helpers/schemaValidators.ts
Import and use it in the test file.
```

---

## Exercise 4: Data-Driven API Tests

```
Generate data-driven tests for the POST /posts endpoint.

Test data table should include:
| scenario | title | body | userId | expectedStatus | expectSuccess |
|----------|-------|------|--------|----------------|---------------|
| valid post with all fields | "Test Title" | "Test Body" | 1 | 201 | true |
| post with very long title (500 chars) | ... | ... | 1 | 201 | true |
| post with userId as string (not number) | ... | ... | "1" | ??? | ??? |
| post with missing title | undefined | "Body" | 1 | ??? | ??? |

Note: JSONPlaceholder is lenient (it accepts most inputs and always returns 201).
Document which cases you'd expect to fail in a stricter real API.

Use Jest's test.each() or describe.each() for the data-driven pattern.
```

---

## Exercise 5: Performance Assertions

```
Add performance assertions to #file:tests/posts.api.spec.ts.

For each test:
1. Record start time before the request
2. Record end time after the response
3. Assert the response time is within SLA:
   - GET requests: < 1000ms
   - POST/PUT/PATCH requests: < 2000ms
   - DELETE requests: < 500ms

Create a custom Jest matcher: expect(duration).toBeFasterThan(ms)
Define it in: src/helpers/customMatchers.ts
```

---

## Running the Tests

```bash
npx jest tests/posts.api.spec.ts --verbose
```

---

## Reflection Questions

1. How did having the API client file as context improve the test generation?
2. What aspects of the generated tests would you change for a real production API?
3. How would you generate tests for an API that requires authentication?

---

## Expected Outcome

- [ ] `src/api/postsClient.ts` - TypeScript API client
- [ ] `tests/posts.api.spec.ts` - Comprehensive test suite
- [ ] `src/helpers/schemaValidators.ts` - Schema validation helpers
- [ ] `src/helpers/customMatchers.ts` - Custom Jest matchers
- [ ] All tests passing

---

## Next Lab

➡️ [Lab 04: Agent Mode - Full Project Scaffold](../lab-04/README.md)
