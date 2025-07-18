---
aliases: ["Application"]
---


- is the core of the system
- contains Application Services which orchestrate the functionality or the use cases
- contains the Domain Model

Domain Model
: the business logic embedded in Aggregates, Entities, and Value Objects

The Application is represented by a hexagon which receives commands or queries from the Ports, and sends requests out to other external actors, like databases, via Ports as well.

![[Pasted image 20250619055306.png]]

When paired with Domain-Driven Design, the Application, or Hexagon, contains both the Application and the Domain layers, leaving the User Interface and Infrastructure layers outside.

A Hexagon is used to have a visual representation of the multiple Port/Adapter combinations an application might have, and also to depict how the left side of the application or 'driving side', has different interactions and implementations compared to the right side, or 'driven side'.