---
title: "Lab 08 — IntelliJ Power Session"
lab: 8
duration: "60 minutes"
tags: [intellij, jetbrains, java, spring-boot, junit5, mockito]
---

# Lab 08 — IntelliJ Power Session

## Objective

Use every IntelliJ-specific Copilot feature to build and test a Spring Boot
REST API in under 60 minutes. This lab is IntelliJ IDEA specific.

---

## Prerequisites

- IntelliJ IDEA 2024.3+ with Copilot plugin installed and signed in
- Java 21 JDK
- Maven or Gradle

---

## Part A — Project Bootstrap (10 min)

### Generate Spring Boot Project via Chat

In IntelliJ Copilot Chat:
```
Generate a Spring Boot 3.4 Maven project for a Task Management REST API.
Include these dependencies: Spring Web, Spring Data JPA, H2 (test scope), 
Spring Validation, Spring Security.

Create the standard Maven project structure.
The main package is: com.example.taskmanager

After creating pom.xml, ask me before creating any Java files.
```

---

## Part B — Generate the Domain Layer (15 min)

### Task 1: Entity via Comment Block

In a new Java file, type this Javadoc comment and let Copilot complete the class:

```java
/**
 * Task entity representing a unit of work in the task management system.
 * 
 * Fields:
 * - id: Long, auto-generated primary key
 * - title: String, not blank, max 100 chars
 * - description: String, max 500 chars, optional
 * - status: TaskStatus enum (TODO, IN_PROGRESS, DONE, CANCELLED)
 * - priority: TaskPriority enum (LOW, MEDIUM, HIGH, CRITICAL)
 * - assigneeId: Long, FK to User (not mapped, just the ID)
 * - dueDate: LocalDate, optional
 * - createdAt: LocalDateTime, auto-set on creation
 * - updatedAt: LocalDateTime, auto-set on update
 * 
 * JPA annotations: @Entity, @Table(name="tasks")
 * Include: equals, hashCode (by id), toString, builder pattern
 */
@Entity
@Table(name = "tasks")
public class Task {
```

### Task 2: Repository Interface

```java
/**
 * JPA repository for Task entities.
 * Custom queries needed:
 * - findByAssigneeIdAndStatus(Long assigneeId, TaskStatus status)
 * - findByDueDateBeforeAndStatusNot(LocalDate date, TaskStatus status)
 * - countByStatusGroupedByAssignee() — native query returning Map<Long, Long>
 */
```

---

## Part C — Generate REST Controller with Copilot (15 min)

Use right-click → **Ask Copilot** on the Task entity to generate the controller:

```
Generate a Spring REST controller for Task management:
- CRUD endpoints: GET /tasks, GET /tasks/{id}, POST /tasks, PUT /tasks/{id}, DELETE /tasks/{id}  
- Filtering: GET /tasks?assigneeId=1&status=TODO&page=0&size=20
- Validation: use @Valid on request bodies
- Error handling: @ExceptionHandler for NotFoundException, ValidationException
- Security: require authentication for all endpoints
- Return DTOs, not entities directly (create TaskDTO and CreateTaskRequest classes)
```

---

## Part D — Generate Tests via Context Menu (15 min)

### Method 1: Right-Click Generate Tests

1. Right-click on `TaskController.java`
2. Select **Generate Tests** (or **Ask Copilot → Generate Tests**)
3. IntelliJ will open a test configuration dialog
4. Select: JUnit 5, @WebMvcTest, generate all methods

### Method 2: Chat Command

If the right-click option isn't available:
```
/tests Generate @WebMvcTest tests for TaskController.
Use Mockito to mock TaskService.
Cover:
- GET /tasks (empty list, with results, with filters applied)
- GET /tasks/{id} (found, not found → 404)
- POST /tasks (valid body → 201, invalid body → 400, duplicate → 409)
- PUT /tasks/{id} (success, not found, validation failure)
- DELETE /tasks/{id} (success, not found)
- Unauthenticated requests (→ 401)
Use MockMvc for HTTP assertions.
```

---

## Part E — App Modernisation Exercise (5 min)

This exercises an IntelliJ-unique feature.

1. Create a legacy test file:

```java
// src/test/java/com/example/taskmanager/LegacyTaskTest.java
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.mockito.runners.MockitoJUnitRunner;
import static org.junit.Assert.*;

@RunWith(MockitoJUnitRunner.class)
public class LegacyTaskTest {
    @Mock
    TaskRepository repository;
    
    @Before
    public void setUp() {
        MockitoAnnotations.initMocks(this);
    }
    
    @Test
    public void testCreateTask() {
        assertTrue(true);
    }
}
```

2. In Copilot Chat:
```
Migrate LegacyTaskTest from JUnit 4 to JUnit 5.
Replace: @RunWith → @ExtendWith, @Before → @BeforeEach, 
org.junit.Assert → org.junit.jupiter.api.Assertions
Remove: MockitoAnnotations.initMocks() (use @ExtendWith(MockitoExtension.class))
```

---

## Part F — Debugger Integration (5 min)

1. Set a breakpoint in `TaskController.createTask()`
2. Run in debug mode and trigger the endpoint
3. When paused, copy the variable values from the debugger
4. In Copilot Chat: `Explain the state of the variables and why the validation is failing`

---

## ✅ Completion Criteria

- [ ] Spring Boot project created with all dependencies
- [ ] Task entity with full JPA annotations generated
- [ ] TaskController with all 5 CRUD endpoints generated
- [ ] @WebMvcTest test class generated (at least 8 test methods)
- [ ] All generated tests pass (`mvn test`)
- [ ] Legacy JUnit 4 test migrated to JUnit 5
- [ ] Debugger used with Copilot Chat for at least one question
