## Project Overview

**Project Name:** OsonSavdo

**Core Purpose:** An all-in-one Inventory Management and Point of Sale (POS) web application designed for small to medium-sized businesses in Uzbekistan.

**Key Features:**

- Product and Catalog Management (with nested sub-categories).
- Inventory Control (stock levels, variations).
- Point of Sale (POS) Interface for creating sales transactions.
- Customer Management (CRM).
- Financial Reporting and Dashboards.
- User Authentication and Subscription-based Monetization.

## Core Technology Stack

- **Framework:** Next.js (using the App Router).
- **Language:** TypeScript.
- **Database:** PostgreSQL hosted on Neon.
- **ORM:** Prisma.
- **Authentication:** Auth.js (NextAuth.js).
- **UI Components:** Shadcn/UI.
- **Styling:** Tailwind CSS.
- **State Management:**
  - **Server State:** TanStack Query (React Query).
  - **Global Client State:** Zustand.
- **Forms:** React Hook Form with Zod for schema validation.
- **Deployment:** Vercel.

## Architectural Rules & Coding Standards

This is how we build things in the OsonSavdo project. Follow these patterns strictly.

### 1. General Next.js & TypeScript Standards

- Use the **App Router** (`/app` directory) and **React Server Components** by default. Only use Client Components (`'use client'`) when interactivity is required.
- Use `zod` to validate all API input and response schemas. Schemas are the single source of truth and are located in `src/lib/schemas.ts`.
- Use `PascalCase` for components and types. Use `camelCase` for hooks, functions, and variables.
- Use `kebab-case` for all file and folder names.

### 2. Folder & Component Structure

- Organize code into **feature-based folders** (e.g., `src/features/products/`, `src/features/auth/`).
- **`src/components/ui/`**: Contains base components from the Shadcn/UI CLI.
- **`src/components/shared/`**: Contains application-specific components reused across multiple features (e.g., `PageHeader`).
- **`src/features/[feature-name]/components/`**: Contains components specific to a single feature.
- **`src/lib/`**: Contains backend/server-side utilities, API functions, schemas, and constants.
- Keep `page.tsx` files minimal. Delegate complex UI and logic to dedicated feature-level components.
- Use Next.js Layout component for consistent page structure in route groups.

### 3. Data Fetching (TanStack Query)

- Use `@tanstack/react-query` for **all** client-side data fetching and mutation logic.
- **Custom Hooks are Mandatory:** Encapsulate all query and mutation logic in custom hooks.
  - **Naming:** Prefix hooks with `use` and suffix them with `Query` or `Mutation` (e.g., `useGetProductsQuery`, `useCreateProductMutation`).
  - **Location:** Co-locate these hooks within their feature folder (e.g., `src/features/products/hooks/`).
- **Query Keys:** Manage all query keys in a central `src/lib/queryKeys.ts` file to avoid typos and ensure consistency. Group keys as tuples (e.g., `export const productKeys = { all: ['products'], detail: (id) => ['products', id] }`).
- **Data Transformation:** Use the `select` option in `useQuery` to transform or select a slice of data on the caching layer, not in the component.
- **UI Synchronization:** Use `invalidateQueries` for simple cache invalidation after mutations. For a better UX, use `optimistic updates` for mutations that should feel instantaneous.

### 4. State Management

1.  **Server State (API Data):** Always use **TanStack Query**.
2.  **Global Client State:** Use **Zustand** for client-side state shared across components (e.g., POS cart). Stores are in `src/store/`.
3.  **Local Component State:** Use `useState` only for non-shared, component-local state.

### 5. Styling

- Use **Tailwind CSS exclusively**. Do not write separate CSS files.
- Use the `cn()` utility from `src/lib/utils.ts` for conditional classes.

### 6. Error Handling

- Use `onError` in queries and mutations for specific failure states.
- Use Next.js **Error Boundaries** (`error.tsx`) for handling unexpected errors.
- Use shadcn Sonner component for notification of user feedback on success or failure of mutations.

### 7. Security

- All sensitive logic (token exchange, direct database calls) **must** be in Next.js API routes or files using the `server-only` package.
- Sanitize all output rendered from untrusted sources to prevent XSS attacks.
- Handle authorization server-side in API routes or middleware.
