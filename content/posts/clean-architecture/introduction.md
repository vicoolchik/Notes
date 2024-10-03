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

 ```mermaid
graph TD;
    A[Presentation Layer] --> B[Business Logic Layer];
    B --> C[Data Access Layer];
    C --> D[(DB)];
    
    style A fill:#ADD8E6,stroke:#333,stroke-width:2px;
    style B fill:#FFB6C1,stroke:#333,stroke-width:2px;
    style C fill:#90EE90,stroke:#333,stroke-width:2px;
    style D fill:#D8BFD8,stroke:#333,stroke-width:2px;
```

| **Architecture Pattern**  | **Description**                                                                                                                                 | **Focus Area**                                  |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------|
| **N-tier/Layered Architecture**        | Organizes software into layers, each responsible for a specific aspect (e.g., presentation, business logic, data access).                                      | <span style="color:#f0e68c;">Separation of concerns across layers</span>           |
| **Hexagonal/Ports and Adapters Architecture** | Focuses on keeping the core logic independent of external factors (e.g., databases, UI, APIs).                                                               | <span style="color:#62a1db;">Independence from external systems</span>             |
| **Microservices Architecture**          | Structures applications as a collection of loosely coupled, independently deployable services.                                                               | <span style="color:#f0e68c;">Decoupling and scalability</span>                     |
| **Clean Architecture**                 | Emphasizes separating concerns and keeping core logic independent of infrastructure and frameworks.                                                          | <span style="color:#62a1db;">Independence of core logic</span>                     |
| **Service-Oriented Architecture**        | Breaks down systems into services that communicate over a network.                                                                                           | <span style="color:#f0e68c;">Distributed services over a network</span>            |
| **Modular Monolith Architecture**        | Combines modular components within a single deployable unit, allowing easier refactoring and scaling.                                                        | <span style="color:#62a1db;">Modularity within a single unit</span>                |
| **Event-Driven Architecture**            | Uses events as triggers for communication between decoupled components or services.                                                                          | <span style="color:#f0e68c;">Asynchronous event handling</span>                    |
| **MVC, MVP, MVVM**                       | Patterns for organizing the presentation layer in software, separating concerns between the model (data), view (UI), and controller/presenter (logic).        | <span style="color:#62a1db;">Presentation layer organization</span>                |