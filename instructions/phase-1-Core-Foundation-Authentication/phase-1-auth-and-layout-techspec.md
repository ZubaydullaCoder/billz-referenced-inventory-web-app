Excellent. Switching to the **Software Architect** persona.

This technical specification provides a detailed, step-by-step guide for implementing Phase 1: Core Foundation & Authentication. The plan is broken down into small, manageable tasks, starting from the database layer and moving up to the UI.

**Document:** `phase-1-auth-and-layout-techspec.md`
**Version:** 1.0

---

### **Technical Specification: Phase 1 - Core Foundation & Authentication**

This document outlines the technical implementation details for user registration, login, session management, and the main application layout for the OsonSavdo project.

#### **Part 1: Database Schema & Authentication Backend**

This section covers the database model definitions and the server-side logic required for authentication.

**Task 1: Update Prisma Schema**

- **Objective:** Define the database models required for user accounts, authentication sessions, and stores.
- **File:** `prisma/schema.prisma`
- **Steps:**
  1.  Define the `User` model. It should include fields for `id`, `name`, `email`, `emailVerified`, `image`, and `hashedPassword`. The `email` field must be unique.
  2.  Define the `Store` model. It must include an `id`, `name`, and a relation to the `User` model. A user can have multiple stores, but a store belongs to one user.
      - Add a `stores Store[]` field to the `User` model.
      - Add `userId String` and a `@relation` field to the `Store` model.
  3.  Add the standard models required by the `@auth/prisma-adapter`: `Account`, `Session`, `VerificationToken`. You can copy these directly from the Auth.js documentation to ensure compatibility.
  4.  Run `npx prisma generate` to update the Prisma Client with the new models.
  5.  Run `npx prisma db push` (or create a migration) to sync the schema with your database.

**Task 2: Install Dependencies**

- **Objective:** Add the necessary packages for authentication and password handling.
- **Steps:**

  1.  Open the terminal and run the following command and pin the version you target (do not leave `beta` floating in production):

      ```bash
      npm install next-auth@beta @auth/prisma-adapter bcryptjs @types/bcryptjs
      ```

  - **Note:** This project targets the App Router-compatible Auth.js/NextAuth v5 API. Pin the exact version (for example `next-auth@5.0.0-canary.x` or the stable v5 when published) and verify imports against the official docs. `bcryptjs` is safe and pure‑JS; consider `bcrypt` (native) if your deployment supports native modules and you want better performance.

**Task 3: Configure Auth.js (NextAuth.js)**

- **Objective:** Create the central configuration file for `Auth.js`.
- **File:** `auth.ts` (create this at the root of the project).
- **Steps:**
  1.  Import `NextAuth` from `next-auth`, `PrismaAdapter` from `@auth/prisma-adapter`, and `Credentials` from `next-auth/providers/credentials`.
  2.  Import the Prisma client instance.
  3.  Define and export the `auth`, `handlers`, `signIn`, and `signOut` functions using the `NextAuth` constructor.
  4.  Inside the configuration object:
      - Set the `adapter` to `PrismaAdapter(prisma)`.
      - Configure `session` with a `strategy` of `"jwt"`.
      - Define the `pages` object to specify custom paths for `signIn: '/auth/login'`.
      - Add the `Credentials` provider to the `providers` array. Leave the `authorize` function empty for now; we will implement it in Task 5.

**Task 4: Implement Registration API Endpoint**

- **Objective:** Create the server-side endpoint to handle new user registration.
- **File:** `app/api/auth/register/route.ts`
- **Steps:**

  1.  Create the file and define an async `POST` function.
  2.  Use Zod to define a schema for the request body, expecting `name` (string), `email` (string, email format), and `password` (string, min 8 characters).
  3.  In the `POST` handler, parse and validate the request body using the Zod schema.
  4.  Normalize the incoming email (e.g., `email = email.trim().toLowerCase()`) before any checks. Query the database using Prisma to check if a user with the normalized email already exists. If so, return a 409 Conflict response.
  5.  Import `bcryptjs` and hash the user's password with a salt round of 12.
  6.  Use `prisma.$transaction` to perform an atomic operation:

      - First, create the `User` record with the provided name, normalized email, and the newly hashed password.
      - Second, using the `id` from the newly created user, create the `Store` record with the default name "Main Store".

  - Note: Handle unique-constraint race conditions: even after the pre-check, concurrent requests may cause a duplicate key error. Catch the Prisma unique constraint error and return 409. Prefer creating a unique index on `email` (Prisma `@unique`) and rely on the DB constraint for final consistency.

  7.  Return a 201 Created response on success. Handle any potential database errors with a 500 Internal Server Error response.

**Task 5: Implement `authorize` Logic for Login**

- **Objective:** Add the logic to the Credentials provider to validate user login attempts.
- **File:** `auth.ts`
- **Steps:**
  1.  Navigate to the `Credentials` provider configuration.
  2.  Implement the `authorize` async function. It receives `credentials` as an argument.
  3.  Use Zod to validate that `credentials` contains a valid email and password. Normalize `credentials.email` before querying (trim + toLowerCase).
  4.  Query the database using Prisma to find a user by the provided `email`.
  5.  If no user is found or if the user does not have a `hashedPassword` (e.g., they signed up via an OAuth provider in the future), return `null`. For security, return generic failure messages from the API/UI (avoid revealing whether email exists).
  6.  Use `bcryptjs.compare()` to check if the provided password matches the hashed password from the database.
  7.  If the passwords match, return the `user` object.
  8.  If the passwords do not match, return `null`.

---

This completes the backend setup. The next part will focus on creating the frontend pages for registration and login. Please confirm to proceed.

Acknowledged. Continuing as the **Software Architect**.

This final part of the technical specification for Phase 1 details the implementation of the main application layout and the crucial middleware for securing application routes.

---

### **Technical Specification: Phase 1 - Core Foundation & Authentication**

#### **Part 3: Application Layout & Route Protection**

This section covers the creation of the persistent UI shell for authenticated users and the middleware that enforces authentication checks, redirecting unauthenticated users away from protected content.

**Task 10: Create the Main Application Layout**

- **Objective:** Build the primary layout structure with a sidebar and header for authenticated users.
- **File:** `app/(app)/layout.tsx`
- **Steps:**
  1.  Create a route group `(app)` to apply this layout to all nested routes (e.g., `/dashboard`, `/products`). This cleanly separates authenticated routes from others like `/auth`.
  2.  Inside `app/(app)/`, create the `layout.tsx` file. This will be a server component.
  3.  Define the main structure using a flexbox or CSS grid layout: a `div` for the sidebar and a `div` for the main content area.
  4.  **Sidebar Component:**
      - Create a new client component: `components/layout/sidebar.tsx`.
      - Inside this component, use the `usePathname` hook from `next/navigation` to determine the active route.
      - Create an array of navigation items (e.g., `{ href: '/dashboard', label: 'Dashboard', icon: <IconComponent /> }`).
      - Map over this array to render Next.js `<Link>` components.
      - Use the `cn` utility to conditionally apply an "active" class (e.g., `bg-muted`) if a link's `href` matches the current `pathname`.
      - Import and render this `Sidebar` component within `app/(app)/layout.tsx`.
  5.  **Header Component:**
      - Create a new server component: `components/layout/header.tsx`.
      - Inside this component, use the `auth` function from our root `auth.ts` to get the current session server-side (`const session = await auth()`).
      - Display the user's name (`session?.user?.name`) inside a User Profile Dropdown.
      - **User Profile Dropdown:**
        - Create a new client component: `components/layout/user-nav.tsx`.
        - Use Shadcn/UI's `DropdownMenu` components.
        - The dropdown trigger will be a button showing the user's name or avatar.
        - The dropdown content will display the user's full name and email.
        - Include a `DropdownMenuItem` for "Logout".
        - **Logout Button:** Create a simple form with a single button that calls the `signOut` server action from `auth.ts`. This is the recommended secure way to handle logout in Next.js 14+.
      - Render the `Header` component within `app/(app)/layout.tsx`.
  6.  Render the `{children}` prop inside the main content area of the layout file.

**Task 11: Create Placeholder Pages**

- **Objective:** Create the initial pages that the layout will wrap to make the application navigable.
- **Files:**
  - `app/(app)/dashboard/page.tsx`
  - `app/(app)/products/page.tsx`
  - `app/(app)/pos/page.tsx`
  - `app/(app)/settings/page.tsx`
- **Steps:**
  1.  For each file, create a simple server component that renders a heading, e.g., `<h1>Dashboard</h1>`. This confirms that routing and the layout are working correctly.

**Task 12: Implement Route Protection Middleware**

- **Objective:** Create middleware to protect all routes within the `(app)` group, redirecting unauthenticated users to the login page.
- **File:** `middleware.ts` (create this at the root of the project).
- **Steps:**
  1.  Import `auth` from `auth.ts`.
  2.  Export the `auth` function as the default export. This is the simplest way to protect the entire app with `NextAuth.js` v5.
  3.  Add a `config` object to the file to specify which routes the middleware should apply to. Use the `matcher` property to include all routes except for static files, `_next` assets, and public routes like `/auth/*`.
      ```typescript
      export const config = {
        matcher: ["/((?!api|_next/static|_next/image|favicon.ico|auth).*)"],
      };
      ```
  - **How it works:** The `auth` function from `NextAuth.js`, when used as middleware, automatically checks for a valid session. If no session exists, it will redirect the user to the login page specified in the `pages` configuration of `auth.ts`.

---

This concludes the technical specification for Phase 1. We have a complete, step-by-step plan covering the database schema, backend authentication logic, frontend UI for login/registration, the main application layout, and the security middleware. The project is now ready for implementation.
