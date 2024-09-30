+++
title = 'Introduction to Clean Architecture'
date = 2024-09-30T19:58:16+03:00
draft = false
tags = ['Clean Architecture']
+++

# Software Architecture Overview

**Software architecture** defines the structure of a system, its components, their relationships, and design principles to ensure the system meets its functional and non-functional needs.

## Key Concepts

- **Components**: The building blocks of the system, including services, modules, and databases.
- **Relationships**: Defines how components communicate and interact with each other.
- **Design Principles**: Guidelines that ensure the system is scalable, maintainable, and secure.
- **Architectural Patterns**: The overall structure used, such as microservices, layered architecture, etc.
- **Technology Stack**: The programming languages, frameworks, and tools used to build the system.

## Definition of a Architecture Pattern:

> "A general, reusable resolution to a commonly occurring problem in software architecture within a given context."

## Examples of Architecture Patterns:

| Architecture Pattern |
|----------------------|
| <details><summary><strong>N-tier/Layered Architecture</strong></summary><div><p>This architecture divides the system into multiple layers, each responsible for a specific aspect of the application:</p><ul><li><strong>Presentation Layer</strong>: Manages the user interface and user input.</li><li><strong>Business Logic Layer</strong>: Contains the core functional logic of the application.</li><li><strong>Data Access Layer</strong>: Responsible for accessing data from databases or other persistent storage.</li></ul></div></details> |
| <details><summary><strong>Hexagonal/Ports and Adapters Architecture</strong></summary><div><p>Hexagonal architecture focuses on keeping the core logic independent of external factors such as databases, UI, and APIs. It provides flexibility by allowing adapters to connect to the system via predefined ports.</p><ul><li><strong>Ports</strong>: Define the operations that the application provides or requires.</li><li><strong>Adapters</strong>: External interfaces such as databases, APIs, and UIs that communicate with the core through ports.</li></ul></div></details> |
| <details><summary><strong>Microservices Architecture</strong></summary><div><p>Microservices architecture structures an application as a collection of loosely coupled, independently deployable services that are responsible for specific business functionalities. These services communicate with each other via APIs or messaging systems.</p><ul><li><strong>Service Independence</strong>: Each service can be deployed, scaled, and maintained independently.</li><li><strong>Communication</strong>: Services communicate via lightweight protocols such as HTTP or messaging queues.</li></ul></div></details> |
| <details><summary><strong>Clean Architecture</strong></summary><div><p>Clean architecture emphasizes separating concerns and keeping core logic independent of external infrastructure and frameworks, allowing the system to be adaptable and maintainable over time.</p><ul><li><strong>Entities</strong>: Represent the core business objects.</li><li><strong>Use Cases</strong>: Application-specific business rules that define how entities are used.</li><li><strong>Interfaces</strong>: External services such as databases, UI, and APIs are implemented through interfaces.</li></ul></div></details> |
| <details><summary><strong>Service-Oriented Architecture (SOA)</strong></summary><div><p>SOA divides the system into a collection of services that communicate over a network. Each service is self-contained and performs a specific business function, with the services loosely coupled via standard protocols like HTTP or SOAP.</p><ul><li><strong>Service Interoperability</strong>: Services can interact regardless of the underlying technology used to implement them.</li><li><strong>Loose Coupling</strong>: Services are designed to minimize dependencies, making them flexible and reusable.</li></ul></div></details> |
| <details><summary><strong>Modular Monolith Architecture</strong></summary><div><p>Modular Monolith Architecture structures an application into modular components while deploying them as a single, unified system. This allows better separation of concerns while maintaining the benefits of a monolithic deployment.</p><ul><li><strong>Modularity</strong>: The system is divided into smaller modules that can be independently developed and tested.</li><li><strong>Unified Deployment</strong>: Despite the modularity, the system is deployed as a single unit.</li></ul></div></details> |
| <details><summary><strong>Event-Driven Architecture</strong></summary><div><p>Event-Driven Architecture is based on the concept of emitting and reacting to events. Components in the system communicate asynchronously by producing and consuming events, which allows for loosely coupled interactions between services.</p><ul><li><strong>Event Producers</strong>: Components that emit events when something of interest happens.</li><li><strong>Event Consumers</strong>: Components that react to events and trigger actions accordingly.</li></ul></div></details> |
| <details><summary><strong>MVC, MVP, MVVM</strong></summary><div><p>MVC (Model-View-Controller), MVP (Model-View-Presenter), and MVVM (Model-View-ViewModel) are patterns used to organize the presentation layer by separating the data (model), user interface (view), and control logic (controller/presenter/viewmodel).</p><ul><li><strong>MVC</strong>: Controller handles the interaction between Model (data) and View (UI).</li><li><strong>MVP</strong>: Presenter mediates between the Model and the View, with more logic pushed into the Presenter.</li><li><strong>MVVM</strong>: ViewModel manages the data and business logic, exposing it to the View, which binds directly to properties in the ViewModel.</li></ul></div></details> |
