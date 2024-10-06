---
config:
  theme: neo
  look: neo
  layout: fixed
---
flowchart TB
    C["Infrastructure"] --> E[("DB")] & B["Application"]
    B --> D["Domain"]
    A["Presentation"] --> B
    C@{ shape: rounded}
    B@{ shape: rounded}
    D@{ shape: rounded}
    A@{ shape: rounded}
    style C fill:#C8E6C9,stroke:#333,stroke-width:1px,stroke-dasharray: 0
    style E fill:#f5d3ff,stroke:#333,stroke-width:2px
    style B fill:#FFCDD2,stroke:#333,stroke-width:1px,stroke-dasharray: 0
    style D fill:#FFECB3,stroke:#333,stroke-width:1px,stroke-dasharray: 0
    style A fill:#BBDEFB,stroke:#333,stroke-width:1px,stroke-dasharray: 0
