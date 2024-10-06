+++
title = 'Introduction to Clean Architecture'
date = 2024-09-30T19:58:16+03:00
draft = false
tags = ['Clean Architecture']
+++

# Introduction to Clean Architecture

## Software Architecture

> **Software architecture** defines the structure of a system, its components, their relationships, and design principles to ensure the system meets its functional and non-functional needs.

### Key Concepts
<details>
  <summary><strong>Components</strong></summary>
  <p>The building blocks of the system, including services, modules, and databases.</p>
</details>

<details>
  <summary><strong>Relationships</strong></summary>
  <p>Defines how components communicate and interact with each other.</p>
</details>

<details>
  <summary><strong>Design Principles</strong></summary>
  <p>Guidelines that ensure the system is scalable, maintainable, and secure.</p>
</details>

<details>
  <summary><strong>Architectural Patterns</strong></summary>
  <p>The overall structure used, such as microservices, layered architecture, etc.</p>
</details>

<details>
  <summary><strong>Technology Stack</strong></summary>
  <p>The programming languages, frameworks, and tools used to build the system.</p>
</details>

## Architecture Pattern:

> **Architecture Pattern** is a general, reusable resolution to a commonly occurring problem in software architecture within a given context.

## Examples of Architecture Patterns:

<table style="color:#000000;">
  <tr style="background-color:#edf2fb;">
    <th>Architecture Pattern</th>
    <th>Description</th>
    <th>Focus Area</th>
  </tr>

  <tr style="background-color:#edf2fb;">
    <td><button style="color:#000000;" onclick="toggleDetails('ntier')"><details><summary><strong>N-tier/Layered Architecture</strong></summary></details></button></td>
    <td>Organizes software into layers, each responsible for a specific aspect (e.g., presentation, business logic, data access).</td>
    <td><span>Separation of concerns across layers</span></td>
  </tr>
  <tr id="ntier" style="display:none;">
    <td colspan="3">

### Layered Architecture Flow

The diagram represents the flow in an N-tier architecture where:

- The **Presentation Layer** interacts with the **Business Logic Layer**.
- The **Business Logic Layer** communicates with the **Data Access Layer**.
- The **Data Access Layer** accesses the **Database (DB)**.

Each layer is visually distinct, demonstrating the separation of concerns in a typical multi-tier architecture.

<img src="/images/layered-architecture-diagram1.png" alt="Layered Architecture">

### Architectural Issues

In this architecture, a presentation layer directly accesses the data repository, as shown in the following code:

```csharp
// Tier N-2 (e.g., Presentation Layer)
var user = _userRepository.GetById(userId);

// Tier N (Data Access Layer)
public interface IUserRepository
{
    User GetById(Guid id);
}
```
**Transitive Layer Access** - layers become dependent on each other, making the system harder to maintain and scale.

   </td>
  </tr>

  <tr style="background-color:#d7e3fc;">
    <td><button style="color:#000000;" onclick="toggleDetails('hexagonal')"><details><summary><strong>Hexagonal/Ports and Adapters Architecture</strong></summary></details></button></td>
    <td>Focuses on keeping the core logic independent of external factors (e.g., databases, UI, APIs).</td>
    <td><span>Independence from external systems</span></td>
  </tr>
  <tr id="hexagonal" style="display:none;">
    <td colspan="3">
      <img src="/images/hexagonal-architecture-diagram.png" alt="Hexagonal Architecture Diagram">
    </td>
  </tr>

  <tr style="background-color:#ccdbfd;">
    <td><button style="color:#000000;" onclick="toggleDetails('microservices')"><details><summary><strong>Microservices Architecture</strong></summary></details></button></td>
    <td>Structures applications as a collection of loosely coupled, independently deployable services.</td>
    <td><span>Decoupling and scalability</span></td>
  </tr>
  <tr id="microservices" style="display:none;">
    <td colspan="3">
      <img src="/images/microservices-architecture-diagram.png" alt="Microservices Architecture Diagram">
    </td>
  </tr>

  <tr style="background-color:#c1d3fe;">
    <td><button style="color:#000000;" onclick="toggleDetails('clean')"><details><summary><strong>Clean Architecture</strong></summary></details></button></td>
    <td>Emphasizes separating concerns and keeping core logic independent of infrastructure and frameworks.</td>
    <td><span>Independence of core logic</span></td>
  </tr>
  <tr id="clean" style="display:none;">
    <td colspan="3">

### Clean Architecture

Clean architecture separates the software into layers with a **dependency rule** that layers only point **inwards**.

- **Inner layers** contain the **business logic**.
- **Outer layers** contain **infrastructure** and **interaction with the outer world**.

This structure helps maintain a clear separation of concerns and ensures that the core business logic is independent of external systems or user interface concerns.

<div style="display: flex; align-items: center;">
  <img src="/images/clean-architecture-diagram1.png" alt="Clean Architecture 1" width="400">
  <img src="/images/clean-architecture-diagram2.png" alt="Clean Architecture 2" width="300">
</div>

  </tr>

  <tr style="background-color:#b2c4fe;">
    <td><button style="color:#000000;" onclick="toggleDetails('soa')"><details><summary><strong>Service-Oriented Architecture (SOA)</strong></summary></details></button></td>
    <td>Breaks down systems into services that communicate over a network.</td>
    <td><span>Distributed services over a network</span></td>
  </tr>
  <tr id="soa" style="display:none;">
    <td colspan="3">
      <img src="/images/soa-architecture-diagram.png" alt="SOA Architecture Diagram">
    </td>
  </tr>

  <tr style="background-color:#a1b7ff;">
    <td><button style="color:#000000;" onclick="toggleDetails('modular')"><details><summary><strong>Modular Monolith Architecture</strong></summary></details></button></td>
    <td>Combines modular components within a single deployable unit, allowing easier refactoring and scaling.</td>
    <td><span>Modularity within a single unit</span></td>
  </tr>
  <tr id="modular" style="display:none;">
    <td colspan="3">
      <img src="/images/modular-monolith-architecture-diagram.png" alt="Modular Monolith Architecture Diagram">
    </td>
  </tr>

  <tr style="background-color:#97aaff;">
    <td><button style="color:#000000;" onclick="toggleDetails('eventdriven')"><details><summary><strong>Event-Driven Architecture</strong></summary></details></button></td>
    <td>Uses events as triggers for communication between decoupled components or services.</td>
    <td><span>Asynchronous event handling</span></td>
  </tr>
  <tr id="eventdriven" style="display:none;">
    <td colspan="3">
      <img src="/images/event-driven-architecture-diagram.png" alt="Event-Driven Architecture Diagram">
    </td>
  </tr>

  <tr style="background-color:#8d9dff;">
    <td><button style="color:#000000;" onclick="toggleDetails('mvc')"><details><summary><strong>MVC, MVP, MVVM</strong></summary></details></button></td>
    <td>Patterns for organizing the presentation layer in software, separating concerns between the model (data), view (UI), and controller/presenter (logic).</td>
    <td><span>Presentation layer organization</span></td>
  </tr>
  <tr id="mvc" style="display:none;">
    <td colspan="3">
      <img src="/images/mvc-mvp-mvvm-architecture-diagram.png" alt="MVC, MVP, MVVM Architecture Diagram">
    </td>
  </tr>
</table>

<script>
function toggleDetails(id) {
  var element = document.getElementById(id);
  if (element.style.display === "none") {
    element.style.display = "table-row";
  } else {
    element.style.display = "none";
  }
}
</script>
