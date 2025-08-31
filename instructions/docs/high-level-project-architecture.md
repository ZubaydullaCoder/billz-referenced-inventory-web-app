This document serves as the master blueprint, summarizing all our technical decisions and providing a single source of truth for the project's structure and standards.

---

### **High-Level Project Architecture: OsonSavdo**

**Version:** 1.0
**Date:** 2025-08-26

### 1. Purpose

This document provides a comprehensive, high-level overview of the technical architecture for the OsonSavdo Inventory Management & POS system. It is intended to be the primary technical guide for the development team (you), defining the core components, technology stack, data flows, and guiding principles.

### 2. Core Architectural Principles

- **Monorepo Simplicity:** The entire application (front-end, back-end, data definitions) resides in a single Next.js repository to simplify development, dependency management, and deployment.
- **Type-Safety End-to-End:** TypeScript, Prisma, and Zod are used to ensure data is strongly typed from the database to the UI, minimizing runtime errors.
- **Serverless-First:** The system is designed for deployment on a serverless platform (Vercel), eliminating server management and providing automatic, cost-effective scaling.
- **Product-Led Growth (PLG):** The architecture directly supports a self-service business model, including a marketing landing page, automated free trials, and a streamlined, single-tier subscription flow.

### 3. Technology Stack Summary

| Category                 | Technology                  | Purpose                                                                                         |
| ------------------------ | --------------------------- | ----------------------------------------------------------------------------------------------- |
| **Core Framework**       | Next.js (App Router)        | Full-stack framework for UI, server logic, and API endpoints.                                   |
| **Language**             | TypeScript                  | Ensures type safety across the entire codebase.                                                 |
| **Database**             | PostgreSQL (hosted on Neon) | Robust, serverless relational database for all application data.                                |
| **ORM**                  | Prisma                      | Type-safe interface for all database queries and schema migrations.                             |
| **Authentication**       | Auth.js (NextAuth.js)       | Manages user sign-up, login, sessions, and route protection.                                    |
| **UI Components**        | Shadcn/UI                   | Foundation for a consistent, accessible, and customizable UI.                                   |
| **Styling**              | Tailwind CSS                | Utility-first CSS framework for rapid UI development.                                           |
| **State Management**     | TanStack Query & Zustand    | TanStack for server state (caching API data), Zustand for global client state (e.g., POS cart). |
| **Forms & Validation**   | React Hook Form & Zod       | Performant form handling and strict, type-safe data validation.                                 |
| **Payment Provider**     | Payme                       | Handles both POS transactions and user subscription payments.                                   |
| **Deployment & Hosting** | Vercel                      | Zero-configuration deployment, CI/CD, and hosting platform.                                     |

### 4. System Architecture Overview

The application is composed of four distinct layers that work in concert.

#### 4.1. Client-Side (The Browser)

This is the user-facing part of the application. It's built with **React** and rendered by the **Next.js App Router**. Its primary responsibilities are rendering the UI and capturing user input. All data from the server is managed via **TanStack Query**, which handles caching, loading, and error states. Global UI state, like the contents of the POS sales cart, is managed by **Zustand**.

#### 4.2. Application Server (Next.js on Vercel)

This is the core of our system, acting as both the web server and the API back-end.

- **API Layer:** Implemented as **Next.js API Routes** within the `/app/api/` directory. These are serverless functions that handle all incoming requests from the client.
- **Business Logic:** All business logic, such as creating a sale, updating inventory, or generating a payment invoice, is executed here.
- **Authentication:** **Auth.js** runs on this layer, managing sessions and protecting API endpoints.

#### 4.3. Data Layer (Prisma & Neon)

This layer is responsible for all data persistence.

- The **Neon** service provides a hosted, serverless **PostgreSQL** database.
- The Application Server **only** communicates with the database through the **Prisma Client**. This ensures all database access is type-safe and consistent. The `prisma/schema.prisma` file is the absolute source of truth for the database structure.

#### 4.4. External Services

- **Payme API:** This is our sole external payment provider. The Application Server makes secure, server-to-server API calls to Payme for two distinct purposes:
  1.  Processing one-time payments for sales in the POS interface.
  2.  Generating invoices for user subscription payments.

### 5. Key Data Flows

#### 5.1. POS Sale Flow

1.  **Client:** Cashier adds items to a cart managed by a **Zustand** store.
2.  **Client -> Server:** On checkout, the client sends a `POST` request to `/api/sales` with the cart data.
3.  **Server:** The API route validates the incoming data with **Zod**, then initiates a **Prisma transaction**. Within the transaction, it creates a `Sale` record and decrements the stock for each `ProductVariation`.
4.  **Server -> Client:** The server returns the created sale object, and the client displays a success message.

#### 5.2. Subscription Activation Flow

1.  **Client:** A user with an expired trial clicks "Subscribe."
2.  **Client -> Server:** The client sends a `POST` request to `/api/subscriptions/create-payment-invoice`.
3.  **Server -> Payme:** The server calls the Payme API to generate a one-time invoice for the subscription fee.
4.  **Server -> Client:** The server returns the payment link/QR code data to the client, who then pays.
5.  **Payme -> Server:** Payme sends a **webhook** to `/api/webhooks/payme` confirming the payment.
6.  **Server:** The webhook handler verifies the request, finds the relevant `Store`, and updates its `paidUntil` date in the database via **Prisma**. The user's access is now active.

### 6. Deployment & CI/CD

- **Git Workflow:** A two-branch system is used:
  - `main-for-development`: All active development and commits happen here.
  - `main-for-production`: This stable branch is only updated by merging from the development branch when ready for a release.
- **Vercel Configuration:** Vercel is connected to the GitHub repository. The **Production Branch** is set to `main-for-production`. Any push to this branch automatically triggers a production deployment. Pushes to other branches create shareable preview deployments.

### 7. MVP Scope & Deferred Features

To ensure a focused and rapid initial launch, the following features are explicitly **out of scope** for the MVP:

- Full Multi-Store Management & Inter-Store Transfers.
- Complex Promotions (e.g., "Buy X, Get Y", promo codes).
- Telegram Bot Notifications.
- Bulk Product Import via Excel/CSV.
- Product Image Management.
