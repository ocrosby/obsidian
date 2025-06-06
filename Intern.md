# Full Stack Intern Project Plan

## Overview

This project is a full-stack application designed to:

- Accept test results in **JUnit** and **ReadyAPI** formats from various CI systems (GitHub Actions, Travis, Jenkins) via a **Go-based CLI**
    
- Post the test results to a **Go-based REST API**
    
- Store and manage test results in a **PostgreSQL** database
    
- Display test results over time using a **React frontend dashboard**
    

The project provides experience in:

- Backend development with Go
    
- Frontend development with React
    
- Working with RESTful APIs
    
- Using relational databases
    
- Unit, integration, and end-to-end testing
    

---

## System Architecture Diagram

```
+------------------------+        +----------------+        +---------------------+
|   CLI / Build Agent    +------->+     REST API    +------->+  PostgreSQL DB       |
| (junit/readyapi input) |        | (Go-based)      |        | (test results store) |
+------------------------+        +--------+-------+        +-----------+---------+
                                            |
                                            v
                                    +---------------+
                                    | React Frontend|
                                    +---------------+
```

---

## Getting Started Guide

1. **Install Required Tools**
    
    - Go
        
    - Node.js & npm
        
    - Docker
        
2. **Clone the Repository**
    
3. **Start the Project**
    
    ```bash
    docker-compose up -d       # start Postgres
    cd api && go run main.go   # start API
    cd frontend && npm start   # start React app
    ```
    
4. **Seed Database with Test Data**
    
5. **Run CLI with Sample Test Files**
    

---

## Contribution Guide

- Use feature branches
    
- Follow code formatting conventions (`gofmt`, Prettier)
    
- Run tests before PRs
    
- Use pre-commit hooks if enabled
    

---

## Project Layout (Monorepo)

```
fullstack-test-tracker/
├── api/               # Go REST API
│   ├── main.go
│   ├── handlers/
│   ├── models/
│   └── tests/
├── cli/               # Go-based CLI
│   ├── main.go
│   ├── parsers/
│   └── uploader/
├── frontend/          # React App
│   ├── public/
│   ├── src/
│   └── tests/
├── db/                # SQL migrations
│   └── schema.sql
├── docker-compose.yml
├── Makefile
├── README.md
└── docs/
```

---

## Milestone Plan

### Week 1: Project Setup & Orientation

- Install Go, Node, Docker
    
- Clone and run each component
    
- Seed DB with mock data
    
- Intro to REST & Postgres schema
    

### Week 2: Backend Development

- Create endpoints: `/tests`, `/suites`, `/runs`
    
- Implement ingestion logic
    
- Add unit tests
    

### Week 3: CLI Development

- Parse JUnit and ReadyAPI XML
    
- POST to API
    
- Add flags for CI system metadata
    

### Week 4: Frontend UI

- Dashboard for test runs
    
- Filters by CI system
    
- Test details page
    
- Use Axios or React Query
    

### Week 5+: Testing & Enhancements

- Unit (Go, React), Integration (Go), E2E (Cypress/Playwright)
    
- CI pipelines
    
- Authentication (optional)
    

---

## Key Concepts to Learn

- API design and REST
    
- Git workflows (feature branches, PRs)
    
- Data mocking and fixtures
    
- Testing layers
    
- Using Docker for dev environments
    

---

## Optional

If needed, a GitHub repo template with scaffolding can be provided to accelerate project setup.