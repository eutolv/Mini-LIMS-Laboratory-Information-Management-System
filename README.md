# Mini-LIMS

A terminal-first **Laboratory Information Management System (LIMS)** built with Bash, SQLite and Python.

The project models a simplified laboratory workflow from sample registration through testing, result validation, review and approval, with business rules enforced at the database level.

## Overview

This project was built as a practical exploration of how real-world **business processes and requirements can be translated into software workflows, data structures, validation rules and operational controls**.

Rather than implementing a simple CRUD application, the system focuses on workflow integrity, traceability, validation, auditability and Linux-based operations.

### Core workflow

```text
Client
  ↓
Sample
  ↓
Test Request
  ↓
Result
  ↓
Review
  ↓
Approval
```

Sample lifecycle:

```text
RECEIVED
   ↓
REGISTERED
   ↓
IN_TESTING
   ↓
COMPLETED
   ↓
REVIEWED
   ↓
APPROVED
```

## Key Features

### Laboratory Workflow

* Client management
* Sample registration
* Test requests
* Result entry and updates
* Sample status management
* Result flags: `NORMAL`, `LOW`, `HIGH`, `INVALID`
* Review and approval workflow
* Post-approval data locking
* Business-rule enforcement

### Data Integrity & Validation

* SQLite relational database
* Database-level constraints and triggers
* Validation of laboratory results
* Protection against invalid workflow transitions
* Protection against direct database writes bypassing business rules
* Transactional operations

### Audit Trail

Every relevant business change is recorded with:

* User
* Action
* Timestamp
* Entity
* Before value
* After value

The audit trail is designed to provide traceability across the laboratory workflow.

### CLI

The system is operated primarily through a Bash command-line interface.

Examples:

```bash
lab client list

lab sample create \
  --client ACME \
  --type WATER \
  --collected 2026-09-29

lab test request SAM-0001 PH

lab result add SAM-0001 PH 7.42

lab sample status SAM-0001

lab audit SAM-0001
```

## REST API

A lightweight REST API is implemented in Python using the standard library.

The API reuses the CLI/business workflow instead of maintaining a separate implementation of the same rules.

This provides a simple example of exposing an existing business application through an HTTP interface while keeping behavior consistent between interfaces.

## Linux & Operations

The project also includes operational tooling for a Linux environment:

* Database initialization
* Health checks
* Online database backup
* Backup checksum verification
* Safe restore procedures
* Log management
* Scheduled operations with `cron`
* `flock`-based execution protection
* Restricted file permissions
* Shell-based automation

## Testing

The project includes automated validation across multiple layers.

Current test suite:

| Test area      |  Checks |
| -------------- | ------: |
| CLI validation |      25 |
| Business rules |      61 |
| CSV import     |      22 |
| Operations     |      22 |
| REST API       |       6 |
| **Total**      | **135** |

The test suite includes both positive and negative scenarios, including attempts to bypass business rules through direct database operations.

Run the complete suite with:

```bash
tests/run.sh
```

## Architecture

```text
                ┌─────────────┐
                │  Bash CLI   │
                └──────┬──────┘
                       │
                       ▼
                ┌─────────────┐
                │   Business  │
                │    Rules    │
                └──────┬──────┘
                       │
              ┌────────┴────────┐
              ▼                 ▼
        ┌───────────┐     ┌───────────┐
        │  SQLite   │     │ Audit Log │
        └───────────┘     └───────────┘
              ▲
              │
        ┌─────┴─────┐
        │ REST API  │
        │  Python   │
        └───────────┘
```

The database is not treated simply as passive storage: important integrity rules are enforced at the database level to reduce the risk of inconsistent state.

## Technology Stack

**Languages & Runtime**

* Bash 4.4+
* Python 3
* SQL / SQLite

**Operating Environment**

* Linux
* Bash CLI
* cron
* standard Unix utilities

**Application Concepts**

* REST API
* Relational database design
* Business rules
* Workflow management
* Data validation
* Audit logging
* Role-based operations
* Automated testing
* Backup and restore
* Security controls

## What This Project Demonstrates

### Business & Functional

* Translating business processes into software workflows
* Modeling entities and relationships
* Defining business rules and state transitions
* Designing validation requirements
* Implementing traceability and approval workflows

### Technical

* Bash scripting
* Linux command-line operations
* SQL and relational database design
* SQLite triggers and constraints
* Python REST API development
* CLI application design
* Data import and automation

### Quality & Reliability

* Automated testing
* Negative testing
* Database integrity testing
* Workflow validation
* Auditability
* Operational safeguards

### Implementation Perspective

The project intentionally sits at the intersection of **business systems and software implementation**.

The goal was not only to make the application work, but to model how requirements, workflows, validation rules, user roles and operational processes become an actual working system.

## Project Structure

```text
mini-lims/
├── bin/
├── config/
├── data/
├── database/
├── docs/
├── migrations/
├── scripts/
├── src/
└── tests/
```

See the `docs/` directory for architecture, database design, workflow, API, operations and testing documentation.

## Disclaimer

This is an educational and portfolio project inspired by real-world laboratory information management workflows.

It is **not intended for production laboratory use** and does not implement the security, regulatory validation, authentication, infrastructure and operational controls required for a production LIMS.

## Author

Built as a hands-on project combining **Business Systems, Enterprise Software Implementation, Linux, Bash, databases and software engineering practices**.
