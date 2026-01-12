# Portfolio Media Documentation Guide

> A comprehensive checklist for creating screenshots, videos, and diagrams to enhance your portfolio READMEs

---

## 📋 Overview

This guide outlines **exactly what media assets you need to create** for both the Ghana ERP and Pee N Kay projects to transform your READMEs from text-heavy documents into compelling portfolio showcases.

**Priority Level Legend:**
- 🔴 **Critical**: Must-have for a complete portfolio presentation
- 🟡 **Important**: Strongly recommended for technical credibility
- 🟢 **Optional**: Nice-to-have for extra polish

---

## 🏢 Ghana ERP - Media Checklist

### 1. Architecture Diagrams

#### 🔴 System Architecture Diagram
**Tool**: Excalidraw, Mermaid, or Draw.io  
**What to include**:
- Frontend (React 19 + Vite) → API Gateway (NestJS)
- API Gateway → Business Logic Modules (Auth, Finance, HR, Inventory, Sales, Procurement, Compliance, Notifications, Common)
- Business Logic → Infrastructure Layer (Redis, BullMQ, Email Service)
- Infrastructure → Database (PostgreSQL with Multi-tenant Schema)
- Observability Stack (Prometheus + Grafana + Loki) connected to all layers
- Event Bus (EventEmitter2) connecting modules

**Where to save**: `/projects-showcase/ghana-erp/diagrams/system-architecture.png`

**Pro tip**: Use different colors for each layer (e.g., blue for frontend, green for backend, orange for infrastructure)

---

#### 🟡 Database Schema (ER Diagram)
**Tool**: dbdiagram.io, Prisma Studio, or pgAdmin  
**What to include**:
- Core tables: `users`, `tenants`, `organizations`, `employees`, `products`, `invoices`, `purchase_orders`, `ledger_entries`, `vat_submissions`
- Foreign key relationships with cardinality annotations (1:1, 1:N, N:M)
- Indexes on frequently queried columns
- Multi-tenant column annotations (highlight `tenant_id` in red)
- At least 15-20 tables to show system complexity

**Where to save**: `/projects-showcase/ghana-erp/diagrams/database-schema.png`

**Pro tip**: Group tables by domain (Finance tables in one cluster, HR tables in another, etc.)

---

### 2. Functional Walkthrough Videos/GIFs

#### 🔴 Multi-Tenant Onboarding Flow (Video: 60-90 seconds)
**What to record**:
1. Login as Super Admin
2. Navigate to "Tenants" management page
3. Click "Create New Tenant" button
4. Fill in form: Organization Name, Domain, Contact Email
5. Submit → Show success toast notification
6. Show auto-generated tenant ID in the table
7. Logout and login as a user from the new tenant
8. Demonstrate that this user sees only their tenant's data (empty dashboard)
9. Switch back to Super Admin → Show tenant B data is completely separate

**Recording tool**: Loom, OBS Studio, or ScreenToGif  
**Where to save**: Upload to Loom/YouTube, embed link in README  
**Pro tip**: Add voiceover explaining "Notice how the tenant ID is automatically injected into every query"

---

#### 🔴 Type-Safe API Development Workflow (GIF: 30-45 seconds)
**What to record**:
1. Open VS Code with split screen (NestJS API on left, React frontend on right)
2. Edit a NestJS DTO (e.g., add a new `phone_number` field to `CreateUserDto`)
3. Run terminal command: `pnpm openapi` (show OpenAPI spec regenerating)
4. Run terminal command: `pnpm generate` (show Orval generating TypeScript types)
5. Switch to frontend code → Show TypeScript error highlighting missing `phone_number` field
6. Add the field to the form → Error disappears
7. Show IntelliSense autocomplete for the new field

**Recording tool**: ScreenToGif or LICEcap  
**Where to save**: `/projects-showcase/ghana-erp/media/type-safe-workflow.gif`  
**Pro tip**: Add text overlay: "End-to-End Type Safety: Database → API → Frontend"

---

#### 🟡 Financial Module Demo (Video: 90-120 seconds)
**What to record**:
1. Navigate to "Sales" module → Click "Create Invoice"
2. Select customer from dropdown
3. Add 3 line items (products with quantities and prices)
4. Show automatic VAT calculation (16% for Ghana)
5. Save invoice → Show invoice PDF preview
6. Navigate to "Finance" module → "Chart of Accounts"
7. Show auto-generated journal entries (Debit: Accounts Receivable, Credit: Sales Revenue)
8. Navigate to "Compliance" → "E-VAT Submissions"
9. Show formatted XML/JSON ready for Ghana Revenue Authority (GRA) submission
10. Navigate to "Audit Log" → Show all actions recorded with user attribution

**Recording tool**: Loom with voiceover  
**Where to save**: Upload to Loom, embed link in README  
**Pro tip**: Explain the double-entry bookkeeping logic in voiceover

---

#### 🟡 Inventory Management (Video: 60-90 seconds)
**What to record**:
1. Navigate to "Inventory" → "Products"
2. Add a new product: "Wireless Mouse" (SKU, name, description, cost, price, initial stock = 50)
3. Navigate to "Procurement" → "Purchase Orders"
4. Create a PO for 100 units of the mouse
5. Approve PO → Show stock level increase to 150
6. Navigate to "Sales" → Create an invoice for 10 units
7. Show stock level decrease to 140
8. Navigate to "Inventory Reports" → Show stock movement history
9. Set low-stock alert threshold to 20 units → Show alert appears when stock drops below 20

**Recording tool**: Loom or OBS Studio  
**Where to save**: Upload to Loom, embed link in README  

---

#### 🟡 RBAC & Permissions System (Video: 45-60 seconds)
**What to record**:
1. Login as **Super Admin** → Show full access to all modules (Admin, Finance, HR, Inventory, Sales)
2. Logout → Login as **Accountant** role → Show only Finance and Compliance modules visible
3. Attempt to access HR module → Show 403 Forbidden error page
4. Logout → Login as **Warehouse Manager** → Show only Inventory module visible
5. Attempt to access Finance module → Show 403 error
6. Show the permissions matrix table in Admin panel (which roles have access to which modules)

**Recording tool**: ScreenToGif or Loom  
**Where to save**: `/projects-showcase/ghana-erp/media/rbac-demo.gif` or Loom  

---

#### 🟢 Observability Dashboard (Screenshot: 1-2 images)
**What to capture**:
1. Open Grafana dashboard in browser
2. Show dashboard with 6-8 panels:
   - API Request Rate (requests/second)
   - Request Latency (p50, p95, p99 percentiles)
   - Error Rate (4xx, 5xx responses)
   - Database Query Performance (query duration)
   - Redis Cache Hit Rate
   - Active Tenants (gauge)
3. Screenshot with real data (or simulate load with Apache Bench/k6)

**Where to save**: `/projects-showcase/ghana-erp/media/grafana-dashboard.png`  
**Pro tip**: Use dark theme for aesthetic appeal

---

### 3. Code Screenshots

#### 🟢 Multi-Tenant Decorator Example
**What to show**:
- A NestJS controller method with `@CurrentTenant()` decorator
- Show the decorator implementation extracting tenant ID from JWT

**Where to save**: `/projects-showcase/ghana-erp/media/multi-tenant-decorator.png`  

---

## 🛒 Pee N Kay E-Commerce - Media Checklist

### 1. Architecture Diagrams

#### 🔴 System Architecture Diagram
**Tool**: Excalidraw or Mermaid  
**What to include**:
- Client Layer (Next.js App Router + React Server Components)
- Edge Network (Vercel/Cloudflare CDN)
- Application Server Layer (Next.js SSR + API Routes + Middleware)
- Supabase Platform (PostgreSQL, Auth, Storage, Realtime, Edge Functions)
- Data Persistence Layer (PostgreSQL Primary + Read Replicas)
- Observability Stack (Grafana + Prometheus + Loki for backend API)

**Where to save**: `/projects-showcase/pee-n-kay/diagrams/system-architecture.png`

---

#### 🟡 Supabase Database Schema (ER Diagram)
**Tool**: dbdiagram.io or Supabase Studio  
**What to include**:
- `auth.users` (Supabase managed) → `profiles` (extended user data)
- `products` → `product_variants` → `categories` → `collections`
- `carts` → `cart_items` → `products`
- `orders` → `order_items` → `products`
- `inventory` → `stock_movements`
- `reviews` → `products` and `users`
- `wishlists` → `products` and `users`
- Show Row-Level Security (RLS) policy annotations

**Where to save**: `/projects-showcase/pee-n-kay/diagrams/database-schema.png`

---

### 2. Functional Walkthrough Videos/GIFs

#### 🔴 User Registration & Authentication Flow (Video: 60-75 seconds)
**What to record**:
1. Navigate to homepage → Click "Sign Up"
2. Fill in registration form (email, password, confirm password)
3. Submit → Show email verification message
4. Open email inbox → Click verification link
5. Redirect back to site → Auto-login
6. Show user dropdown (avatar, profile, logout)
7. Logout → Click "Sign In with Google"
8. Google OAuth consent screen → Approve
9. Redirect back → Show authenticated state

**Recording tool**: Loom with voiceover  
**Where to save**: Upload to Loom, embed in README  
**Pro tip**: Explain "This is Supabase Auth handling JWT tokens and OAuth"

---

#### 🔴 Product Discovery & Search (GIF: 30-45 seconds)
**What to record**:
1. Homepage → Scroll through product grid (show infinite scroll loading)
2. Click on search bar → Type "headphones"
3. Show autocomplete suggestions appearing
4. Submit search → Show filtered results
5. Apply filters: Price range ($50-$200), Category (Electronics), Rating (4+ stars)
6. Sort by "Price: Low to High"
7. Click on a product → Navigate to product detail page
8. Show image carousel (swipe through 3-4 images)

**Recording tool**: ScreenToGif  
**Where to save**: `/projects-showcase/pee-n-kay/media/product-discovery.gif`

---

#### 🔴 Shopping Cart & Checkout (Video: 90-120 seconds)
**What to record**:
1. Browse products → Add 3 different items to cart (show "Added to cart" toast)
2. Click cart icon in navbar → Cart drawer slides in from right
3. Show cart items with thumbnails, quantities, prices
4. Increase quantity of one item → Show total updating in real-time
5. Remove one item → Show smooth animation
6. Click "Checkout" button → Navigate to checkout page
7. **Step 1**: Enter shipping address (autocomplete with previous address if returning user)
8. **Step 2**: Review order summary (items, subtotal, shipping, tax, total)
9. Click "Place Order" (simulate payment success)
10. Redirect to order confirmation page → Show order ID and estimated delivery
11. Navigate to "Order History" → Show the new order in the table

**Recording tool**: Loom with voiceover  
**Where to save**: Upload to Loom, embed in README  
**Pro tip**: Highlight "Cart persists across devices via Supabase"

---

#### 🔴 Real-Time Inventory Updates (Video: 45-60 seconds) ⭐ **MOST IMPRESSIVE**
**What to record (requires 2 browser windows side-by-side)**:
1. **Window A** (Customer view): Open product page for "Gaming Laptop" → Show "12 in stock"
2. **Window B** (Admin panel): Open inventory management → Find "Gaming Laptop" → Change stock to "3 units"
3. **Window A**: Without refreshing, show stock indicator update to "Only 3 left!"
4. **Window A**: Add 3 units to cart → Stock shows "0 left"
5. **Window B**: Set stock to 0
6. **Window A**: Observe "Out of Stock" badge appears instantly + "Add to Cart" button becomes disabled

**Recording tool**: OBS Studio (to capture two windows simultaneously)  
**Where to save**: Upload to Loom, embed in README  
**Pro tip**: Add text overlay: "Real-Time Sync via Supabase WebSockets"

---

#### 🟡 Order History & Profile Management (GIF: 30-45 seconds)
**What to record**:
1. Click user dropdown → "My Profile"
2. Show profile page with tabs: Profile Info, Order History, Addresses
3. Click "Order History" tab → Show table with 3-5 past orders
4. Click on one order → Show order details modal (items, status, tracking)
5. Go to "Profile Info" tab → Edit name and email
6. Submit → Show success toast

**Recording tool**: ScreenToGif  
**Where to save**: `/projects-showcase/pee-n-kay/media/profile-management.gif`

---

#### 🟡 Server Components vs. Client Components (GIF: 20-30 seconds)
**What to record (developer-focused)**:
1. Open Chrome DevTools → Network tab
2. Navigate to homepage
3. Show HTML response (View Page Source) → Highlight that full HTML is rendered
4. Show React DevTools → Highlight which components are Server Components (icon)
5. Interact with cart drawer (Client Component) → Show client-side hydration boundary

**Recording tool**: ScreenToGif  
**Where to save**: `/projects-showcase/pee-n-kay/media/server-components.gif`  
**Pro tip**: Add annotations in the GIF

---

#### 🟡 Responsive Design & Mobile Experience (Video: 30-45 seconds)
**What to record**:
1. Desktop view: Show full navigation menu, 4-column product grid
2. Resize browser to tablet → Grid adjusts to 2 columns
3. Resize to mobile → Hamburger menu appears, 1-column grid
4. Open hamburger menu → Show slide-in mobile navigation
5. Click cart icon → Cart drawer optimized for touch
6. Open filter panel → Filters appear in bottom sheet (mobile pattern)
7. Demonstrate smooth scrolling and touch interactions

**Recording tool**: Loom or OBS Studio  
**Where to save**: Upload to Loom, embed in README

---

#### 🔴 Performance Metrics (Screenshot: 2-3 images)
**What to capture**:
1. Open Chrome DevTools → Lighthouse tab
2. Run audit on **Homepage**:
   - Performance: 95+
   - Accessibility: 100
   - Best Practices: 100
   - SEO: 100
3. Run audit on **Product Detail Page**
4. Show Core Web Vitals metrics:
   - LCP (Largest Contentful Paint): < 2.5s
   - FID (First Input Delay): < 100ms
   - CLS (Cumulative Layout Shift): < 0.1

**Where to save**: `/projects-showcase/pee-n-kay/media/lighthouse-homepage.png` and `lighthouse-product-page.png`

---

### 3. Code Screenshots

#### 🟡 Server Component Example
**What to show**:
```typescript
// app/products/[id]/page.tsx (Server Component)
export default async function ProductPage({ params }) {
  const supabase = createServerClient()
  const { data: product } = await supabase
    .from('products')
    .select('*, variants(*), reviews(*)')
    .eq('id', params.id)
    .single()

  return <ProductDetails product={product} />
}
```

**Where to save**: `/projects-showcase/pee-n-kay/media/server-component-code.png`

---

#### 🟡 Supabase Realtime Hook
**What to show**:
```typescript
// hooks/useInventorySubscription.ts
const channel = supabase
  .channel('inventory-changes')
  .on('postgres_changes', {
    event: 'UPDATE',
    schema: 'public',
    table: 'inventory',
    filter: `product_id=eq.${productId}`
  }, (payload) => {
    // Update state with new inventory
  })
  .subscribe()
```

**Where to save**: `/projects-showcase/pee-n-kay/media/realtime-subscription-code.png`

---

## 📊 Summary of Media Assets

### Ghana ERP
- **Diagrams**: 2 (System Architecture, Database Schema)
- **Videos**: 4-5 (Multi-tenant onboarding, Type-safe workflow, Financial module, Inventory, RBAC)
- **Screenshots**: 2-3 (Grafana dashboard, Code examples)
- **Total estimated time**: 4-6 hours

### Pee N Kay
- **Diagrams**: 2 (System Architecture, Database Schema)
- **Videos/GIFs**: 6-7 (Auth flow, Product discovery, Checkout, Real-time inventory, Profile, Responsive design, Performance)
- **Screenshots**: 3-4 (Lighthouse scores, Code examples)
- **Total estimated time**: 5-7 hours

---

## 🎬 Recording Best Practices

### Video Recording
1. **Screen Resolution**: Record at 1920x1080 (Full HD)
2. **Frame Rate**: 30 FPS minimum
3. **Length**: Keep videos under 2 minutes (ideal: 45-90 seconds)
4. **Audio**: Use a decent microphone for voiceover (Blue Yeti, Rode NT-USB, etc.)
5. **Editing**: Use Loom's built-in trimming or DaVinci Resolve (free)

### GIF Creation
1. **Tool**: ScreenToGif (Windows), Gifox (Mac), or Peek (Linux)
2. **Frame Rate**: 15-20 FPS (smaller file size)
3. **Duration**: 15-45 seconds max
4. **File Size**: Compress to under 10MB (use ezgif.com)
5. **Dimensions**: 1200px wide or smaller

### Diagram Creation
1. **Tool Recommendations**:
   - **Architecture diagrams**: Excalidraw (free, beautiful hand-drawn style)
   - **Database schemas**: dbdiagram.io (free, code-to-diagram)
   - **Complex flows**: Draw.io / diagrams.net (free, professional)
2. **Export Format**: PNG with transparent background (if possible)
3. **Resolution**: At least 1920px wide for clarity
4. **Color Scheme**: Use consistent colors (e.g., blue for data flow, green for success, red for errors)

---

## 📍 Where to Host Videos

### Option 1: Loom (Recommended)
- **Pros**: Free tier available, easy embedding, no download required
- **Cons**: Videos may expire after 25 videos on free plan
- **Best for**: Quick demos and walkthroughs

### Option 2: YouTube (Unlisted)
- **Pros**: Unlimited storage,永久 hosting, professional player
- **Cons**: Requires Google account, slight overkill for portfolio
- **Best for**: Long-form demos (> 2 minutes)

### Option 3: GitHub Repository
- **Pros**: Version controlled, no external dependencies
- **Cons**: Large files increase repo size, slower loading
- **Best for**: GIFs and small videos (< 50MB)

---

## ✅ Final Checklist

### Before You Start Recording:
- [ ] Clean up your development environment (close unnecessary tabs/windows)
- [ ] Use a clean browser profile (no personal bookmarks or extensions visible)
- [ ] Prepare demo data (sample products, users, orders)
- [ ] Test the flow multiple times before recording
- [ ] Disable desktop notifications during recording

### After Recording:
- [ ] Watch the entire video for errors or awkward pauses
- [ ] Add captions/subtitles if possible (accessibility!)
- [ ] Export at appropriate resolution (1080p for videos, compressed for GIFs)
- [ ] Upload to hosting platform and test embedding
- [ ] Update README with correct links/paths

---

## 🚀 Next Steps

1. **Week 1**: Create architecture diagrams for both projects (2-3 hours)
2. **Week 2**: Record Ghana ERP videos (4-6 hours)
3. **Week 3**: Record Pee N Kay videos (5-7 hours)
4. **Week 4**: Capture screenshots and code examples (2-3 hours)
5. **Week 5**: Embed all media in READMEs and polish (2-3 hours)

**Total estimated effort**: 15-22 hours spread over 4-5 weeks

---

**Pro Tip**: Start with the 🔴 **Critical** items first. Even having just the architecture diagrams and 2-3 key videos will make your portfolio 10x more compelling than text alone!

Good luck! 🎉
