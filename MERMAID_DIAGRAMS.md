# Mermaid Diagrams for Portfolio Projects

This file contains all Mermaid diagram codes for generating architecture diagrams for Ghana ERP and Pee N Kay projects.

---

## 🏢 Ghana ERP Diagrams

### 1. System Architecture Diagram

```mermaid
architecture-beta
    group client(cloud)[Client Layer]
    group gateway(cloud)[API Gateway]
    group business(cloud)[Business Logic]
    group infrastructure(cloud)[Infrastructure]
    group persistence(database)[Persistence]
    group observability(cloud)[Observability]

    service webapp(internet)[React App] in client
    service nestjs(server)[NestJS] in gateway
    service ratelimit(server)[Rate Limit] in gateway
    service validation(server)[Validation] in gateway
    service rbac(server)[RBAC] in gateway
    
    service auth(server)[Auth] in business
    service finance(server)[Finance] in business
    service hr(server)[HR] in business
    service inventory(server)[Inventory] in business
    service sales(server)[Sales] in business
    service procurement(server)[Procurement] in business
    service compliance(server)[Compliance] in business
    service notifications(server)[Notifications] in business
    service eventbus(server)[EventBus] in business
    
    service redis(database)[Redis] in infrastructure
    service bullmq(server)[BullMQ] in infrastructure
    service email(server)[Email] in infrastructure
    
    service prisma(disk)[Prisma] in persistence
    service postgres(database)[PostgreSQL] in persistence
    
    service prometheus(disk)[Prometheus] in observability
    service grafana(internet)[Grafana] in observability
    service loki(disk)[Loki] in observability

    webapp:R -- L:nestjs
    nestjs:R -- L:ratelimit
    nestjs:R -- L:validation
    nestjs:R -- L:rbac
    
    nestjs:B -- T:auth
    nestjs:B -- T:finance
    nestjs:B -- T:hr
    nestjs:B -- T:inventory
    nestjs:B -- T:sales
    nestjs:B -- T:procurement
    nestjs:B -- T:compliance
    nestjs:B -- T:notifications
    
    auth:R -- L:eventbus
    finance:R -- L:eventbus
    sales:R -- L:eventbus
    inventory:R -- L:eventbus
    
    auth:B -- T:redis
    finance:B -- T:redis
    auth:B -- T:bullmq
    notifications:B -- T:bullmq
    notifications:B -- T:email
    
    auth:B -- T:prisma
    finance:B -- T:prisma
    hr:B -- T:prisma
    inventory:B -- T:prisma
    sales:B -- T:prisma
    
    prisma:B -- T:postgres
    
    nestjs:R -- L:prometheus
    auth:R -- L:prometheus
    grafana:B -- T:prometheus
    grafana:B -- T:loki
```

### 2. Multi-Tenant Data Flow

```mermaid
sequenceDiagram
    actor User
    participant Client as React Client
    participant Gateway as API Gateway
    participant Auth as Auth Module
    participant Finance as Finance Module
    participant DB as PostgreSQL
    
    User->>Client: Login Request
    Client->>Gateway: POST /auth/login
    Gateway->>Auth: Authenticate User
    Auth->>DB: Query users table
    DB-->>Auth: User + Tenant Info
    Auth-->>Gateway: JWT Token (with tenant_id)
    Gateway-->>Client: Access Token
    Client-->>User: Login Success
    
    Note over User,DB: Subsequent Request with Tenant Context
    
    User->>Client: Get Invoices
    Client->>Gateway: GET /invoices<br/>Authorization: Bearer {token}
    Gateway->>Gateway: Extract tenant_id from JWT
    Gateway->>Finance: Get Invoices (tenant_id: 123)
    Finance->>DB: SELECT * FROM invoices<br/>WHERE tenant_id = 123
    Note over DB: Row-Level Security<br/>enforces tenant isolation
    DB-->>Finance: Tenant-specific invoices
    Finance-->>Gateway: Invoice List
    Gateway-->>Client: Response with data
    Client-->>User: Display Invoices
```

### 3. Event-Driven Communication

```mermaid
graph LR
    subgraph Sales Module
        SalesAPI["Sales API Controller"]
        SalesService["Sales Service"]
        SalesEmit["Event Emitter"]
    end
    
    subgraph Event Bus
        EventBus["EventEmitter2<br/>Central Event Bus"]
    end
    
    subgraph Finance Module
        FinanceListener["Invoice Created Listener"]
        JournalService["Journal Entry Service"]
    end
    
    subgraph Inventory Module
        InvListener["Stock Update Listener"]
        StockService["Stock Management Service"]
    end
    
    subgraph Notification Module
        NotifListener["Notification Listener"]
        EmailService["Email Service"]
    end
    
    SalesAPI -->|Create Invoice| SalesService
    SalesService -->|Save to DB| SalesService
    SalesService -->|Emit Event| SalesEmit
    SalesEmit -->|"invoice.created"| EventBus
    
    EventBus -->|Trigger| FinanceListener
    EventBus -->|Trigger| InvListener
    EventBus -->|Trigger| NotifListener
    
    FinanceListener --> JournalService
    JournalService -->|"Create Debit/Credit Entries"| JournalService
    
    InvListener --> StockService
    StockService -->|"Decrease Stock Quantity"| StockService
    
    NotifListener --> EmailService
    EmailService -->|"Send Invoice Email to Customer"| EmailService
    
    style EventBus fill:#ffd700,stroke:#333,stroke-width:3px,color:#000
    style SalesEmit fill:#90ee90,stroke:#333,stroke-width:2px,color:#000
    style FinanceListener fill:#87ceeb,stroke:#333,stroke-width:2px,color:#000
    style InvListener fill:#87ceeb,stroke:#333,stroke-width:2px,color:#000
    style NotifListener fill:#87ceeb,stroke:#333,stroke-width:2px,color:#000
```

### 4. Database Schema (Entity Relationship)

```mermaid
erDiagram
    TENANTS ||--o{ USERS : has
    TENANTS ||--o{ ORGANIZATIONS : owns
    TENANTS ||--o{ PRODUCTS : contains
    TENANTS ||--o{ INVOICES : generates
    TENANTS ||--o{ PURCHASE_ORDERS : creates
    
    USERS {
        uuid id PK
        uuid tenant_id FK
        string email
        string password_hash
        string role
        timestamp created_at
    }
    
    TENANTS {
        uuid id PK
        string name
        string domain
        boolean is_active
        timestamp created_at
    }
    
    ORGANIZATIONS {
        uuid id PK
        uuid tenant_id FK
        string name
        string tax_id
        json address
    }
    
    PRODUCTS {
        uuid id PK
        uuid tenant_id FK
        string name
        string sku
        decimal cost_price
        decimal selling_price
        integer stock_quantity
        uuid category_id FK
    }
    
    INVOICES {
        uuid id PK
        uuid tenant_id FK
        uuid customer_id FK
        string invoice_number
        decimal subtotal
        decimal vat_amount
        decimal total
        string status
        timestamp created_at
    }
    
    INVOICE_ITEMS {
        uuid id PK
        uuid invoice_id FK
        uuid product_id FK
        integer quantity
        decimal unit_price
        decimal line_total
    }
    
    PURCHASE_ORDERS {
        uuid id PK
        uuid tenant_id FK
        uuid supplier_id FK
        string po_number
        decimal total_amount
        string status
        timestamp created_at
    }
    
    LEDGER_ENTRIES {
        uuid id PK
        uuid tenant_id FK
        uuid account_id FK
        string entry_type
        decimal amount
        string description
        timestamp transaction_date
    }
    
    AUDIT_LOGS {
        uuid id PK
        uuid tenant_id FK
        uuid user_id FK
        string action
        string entity_type
        json changes
        timestamp created_at
    }
    
    INVOICES ||--o{ INVOICE_ITEMS : contains
    PRODUCTS ||--o{ INVOICE_ITEMS : "listed in"
    USERS ||--o{ AUDIT_LOGS : performs
    USERS ||--o{ INVOICES : creates
    USERS ||--o{ PURCHASE_ORDERS : creates
```

### 5. Type-Safe API Development Flow

```mermaid
graph TB
    subgraph Backend["Backend (NestJS)"]
        DTOs["DTOs with Decorators<br/>CreateUserDto, UpdateInvoiceDto"]
        Controller["NestJS Controllers<br/>@ApiTags, @ApiProperty"]
        Swagger["Swagger Plugin"]
        OpenAPI["openapi.json<br/>Generated Spec"]
    end
    
    subgraph CodeGen["Code Generation"]
        Orval["Orval CLI<br/>pnpm generate"]
        TSTypes["TypeScript Types<br/>Interfaces & API Client"]
    end
    
    subgraph Frontend["Frontend (React)"]
        APIClient["Type-Safe API Client<br/>Auto-generated"]
        Components["React Components<br/>Full IntelliSense"]
        Form["Form Validation<br/>Compile-time errors"]
    end
    
    DTOs --> Controller
    Controller --> Swagger
    Swagger --> OpenAPI
    OpenAPI --> Orval
    Orval --> TSTypes
    TSTypes --> APIClient
    APIClient --> Components
    Components --> Form
    
    Form -.->|Type mismatch error| DTOs
    
    style OpenAPI fill:#85ea2d,stroke:#333,stroke-width:3px,color:#000
    style TSTypes fill:#3178c6,stroke:#333,stroke-width:2px,color:#fff
    style Form fill:#61dafb,stroke:#333,stroke-width:2px,color:#000
```

---

## 🛒 Pee N Kay E-Commerce Diagrams

### 1. System Architecture Diagram

```mermaid
architecture-beta
    group client(cloud)[Client Layer]
    group edge(cloud)[Edge Network]
    group appserver(cloud)[App Server]
    group supabase(cloud)[Supabase]
    group persistence(database)[Persistence]
    group observability(cloud)[Observability]

    service nextapp(internet)[NextJS] in client
    service cdn(internet)[CDN] in edge
    
    service ssr(server)[SSR] in appserver
    service api(server)[API] in appserver
    service middleware(server)[Auth] in appserver
    
    service supadb(database)[Database] in supabase
    service supaauth(server)[Auth] in supabase
    service supastorage(disk)[Storage] in supabase
    service suparealtime(server)[Realtime] in supabase
    service supaedge(server)[Edge] in supabase
    service supavector(database)[Vector] in supabase
    
    service primary(database)[Primary] in persistence
    service replicas(database)[Replicas] in persistence
    service backups(disk)[Backups] in persistence
    
    service prometheus(disk)[Prometheus] in observability
    service grafana(internet)[Grafana] in observability
    service loki(disk)[Loki] in observability

    nextapp:R -- L:cdn
    cdn:R -- L:ssr
    ssr:R -- L:middleware
    middleware:R -- L:api
    
    api:B -- T:supadb
    api:B -- T:supaauth
    api:B -- T:supastorage
    api:B -- T:suparealtime
    api:B -- T:supaedge
    api:B -- T:supavector
    
    supadb:B -- T:primary
    primary:R -- L:replicas
    primary:R -- L:backups
    
    ssr:R -- L:prometheus
    api:R -- L:prometheus
    supadb:R -- L:loki
    grafana:B -- T:prometheus
    grafana:B -- T:loki
```

### 2. Authentication Flow

```mermaid
sequenceDiagram
    actor User
    participant Client as Next.js Client
    participant Middleware as Auth Middleware
    participant Supabase as Supabase Auth
    participant DB as PostgreSQL
    
    User->>Client: Click "Sign Up"
    Client->>Supabase: POST /auth/signup<br/>{email, password}
    Supabase->>DB: INSERT INTO auth.users
    DB-->>Supabase: User Created
    Supabase->>User: Verification Email
    Supabase-->>Client: Temporary Session
    
    Note over User: User clicks verification link
    
    User->>Supabase: Confirm Email Token
    Supabase->>DB: UPDATE auth.users<br/>SET email_confirmed = true
    Supabase-->>Client: Access Token + Refresh Token
    Client->>Client: Store tokens in cookies
    
    Note over User,DB: Subsequent Authenticated Request
    
    User->>Client: Browse Products
    Client->>Middleware: GET /products<br/>Cookie: access_token
    Middleware->>Middleware: Validate JWT
    Middleware->>Supabase: Verify Token
    Supabase-->>Middleware: Token Valid
    Middleware->>DB: SELECT * FROM products<br/>(with RLS policies)
    DB-->>Middleware: Products (public access)
    Middleware-->>Client: Products List
    Client-->>User: Display Products
    
    Note over User,DB: Protected Resource (User's Cart)
    
    User->>Client: View Cart
    Client->>Middleware: GET /cart<br/>Cookie: access_token
    Middleware->>Supabase: Verify Token + Get user_id
    Middleware->>DB: SELECT * FROM carts<br/>WHERE user_id = {id}
    Note over DB: RLS Policy enforces<br/>user can only see own cart
    DB-->>Middleware: User's Cart Items
    Middleware-->>Client: Cart Data
    Client-->>User: Display Cart
```

### 3. Real-Time Inventory Synchronization

```mermaid
sequenceDiagram
    participant Admin as Admin Panel
    participant API as Next.js API
    participant DB as PostgreSQL
    participant Realtime as Supabase Realtime
    participant Customer as Customer Browser
    
    Note over Customer: Customer viewing product page
    
    Customer->>Realtime: Subscribe to inventory changes<br/>channel: product-123
    Realtime-->>Customer: WebSocket Connected
    
    Note over Admin: Admin updates stock
    
    Admin->>API: PUT /inventory/product-123<br/>{stock: 5}
    API->>DB: UPDATE inventory<br/>SET stock = 5<br/>WHERE product_id = 123
    DB-->>API: Update Successful
    API-->>Admin: Success Response
    
    DB->>Realtime: Trigger: postgres_changes<br/>Table: inventory<br/>Event: UPDATE
    Realtime->>Customer: Push Update via WebSocket<br/>{product_id: 123, stock: 5}
    Customer->>Customer: Update UI<br/>"Only 5 left!"
    
    Note over Customer: Customer adds 5 items to cart
    
    Customer->>API: POST /cart/add<br/>{product_id: 123, qty: 5}
    API->>DB: UPDATE inventory<br/>SET stock = 0<br/>WHERE product_id = 123
    DB->>Realtime: Trigger: UPDATE event
    Realtime->>Customer: Push Update<br/>{product_id: 123, stock: 0}
    Customer->>Customer: Update UI<br/>"Out of Stock"<br/>Disable "Add to Cart" button
    
    Note over Admin: Admin receives notification
    
    Realtime->>Admin: Push Update<br/>{product_id: 123, stock: 0}
    Admin->>Admin: Show low stock alert
```

### 4. Shopping Cart & Checkout Flow

```mermaid
graph TB
    subgraph Browse["Product Browsing"]
        ProductList["Product Grid<br/>Server Component"]
        ProductDetail["Product Detail<br/>Server Component + Client Actions"]
        AddToCart["Add to Cart Button<br/>Client Component"]
    end
    
    subgraph Cart["Cart Management"]
        CartState["Zustand Store<br/>Client-Side State"]
        CartAPI["Cart API Route<br/>/api/cart"]
        CartDB["Supabase cart table<br/>Persistent Storage"]
    end
    
    subgraph Checkout["Checkout Process"]
        CheckoutPage["Checkout Page<br/>Server Component"]
        ShippingForm["Shipping Form<br/>Client Component"]
        PaymentAPI["Payment API<br/>Server Action"]
        OrderCreate["Create Order<br/>Database Transaction"]
    end
    
    subgraph PostOrder["Post-Order"]
        Confirmation["Order Confirmation<br/>Server Component"]
        EmailQueue["Email Queue<br/>BullMQ/Supabase Edge"]
        InventoryUpdate["Update Inventory<br/>Decrease Stock"]
    end
    
    ProductList -->|Click Product| ProductDetail
    ProductDetail -->|Click Add| AddToCart
    AddToCart -->|Update| CartState
    CartState -->|Sync| CartAPI
    CartAPI -->|Persist| CartDB
    
    CartState -->|Navigate| CheckoutPage
    CheckoutPage -->|Fill Form| ShippingForm
    ShippingForm -->|Submit| PaymentAPI
    PaymentAPI -->|Process| OrderCreate
    
    OrderCreate -->|Success| Confirmation
    OrderCreate -->|Trigger| EmailQueue
    OrderCreate -->|Trigger| InventoryUpdate
    
    style AddToCart fill:#61dafb,stroke:#333,stroke-width:2px,color:#000
    style CartState fill:#ffd700,stroke:#333,stroke-width:2px,color:#000
    style OrderCreate fill:#90ee90,stroke:#333,stroke-width:2px,color:#000
```

### 5. Server Components vs Client Components Architecture

```mermaid
graph TB
    subgraph ServerSide["Server Side - Node.js Runtime"]
        ServerLayout["Layout<br/>Server Component<br/>Data Fetching"]
        ProductPage["Product Page<br/>Server Component<br/>Fetch from Supabase"]
        SearchResults["Search Results<br/>Server Component<br/>SSR with data"]
    end
    
    subgraph ClientSide["Client Side - Browser Runtime"]
        CartDrawer["Cart Drawer<br/>Client Component<br/>'use client'"]
        SearchBar["Search Bar<br/>Client Component<br/>Interactive"]
        AddToCartBtn["Add to Cart Button<br/>Client Component<br/>Event Handlers"]
        FilterPanel["Filter Panel<br/>Client Component<br/>State Management"]
    end
    
    subgraph DataLayer["Data Layer"]
        SupabaseServer["Supabase Server Client<br/>Server-side only<br/>Admin privileges"]
        SupabaseClient["Supabase Client<br/>Browser-side<br/>RLS enforced"]
    end
    
    ServerLayout -->|Render| ProductPage
    ServerLayout -->|Render| SearchResults
    
    ProductPage -->|"Embed (Hydration Boundary)"| CartDrawer
    ProductPage -->|"Embed (Hydration Boundary)"| AddToCartBtn
    SearchResults -->|"Embed (Hydration Boundary)"| SearchBar
    SearchResults -->|"Embed (Hydration Boundary)"| FilterPanel
    
    ServerLayout --> SupabaseServer
    ProductPage --> SupabaseServer
    
    CartDrawer --> SupabaseClient
    SearchBar --> SupabaseClient
    FilterPanel --> SupabaseClient
    
    style ServerLayout fill:#87ceeb,stroke:#333,stroke-width:2px,color:#000
    style ProductPage fill:#87ceeb,stroke:#333,stroke-width:2px,color:#000
    style CartDrawer fill:#ffa07a,stroke:#333,stroke-width:2px,color:#000
    style AddToCartBtn fill:#ffa07a,stroke:#333,stroke-width:2px,color:#000
    style SupabaseServer fill:#3ecf8e,stroke:#333,stroke-width:2px,color:#000
```

### 6. Database Schema (Entity Relationship)

```mermaid
erDiagram
    AUTH_USERS ||--o| PROFILES : has
    AUTH_USERS ||--o{ ORDERS : places
    AUTH_USERS ||--o{ CARTS : owns
    AUTH_USERS ||--o{ REVIEWS : writes
    AUTH_USERS ||--o{ WISHLISTS : creates
    
    AUTH_USERS {
        uuid id PK
        string email
        string encrypted_password
        timestamp email_confirmed_at
        json raw_user_meta_data
    }
    
    PROFILES {
        uuid id PK
        uuid user_id FK
        string full_name
        string avatar_url
        json shipping_address
        timestamp created_at
    }
    
    PRODUCTS ||--o{ PRODUCT_VARIANTS : has
    PRODUCTS ||--o{ CART_ITEMS : "added to"
    PRODUCTS ||--o{ ORDER_ITEMS : "ordered in"
    PRODUCTS ||--o{ REVIEWS : receives
    PRODUCTS }|--|| CATEGORIES : "belongs to"
    PRODUCTS }o--o{ COLLECTIONS : "featured in"
    
    PRODUCTS {
        uuid id PK
        string name
        text description
        decimal base_price
        string slug
        uuid category_id FK
        json images
        boolean is_active
        timestamp created_at
    }
    
    PRODUCT_VARIANTS {
        uuid id PK
        uuid product_id FK
        string sku
        string size
        string color
        decimal price
        integer stock_quantity
    }
    
    CATEGORIES {
        uuid id PK
        string name
        string slug
        uuid parent_id FK
        integer sort_order
    }
    
    CARTS {
        uuid id PK
        uuid user_id FK
        timestamp created_at
        timestamp updated_at
    }
    
    CARTS ||--o{ CART_ITEMS : contains
    
    CART_ITEMS {
        uuid id PK
        uuid cart_id FK
        uuid product_id FK
        uuid variant_id FK
        integer quantity
        decimal unit_price
    }
    
    ORDERS {
        uuid id PK
        uuid user_id FK
        string order_number
        decimal subtotal
        decimal shipping_cost
        decimal tax
        decimal total
        string status
        json shipping_address
        timestamp created_at
    }
    
    ORDERS ||--o{ ORDER_ITEMS : contains
    
    ORDER_ITEMS {
        uuid id PK
        uuid order_id FK
        uuid product_id FK
        uuid variant_id FK
        integer quantity
        decimal unit_price
        decimal line_total
    }
    
    REVIEWS {
        uuid id PK
        uuid product_id FK
        uuid user_id FK
        integer rating
        text comment
        boolean is_verified_purchase
        timestamp created_at
    }
    
    WISHLISTS {
        uuid id PK
        uuid user_id FK
        uuid product_id FK
        timestamp created_at
    }
    
    INVENTORY {
        uuid id PK
        uuid product_id FK
        uuid variant_id FK
        integer stock_quantity
        integer reserved_quantity
        timestamp last_updated
    }
```

---

## 📝 How to Use These Diagrams

### Online Mermaid Editors:
1. **Mermaid Live Editor**: https://mermaid.live/
   - Copy any diagram code above
   - Paste into the editor
   - Export as PNG/SVG

2. **GitHub Markdown**: 
   - GitHub natively supports Mermaid in markdown
   - Simply paste the code block with ` ```mermaid ` in your README

3. **VS Code Extension**:
   - Install "Markdown Preview Mermaid Support" extension
   - View diagrams directly in VS Code preview

### Export Tips:
- **PNG**: Best for embedding in READMEs (transparent background)
- **SVG**: Best for high-resolution, scalable diagrams
- **PDF**: Best for documentation

### Customization:
- Colors can be adjusted using `style` declarations at the end of each diagram
- Node shapes can be changed: `[]` (rectangle), `()` (rounded), `{}` (rhombus), `((()))` (circle)
- Arrow types: `-->` (solid), `-.->` (dotted), `==>` (thick)

---

## 🎨 Color Scheme Guide

### Ghana ERP:
- **Frontend**: `#61dafb` (React Blue)
- **Backend**: `#e0234e` (NestJS Red)
- **Database**: `#336791` (PostgreSQL Blue)
- **Cache**: `#dc382d` (Redis Red)
- **Monitoring**: `#f46800` (Grafana Orange)

### Pee N Kay:
- **Next.js**: `#000000` (Black)
- **Supabase**: `#3ecf8e` (Supabase Green)
- **Edge/CDN**: `#ff6b6b` (Red)
- **Server Components**: `#87ceeb` (Light Blue)
- **Client Components**: `#ffa07a` (Light Salmon)

---

## ✅ Checklist

- [ ] Copy diagram codes to Mermaid Live Editor
- [ ] Customize colors if needed
- [ ] Export as PNG with transparent background
- [ ] Save to appropriate directory:
  - Ghana ERP: `/projects-showcase/ghana-erp/diagrams/`
  - Pee N Kay: `/projects-showcase/pee-n-kay/diagrams/`
- [ ] Embed images in respective README files
- [ ] Test diagram rendering in GitHub preview

---

**Note**: These diagrams are designed to be comprehensive yet clear. Feel free to simplify them based on your specific needs or the level of detail you want to showcase in your portfolio.
