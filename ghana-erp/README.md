# Ghana ERP - Enterprise Resource Planning Platform

> A production-grade, multi-tenant ERP system architected for horizontal scalability and regulatory compliance in emerging markets

## Executive Summary

Ghana ERP is a comprehensive enterprise resource planning platform engineered to solve the complex challenge of business process digitization across multiple organizational tenants. The system addresses critical pain points in financial management, inventory control, human resources, procurement, and regulatory compliance—specifically designed for jurisdictions requiring strict audit trails and e-VAT integration.

Built on a **microservices-oriented monorepo architecture**, this platform demonstrates advanced software engineering patterns including domain-driven design (DDD), event-driven architecture (EDA), and robust multi-tenancy at the database level. The system is designed for **horizontal scaling**, enabling the platform to serve thousands of concurrent users across isolated tenant contexts without data leakage or performance degradation.

---

## System Architecture

### High-Level Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                         Client Layer (Web App)                       │
│                    (React 19 + Vite + TanStack Query)               │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                │ HTTPS/REST API
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                          API Gateway Layer                           │
│        (NestJS + Swagger/OpenAPI + JWT Authentication)              │
│              Rate Limiting │ Request Validation │ RBAC               │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                │ Internal Module Communication
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                        Business Logic Layer                          │
│                      (Domain-Driven Modules)                         │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ │
│  │   Auth   │ │ Finance  │ │    HR    │ │Inventory │ │  Sales   │ │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘ └──────────┘ │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐              │
│  │Compliance│ │Procuremnt│ │  Common  │ │ Notifs   │              │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘              │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                │ Event Bus (EventEmitter2)
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                      Infrastructure Layer                            │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐    │
│  │  Caching Layer  │  │  Queue System   │  │  Email Service  │    │
│  │     (Redis)     │  │   (BullMQ)      │  │   (Nodemailer)  │    │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘    │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                │ Prisma ORM + Connection Pooling
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                      Persistence Layer                               │
│                   PostgreSQL (Multi-tenant Schema)                   │
│       Row-Level Security │ Tenant Isolation │ Audit Logging         │
└─────────────────────────────────────────────────────────────────────┘
                                │
                                │ Observability Stack
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│              Prometheus + Grafana + Loki (Logging)                   │
│          Distributed Tracing │ Metrics │ Health Checks               │
└─────────────────────────────────────────────────────────────────────┘
```

**Architecture Diagrams (Draw.io)**

Diagrams for Ghana ERP have been created and are available in the Draw.io file: [Ghana ERP Diagrams - Draw.io](https://drive.google.com/file/d/1FtMUtqF33-xPjxBSxLvFyzylhNuviUJ3/view?usp=sharing)

Included diagrams (in the Draw.io file):
- **System Architecture** — Frontend → API Gateway → Module Layer → Database
- **Multi-tenant Data Flow** — Login/auth flow with tenant context and RLS
- **Event-Driven Communication** — Events between Sales, Finance, Inventory, Notifications
- **Database Schema** — ER diagram (tables & relationships)
- **Observability & Infrastructure** — Prometheus, Grafana, Loki, Redis, BullMQ

How to view:
- Open the link above and choose **Open with > diagrams.net** (or download PNG/SVG exports)

If you'd like, I can export PNG/SVGs from the Draw.io file and commit them to `/projects-showcase/ghana-erp/diagrams/` and update this README with the file paths — tell me and I will do that.
---

## Core Technical Features

### 🏗️ Multi-Tenant Architecture
- **Tenant Isolation Strategy**: Implements schema-based multi-tenancy using Prisma with PostgreSQL's row-level security (RLS)
- **Context Propagation**: Tenant ID injected via custom decorators and maintained throughout the request lifecycle
- **Data Segregation**: Zero cross-tenant data leakage through database-level constraints and application-layer guards

### 🔐 Enterprise-Grade Security
- **Authentication & Authorization**: JWT-based authentication with role-based access control (RBAC) using custom decorators
- **Password Security**: Bcrypt hashing with configurable salt rounds
- **API Protection**: Rate limiting (configurable per endpoint), request throttling, and DDoS mitigation
- **Audit Trail**: Comprehensive logging of all CRUD operations with user attribution

### 📊 Type-Safe API Layer
- **Contract-First Development**: OpenAPI specification generated from NestJS decorators
- **Client Generation**: Orval auto-generates TypeScript clients with full type inference from OpenAPI spec
- **Schema Validation**: Zod integration for runtime type validation at API boundaries
- **End-to-End Type Safety**: From database schema → API response → frontend components

### 🎯 Domain-Driven Design (DDD)
- **Bounded Contexts**: Each business module (Finance, HR, Inventory, etc.) operates as an independent bounded context
- **Separation of Concerns**: Clear boundaries enforced via ESLint plugin-boundaries
- **Event-Driven Communication**: Modules communicate via domain events using NestJS EventEmitter2

### ⚡ Performance Optimization
- **Distributed Caching**: Redis-based caching layer with configurable TTLs
- **Query Optimization**: Prisma query batching and connection pooling
- **Background Jobs**: BullMQ for asynchronous task processing (email notifications, report generation)
- **Database Indexing**: Strategic indexes on frequently queried columns

### 📦 Monorepo Architecture
- **Turborepo**: Optimized build system with intelligent caching and parallel execution
- **pnpm Workspaces**: Efficient dependency management with shared packages
- **Modular Packages**: Reusable packages for authentication, database, observability, pagination, etc.

### 🔍 Observability & Monitoring
- **Metrics**: Prometheus integration for API metrics, request latency, and resource utilization
- **Logging**: Centralized logging with Loki (structured JSON logs)
- **Dashboards**: Grafana dashboards for real-time monitoring
- **Health Checks**: `/health` endpoint with database connectivity and service status

### 🌐 Regulatory Compliance
- **E-VAT Integration**: Compliance module for Ghana Revenue Authority's electronic VAT system
- **Audit Logs**: Immutable audit trail for all financial transactions
- **Data Retention**: Configurable retention policies for compliance requirements

---

## Database Schema & Data Management Strategy

### Persistence Layer Architecture

The system employs a **single-database, multi-schema approach** using PostgreSQL with Prisma ORM. This design balances operational simplicity with robust tenant isolation.

#### Key Schema Design Principles:

1. **Tenant Isolation Model**:
   - Each tenant operates within a logically isolated namespace
   - Tenant ID embedded in all domain entity tables
   - Database-level foreign key constraints enforce referential integrity within tenant boundaries

2. **Entity Relationship Patterns**:
   - **Users & Authentication**: User entities linked to tenant context via composite keys
   - **Financial Records**: Double-entry bookkeeping tables with journal entries, ledgers, and chart of accounts
   - **Inventory Management**: Product catalog, stock movements, and warehouse locations with real-time inventory tracking
   - **HR Module**: Employee records, payroll, attendance, and leave management

3. **Audit & Compliance Tables**:
   - `audit_logs`: Immutable record of all write operations (INSERT/UPDATE/DELETE)
   - `vat_submissions`: Historical record of e-VAT submissions to GRA
   - Temporal data patterns for tracking entity state over time

4. **Scalability Considerations**:
   - Partitioning strategy prepared for tables exceeding 100M rows
   - Read replicas for reporting queries to reduce load on primary database
   - Archival strategy for historical data beyond retention period

#### Database Schema Diagram Placeholder:
> 📸 **Recording Needed:** Generate an ER diagram using dbdiagram.io or similar tool showing:
> - Core tables (users, tenants, products, invoices, employees, etc.)
> - Relationships and foreign key constraints
> - Multi-tenant column annotations
> - Indexes and unique constraints

---

## Functional Walkthrough

> 🎥 **Video/GIF Placeholders** - Record the following sequences to demonstrate production-level functionality:

### 1. Multi-Tenant Onboarding Flow
**What to record:**
- Admin creates a new tenant organization
- System auto-generates tenant-specific schema/namespace
- First user registration and role assignment
- Dashboard shows tenant-isolated data

**Technical highlight:** Demonstrate that users from Tenant A cannot access Tenant B's data

---

### 2. Type-Safe API Development Workflow
**What to record:**
- Make a change to a NestJS DTO
- Run `pnpm openapi` to regenerate OpenAPI spec
- Run `pnpm generate` (Orval) to update frontend types
- Show TypeScript IntelliSense catching type errors in frontend code

**Technical highlight:** End-to-end type safety from database → API → frontend

---

### 3. Financial Module Demo
**What to record:**
- Create a new invoice with line items
- Apply tax calculations (VAT)
- Generate journal entries (double-entry bookkeeping)
- Export to e-VAT compliant format
- View audit log of transaction

**Technical highlight:** Regulatory compliance and audit trail

---

### 4. Inventory Management
**What to record:**
- Add new product to catalog
- Record stock receipt from supplier
- Process a sale that decrements inventory
- View low-stock alerts
- Generate inventory valuation report

**Technical highlight:** Real-time inventory tracking across warehouses

---

### 5. RBAC & Permissions System
**What to record:**
- Login as Admin user → full access to all modules
- Login as Accountant → access only to Finance module
- Login as Warehouse Manager → access only to Inventory
- Attempt unauthorized action → show 403 Forbidden response

**Technical highlight:** Fine-grained role-based access control

---

### 6. Observability Dashboard
**What to record:**
- Open Grafana dashboard showing API metrics
- Trigger high load (simulate 100 concurrent requests)
- Show request latency graphs, error rates
- Demonstrate Loki logs filtered by tenant ID
- Show health check endpoint status

**Technical highlight:** Production-ready monitoring and alerting

---

## Tech Stack

| **Category**              | **Technology**                     | **Purpose**                                                  |
|---------------------------|------------------------------------|--------------------------------------------------------------|
| **Backend Framework**     | NestJS 11                          | Enterprise Node.js framework with TypeScript-first design    |
| **API Documentation**     | Swagger/OpenAPI 3.0                | Contract-first API development                               |
| **Type Generation**       | Orval                              | Auto-generate TypeScript clients from OpenAPI               |
| **Runtime Validation**    | Zod + class-validator              | Schema validation at API boundaries                          |
| **ORM**                   | Prisma 7                           | Type-safe database client with migration management         |
| **Database**              | PostgreSQL 16                      | ACID-compliant relational database                           |
| **Caching**               | Redis 7                            | Distributed caching and session storage                      |
| **Message Queue**         | BullMQ                             | Background job processing                                    |
| **Authentication**        | Passport.js + JWT                  | Stateless authentication with token-based auth               |
| **Authorization**         | Custom RBAC                        | Role-based access control with decorators                    |
| **Frontend Framework**    | React 19 + Vite                    | Modern UI with Fast Refresh and optimized bundling          |
| **State Management**      | TanStack Query (React Query)       | Server state management with caching and invalidation        |
| **UI Components**         | Custom component library           | Reusable design system components                            |
| **Monorepo Tool**         | Turborepo                          | High-performance build system                                |
| **Package Manager**       | pnpm                               | Fast, disk space efficient package manager                   |
| **Code Quality**          | ESLint + Prettier + Husky          | Linting, formatting, and pre-commit hooks                    |
| **Observability**         | Prometheus + Grafana + Loki        | Metrics collection, visualization, and log aggregation       |
| **Health Checks**         | NestJS Terminus                    | Readiness and liveness probes                                |
| **Email Service**         | Nodemailer                         | Transactional email delivery                                 |
| **Testing**               | Jest + Supertest                   | Unit and integration testing                                 |
| **CI/CD**                 | GitHub Actions                     | Automated testing and deployment pipelines                   |

---

## Development Workflow

### Prerequisites
- Node.js 20+
- pnpm 10+
- PostgreSQL 16+
- Redis 7+

### Quick Start

```bash
# Clone the repository
git clone <repository-url>
cd ghana-erp

# Install dependencies
pnpm install

# Set up environment variables
cp apps/api/.env.example apps/api/.env
# Configure DATABASE_URL, REDIS_URL, JWT_SECRET, etc.

# Run database migrations
cd packages/database
pnpm prisma:migrate:dev

# Generate Prisma client
pnpm prisma:generate

# Start development servers (API + Web)
pnpm dev
```

### Key Commands

```bash
# Generate OpenAPI specification
cd apps/api
pnpm openapi

# Generate TypeScript client from OpenAPI
cd packages/api-client
pnpm generate

# Run all linters and type checks
pnpm validate

# Build for production
pnpm build

# Run tests
pnpm test
```

---

## Project Structure

```
ghana-erp/
├── apps/
│   ├── api/                    # NestJS API application
│   │   ├── src/
│   │   │   ├── main.ts         # Application entry point
│   │   │   └── modules/        # Feature modules aggregation
│   │   └── openapi/            # Generated OpenAPI spec
│   └── web/                    # React frontend application
│
├── modules/                    # Domain-driven business modules
│   ├── auth/                   # Authentication & authorization
│   ├── finance/                # Financial management
│   ├── hr/                     # Human resources
│   ├── inventory/              # Inventory management
│   ├── sales/                  # Sales & CRM
│   ├── procurement/            # Procurement & purchasing
│   ├── compliance/             # Regulatory compliance (e-VAT)
│   ├── notifications/          # Email/SMS notifications
│   └── common/                 # Shared domain logic
│
├── packages/                   # Reusable shared packages
│   ├── database/               # Prisma schema and client
│   ├── api-client/             # Generated TypeScript client (Orval)
│   ├── authentication/         # Auth utilities and guards
│   ├── access-control/         # RBAC implementation
│   ├── cache/                  # Redis caching layer
│   ├── queue/                  # BullMQ job queue
│   ├── observability/          # Prometheus + Loki integration
│   ├── types/                  # Shared TypeScript types
│   ├── utils/                  # Common utilities
│   └── validators/             # Zod schemas
│
├── grafana/                    # Grafana dashboards and provisioning
├── scripts/                    # Build and deployment scripts
├── turbo.json                  # Turborepo configuration
└── pnpm-workspace.yaml         # pnpm workspace configuration
```

---

## Deployment Architecture

### Containerization
- **Docker**: Multi-stage builds for optimized image size
- **Docker Compose**: Local development environment orchestration

### Cloud-Native Deployment (Planned)
- **Kubernetes**: Container orchestration for horizontal pod autoscaling
- **Helm Charts**: Templated Kubernetes manifests
- **CI/CD**: GitHub Actions with automated testing and deployment
- **Database Migrations**: Automated via CI/CD pipeline with rollback support

### Environment Configuration
- **Development**: Local PostgreSQL + Redis
- **Staging**: Managed PostgreSQL (e.g., AWS RDS) + ElastiCache
- **Production**: High-availability PostgreSQL cluster + Redis Sentinel

---

## Security Considerations

- **API Security**: All endpoints protected by JWT authentication
- **Rate Limiting**: Configurable per-route throttling
- **Input Validation**: All inputs validated using class-validator and Zod
- **SQL Injection Protection**: Prisma's parameterized queries
- **XSS Protection**: React's built-in XSS escaping
- **CSRF Protection**: Token-based CSRF prevention
- **Secrets Management**: Environment variables, never committed to version control

---

## Performance Benchmarks

> 📊 **Metrics to Capture:**
> - API response times (p50, p95, p99)
> - Throughput (requests per second)
> - Database query performance
> - Cache hit rates

---

## Roadmap

- [ ] **Phase 1**: Core ERP modules (Finance, Inventory, HR) - ✅ Complete
- [ ] **Phase 2**: E-VAT integration and compliance reporting - ✅ Complete
- [ ] **Phase 3**: Advanced analytics and reporting dashboards - 🔄 In Progress
- [ ] **Phase 4**: Mobile application (React Native)
- [ ] **Phase 5**: AI-powered insights and forecasting

---

## License

This project is proprietary and confidential. Unauthorized copying, distribution, or use is strictly prohibited.

---

## Contact

For inquiries about this project, please reach out via [your contact information].

---

**Built with ❤️ using modern TypeScript and enterprise-grade architecture patterns**
