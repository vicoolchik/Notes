+++
title = 'Introduction to Clean Architecture'
date = 2024-09-30T19:58:16+03:00
draft = false
tags = ['Clean Architecture']
+++

# Introduction to Clean Architecture

## Software Architecture 

> **Software architecture** defines the structure of a system, its components, their relationships, and design principles to ensure the system meets its functional and non-functional needs.

#### Key Concepts

<p class="definition-term"><strong>Components</strong></p>
<div class="definition-content">The building blocks of the system, including services, modules, and databases.</div>

<p class="definition-term"><strong>Relationships</strong></p>
<div class="definition-content">Defines how components communicate and interact with each other.</div>

<p class="definition-term"><strong>Design Principles</strong></p>
<div class="definition-content">Guidelines that ensure the system is scalable, maintainable, and secure.</div>

## Architecture Pattern:

> **Architecture Pattern** is a general, reusable resolution to a commonly occurring problem in software architecture within a given context.

 ## Examples of Architecture Patterns:

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