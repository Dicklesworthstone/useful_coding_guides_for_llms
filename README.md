# Useful Coding Guides for LLMs

This repository contains a growing body of documentation about how to build robust, maintainable Next.js 15 applications while collaborating with large language model coding tools. The goal is to provide a clear reference so that both humans and AI assistants can work together on complex codebases.

The main documents in this repository include:

* **NEXTJS15_BEST_PRACTICES.md** – a nearly 1000 line guide describing recommended architecture and patterns for modern Next.js 15 projects.
* **NEXT_BEST_PRACTICES_IMPLEMENTATION_PROGRESS.md** – a detailed checklist that tracks which parts of the guide have been implemented in a real codebase.
* **PROMPT_TO_GENERATE_AND_REVISE_GUIDES.md** – the seed prompt used to research best practices and compile the guide.
* **EXAMPLE_USING_THE_TECHNIQUE_IN_CLAUDE_CODE.md** – a transcript demonstrating how to iteratively apply the guide with an agentic coding assistant like Claude Code.

## Recommended Workflow

The documentation in this repo grew out of a real project that combined a Python FastAPI backend with a modern Next.js 15 frontend.  To establish a solid baseline, multiple models — Claude Opus 4, OpenAI’s GPT‑4o, and Gemini Deep Research — were asked to scour the web for the latest best practices in this stack.  Their findings were merged into a single guide using a model with a large context window (Gemini 2.5 Pro or Opus works well).

Iterate on the guide until the models largely agree that it captures the current state of the art.  Example prompts for this refinement loop live in `PROMPT_TO_GENERATE_AND_REVISE_GUIDES.md`, and the resulting document is `NEXTJS15_BEST_PRACTICES.md`.

Once the guide is stable, commit your code to git and launch Claude Code in background task mode.  Many users create a convenient alias:

```bash
alias cc='ENABLE_BACKGROUND_TASKS=1 claude --dangerously-skip-permissions'
```

Ask Claude Code to carefully read the best practices guide and begin implementing it across the project.  Progress is tracked in `NEXT_BEST_PRACTICES_IMPLEMENTATION_PROGRESS.md`.  Because models sometimes overstate completion, review this progress file yourself and ensure it only marks tasks complete once the pattern is applied throughout the codebase.

As the project evolves, you may need to start a fresh conversation (for instance, when swapping API keys or compacting the context).  Each time, load both the best practices document and the progress tracker so the model has full context.  Commit between iterations so changes can be reverted if necessary.

---

When using these files with an AI, the typical workflow looks like this:

1. **Read the full guide** to understand the recommended approach for file structure, state management, server actions, caching, and more. The guide begins with the statement:

   > This guide synthesizes modern best practices for building scalable, secure, and performant applications with Next.js 15, the App Router, and Zustand. It moves beyond basic patterns to provide a production-grade architectural blueprint.

2. **Open the progress document** and carefully mark which patterns are already implemented. The progress doc explicitly warns:

   > A task is only complete when the pattern is applied throughout the entire application.

3. **Run the prompts from PROMPT_TO_GENERATE_AND_REVISE_GUIDES.md** or craft your own similar prompts to direct the AI. The prompts encourage the assistant to continuously reference the best practices guide and to keep the progress document accurate.

4. **Iterate** – as tasks are completed, update NEXT_BEST_PRACTICES_IMPLEMENTATION_PROGRESS.md and verify that the new code aligns with the best practices.

This README merges the entire best practices guide directly below so that everything is available in a single file. The aim is to make the repository a one-stop reference when working with an agentic coding tool. By providing abundant context, we reduce hallucinations and keep the assistant focused on real, actionable advice.

---

---

## Using This Repository with Agentic Coding Tools

The reason this repository places such emphasis on comprehensive documentation is that modern large language models work best when they have copious, accurate context. By embedding the entire best practices guide here in the README, we make it extremely easy for tools like Claude Code to reference the information they need.

When guiding an AI through a codebase, follow these steps:

1. **Load this README** in the context window. Its length ensures the model sees key patterns without needing to jump around between files.
2. **Supply the implementation progress document** when asking for help updating the project. This keeps the model honest about which tasks remain unfinished.
3. **Show concrete code snippets** from your working tree so the model can align proposed changes with the actual structure of your project.
4. **Iterate gradually**. After each batch of changes, run any programmatic checks and update NEXT_BEST_PRACTICES_IMPLEMENTATION_PROGRESS.md so that the AI has an up-to-date picture.

This method of coupling thorough docs with incremental progress tracking has proven effective for large refactors. The example transcript in `EXAMPLE_USING_THE_TECHNIQUE_IN_CLAUDE_CODE.md` demonstrates how step-by-step requests and careful progress updates keep the AI grounded.

Below is the complete best practices guide for convenience.
# The Definitive Guide to Next.js 15, App Router, and Zustand (2025)

This guide synthesizes modern best practices for building scalable, secure, and performant applications with Next.js 15, the App Router, and Zustand. It moves beyond basic patterns to provide a production-grade architectural blueprint.

### Prerequisites & Configuration
Ensure your project uses **Next.js 15.3.4+**, **React 19.1+**, and **TypeScript 5.6+**.

Next.js 15 now supports TypeScript configuration files. Enable modern features in your `next.config.ts`:

```typescript
// next.config.ts
import type { NextConfig } from 'next'

const nextConfig: NextConfig = {
  // Server Actions are stable and the preferred mutation path
  serverActions: true,
  // React Compiler is now production-ready in 15.3
  experimental: {
    reactCompiler: true, // build-time auto-memoization
    typedRoutes: true,   // statically-typed <Link> (still experimental)
    ppr: 'incremental',  // opt-in to Partial Prerendering per route
    after: true,         // Enable the new 'after' API
  },
  // Renamed configuration options
  serverExternalPackages: ['@node-rs/argon2'], // renamed from serverComponentsExternalPackages
  bundlePagesRouterDependencies: true, // renamed from bundlePagesExternals
}

export default nextConfig
```

> **Note**: You can also use `next.config.mjs` if you prefer ES modules without TypeScript.

---

## 1. Foundational Architecture & File Organization

A well-defined structure is critical for scalability and maintainability. Adopt a `src` directory and a hybrid approach of centralized and colocated code.

### ✅ DO: Use a Scalable `src` Layout

This structure separates concerns, simplifies tooling, and provides a clear map of the application.

```
/src
├── app/                  # App Router: Routing, layouts, pages, and route-specific logic
│   ├── (auth)/           # Route group for authentication pages
│   │   └── login/
│   │       ├── _actions.ts # Server Actions specific to login
│   │       ├── login-form.tsx # Client Component
│   │       └── page.tsx      # Server Component
│   └── (dashboard)/      # Route group for protected dashboard
│       ├── layout.tsx      # Dashboard-specific layout
│       └── page.tsx        # Server Component
├── components/           # Globally reusable React components (see Section 15 for detailed architecture)
│   ├── ui/               # Atomic design system components (Button, Card, Input)
│   ├── layout/           # Structural components (Header, Footer, Sidebar)
│   └── features/         # Complex components for specific domains (ProductSearch)
├── lib/                  # Core application logic and external service integrations
│   ├── api/              # Auto-generated API client and configuration
│   ├── auth/             # Server-side auth logic (session validation)
│   └── utils/            # Pure, stateless utility functions (formatters)
├── stores/               # Zustand store definitions and providers
│   ├── auth-store.ts     # Store factory function for auth state
│   └── ui-store.ts       # Store factory function for UI state
├── hooks/                # Globally reusable client-side React hooks (useMediaQuery)
└── middleware.ts         # Edge middleware for route protection
```

### ✅ DO: Colocate Route-Specific Logic

For components, hooks, or Server Actions used only by a single route, colocate them within that route's directory using **private folders** (e.g., `_components`). This improves discoverability and keeps related files together without creating new URL segments.

### Partial Pre-rendering (PPR) Configuration

For any route segment where you need the new PPR behaviour, add to that segment's `layout.tsx`:

```typescript
export const experimental_ppr = true
```

PPR keeps the static shell cached while streaming the dynamic holes and works wonderfully with Server Actions revalidation.

---

## 2. The Server-First Data Paradigm

Next.js 15 is server-first. Data fetching, rendering, and caching should happen on the server by default.

### ✅ DO: Fetch Data in Server Components

Use `async/await` directly in Server Components (`page.tsx`, `layout.tsx`) to fetch data. This reduces latency, enhances security by keeping secrets on the server, and minimizes the client-side JavaScript bundle.

```typescript
// Good - app/dashboard/page.tsx (Server Component)
import { apiClient } from '@/lib/api/client';
import { DashboardClient } from './_components/dashboard-client';

export default async function DashboardPage() {
  // Data is fetched directly on the server during render
  const dashboardData = await apiClient.GET('/dashboard/data');
  
  // The fetched data is passed as props to a Client Component
  return <DashboardClient initialData={dashboardData.data} />;
}
```

### ❌ DON'T: Use `useEffect` or Client Components for Initial Data Fetching

This legacy pattern creates network waterfalls, increases latency, and sends unnecessary JavaScript to the client.

```typescript
// Bad - pages/dashboard/page.tsx
'use client'
import { useEffect, useState } from 'react'

export default function DashboardPage() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('/api/dashboard')
      .then(res => res.json())
      .then(data => {
        setData(data);
        setLoading(false);
      });
  }, []);
  
  if (loading) return <Loading />;
  return <Dashboard data={data} />;
}
```

### Understanding Caching in Next.js 15

Next.js 15 makes caching **explicit and opt-in**. By default, `fetch` requests and GET route handlers are **not cached**.
*   **Opt-in to Caching**: Use `fetch('/api/…', { next: { revalidate: 60 } })` for time-based revalidation (ISR) or `{ cache: 'force-cache' }`.
*   **On-Demand Invalidation**: Tag fetches with `{ next: { tags: ['my-tag'] } }` and use `revalidateTag('my-tag')` in a Server Action to bust the cache.
*   **Dynamic Data**: For per-user data that should never be cached, use `fetch('/api/…', { cache: 'no-store' })`.
*   **Router Cache**: Configure `staleTimes` in next.config for client-side navigation caching.

### React 19 Integration and New Features

#### The `use()` Hook for Promise Unwrapping
React 19 introduces the `use()` hook for unwrapping promises in Client Components, which is particularly useful with Next.js 15's async params and searchParams:

```typescript
// app/products/[id]/page.tsx
'use client'
import { use } from 'react'

type Params = Promise<{ id: string }>
type SearchParams = Promise<{ filter?: string }>

export default function ProductPage(props: { 
  params: Params
  searchParams: SearchParams 
}) {
  const params = use(props.params)
  const searchParams = use(props.searchParams)
  
  return <div>Product {params.id} with filter: {searchParams.filter}</div>
}
```

#### Improved JSX Transform
React 19 eliminates the need to import React in files using JSX, reducing boilerplate:

```typescript
// No longer needed: import React from 'react'

export function Component() {
  return <div>Hello!</div> // Works without React import
}
```

---

## 3. Data Mutations: Server Actions are King

Server Actions are the preferred way to handle data mutations. They run securely on the server, can be called directly from client components, and integrate seamlessly with the Next.js cache.

> **⚠️ Warning**: Use Server Actions for mutations only, not for read-only queries. Using them for data fetching incurs an extra POST request, defeating the server-component model and adding unnecessary latency.

### ✅ DO: Use Server Actions for Mutations

This pattern is secure, efficient, and avoids the need for manual API route handlers.

**1. Define the Action (`'use server'`)**
Use Zod for validation and `revalidatePath` or `revalidateTag` to update the UI on the next request.

```typescript
// app/dashboard/_actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { z } from 'zod'
import { apiClient } from '@/lib/api/client' // Your typed API client

const WidgetSchema = z.object({ name: z.string().min(3) });

export async function createWidget(prevState: any, formData: FormData) {
  try {
    const validatedData = WidgetSchema.parse(Object.fromEntries(formData));
    await apiClient.POST('/widgets', { body: validatedData });
    revalidatePath('/dashboard'); // Invalidate cache for the dashboard page
    return { message: 'Widget created successfully.' };
  } catch (e) {
    return { error: 'Failed to create widget.' };
  }
}
```

**2. Call from a Client Component Form**
Use the `useActionState` hook from React 19 (replacing the deprecated `useFormState`) to handle pending states and responses.

```typescript
// app/dashboard/_components/create-widget-form.tsx
'use client'

import { useActionState, useFormStatus } from 'react'
import { createWidget } from '../_actions'

function SubmitButton() {
  const { pending } = useFormStatus();
  return <button type="submit" disabled={pending}>{pending ? 'Creating...' : 'Create'}</button>;
}

export function CreateWidgetForm() {
  const [state, formAction] = useActionState(createWidget, { message: null });

  return (
    <form action={formAction}>
      <input type="text" name="name" required />
      <SubmitButton />
      {state?.error && <p style={{ color: 'red' }}>{state.error}</p>}
      {state?.message && <p style={{ color: 'green' }}>{state.message}</p>}
    </form>
  )
}
```

### Server Actions Security Enhancements (Next.js 15)

Next.js 15 introduces significant security improvements:

**1. Unguessable Action IDs**: Server Actions now use cryptographically secure, non-deterministic IDs that are periodically recalculated between builds.

**2. Dead Code Elimination**: Unused Server Actions are automatically removed from the client bundle, reducing attack surface and bundle size.

```typescript
// app/actions.ts
'use server'

// This unused action won't be exposed to the client
async function unusedAction() {
  // Never called, automatically eliminated
}

// Only this action gets a secure ID in the client bundle
export async function usedAction(data: FormData) {
  // Validate and authorize as before
  const user = await getCurrentUser()
  if (!user) throw new Error('Unauthorized')
  
  // Process action...
}
```

**3. Environment Variable for Encryption Key**: For consistent action IDs across multiple builds or servers:

```bash
# .env
NEXT_SERVER_ACTIONS_ENCRYPTION_KEY=your-32-byte-base64-key
```

### ✅ DO: Combine Server Actions with `useMutation` for Optimistic UI

For an exceptional user experience, wrap Server Actions with TanStack Query's `useMutation` hook. This provides loading states, error handling, and instantaneous UI updates *before* the server responds.

This clarifies the modern role of TanStack Query: it's not for initial data fetching (which should happen in Server Components), but for managing the client-side lifecycle of asynchronous mutations.

```typescript
// app/todos/_components/todo-list.tsx
'use client'

import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'
import { addTodo } from '../_actions'
import { getTodos } from '../_data' // A client-callable fetch function

export function TodoList({ initialTodos }) {
  const queryClient = useQueryClient();

  // 1. TanStack Query manages the client cache of server data
  const { data: todos } = useQuery({
    queryKey: ['todos'],
    queryFn: getTodos,
    initialData: initialTodos,
  });

  // 2. useMutation wraps the Server Action for a rich UI experience
  const { mutate: addTodoMutation } = useMutation({
    mutationFn: (newTodoText: string) => addTodo(newTodoText), // Server Action

    onMutate: async (newTodoText: string) => {
      // Optimistically update the UI
      await queryClient.cancelQueries({ queryKey: ['todos'] });
      const previousTodos = queryClient.getQueryData(['todos']);
      queryClient.setQueryData(['todos'], (old: any) => [
       ...(old ?? []),
        { id: Math.random(), text: newTodoText, completed: false }, // Temporary
      ]);
      return { previousTodos };
    },
    onError: (err, newTodo, context) => {
      // Roll back on failure
      queryClient.setQueryData(['todos'], context.previousTodos);
    },
    onSettled: () => {
      // Sync with server state once the action is complete
      queryClient.invalidateQueries({ queryKey: ['todos'] });
    },
  });

  // JSX form calls `addTodoMutation('New todo text')`
  return (/* ... */)
}
```

---

## 4. Mastering Zustand for Client-Side UI State

Zustand is for **global, client-side UI state only**. Using it incorrectly in an SSR environment can lead to critical bugs and security vulnerabilities.

| Type of State | Source of Truth | Recommended Tool | Example |
| :--- | :--- | :--- | :--- |
| **Server Data (Canonical)** | Backend Database / API | N/A (Server-Side) | User record in a database |
| **Server Data (Client Cache)**| Server | **TanStack Query** | A list of products cached on the client |
| **Global UI State** | Client Interaction | **Zustand** | Theme (dark/light), sidebar open/closed |
| **Local Component State** | Client Interaction | **`useState` / `useReducer`**| The value of a controlled input field |
| **URL State** | Browser URL | **Next.js `useRouter` / `<Link>`**| Search filters, page number |

### ❌ DON'T: Create a Global Singleton Store

This is the most common and dangerous anti-pattern. A global store is shared across all user requests on the server, leading to **data leakage between users**.

```typescript
// Bad - DO NOT DO THIS
import { create } from 'zustand'

// This creates a single, shared instance for the entire server process!
export const useStore = create<MyState>(set => ({
  // ...
}))
```

> **Important**: The official Next.js guide and core maintainers reiterate that every request must get its own store instance to avoid cross-user leaks.

### ✅ DO: Create Per-Request Stores with Context

This is the official, safe pattern for using Zustand with SSR. A new store instance is created for every server request, ensuring user data is isolated.

**1. Create a Store Factory Function**
Use `createStore` from `zustand/vanilla` and Immer middleware for easy state updates.

```typescript
// src/stores/ui-store.ts
import { createStore } from 'zustand/vanilla'
import { immer } from 'zustand/middleware/immer'

export type UIState = { sidebarOpen: boolean }
export type UIActions = { toggleSidebar: () => void }
export type UIStore = UIState & UIActions

// The factory creates a new store instance every time it's called
export const createUIStore = (initState: Partial<UIState> = {}) => {
  return createStore<UIStore>()(
    immer((set) => ({
      sidebarOpen: true,
      toggleSidebar: () => set((state) => {
        state.sidebarOpen = !state.sidebarOpen
      }),
      ...initState,
    }))
  )
}
```

**2. Create a Provider and a Custom Hook**
The provider creates the store instance once per request and makes it available via context.

```typescript
// src/stores/ui-store-provider.tsx
'use client'

import { type ReactNode, createContext, useRef, useContext } from 'react'
import { type StoreApi, useStore } from 'zustand'
import { type UIStore, createUIStore } from './ui-store'

export const UIStoreContext = createContext<StoreApi<UIStore> | undefined>(undefined)

export function UIStoreProvider({ children }: { children: ReactNode }) {
  const storeRef = useRef<StoreApi<UIStore>>()
  if (!storeRef.current) {
    storeRef.current = createUIStore()
  }

  return (
    <UIStoreContext.Provider value={storeRef.current}>
      {children}
    </UIStoreContext.Provider>
  )
}

// Custom hook for easy access, ensuring the provider is present
export const useUIStore = <T,>(selector: (store: UIStore) => T): T => {
  const context = useContext(UIStoreContext)
  if (!context) {
    throw new Error('useUIStore must be used within a UIStoreProvider')
  }
  return useStore(context, selector)
}
```

**3. Add the Provider to Your Root Layout**

```typescript
// src/app/layout.tsx
import { UIStoreProvider } from '@/stores/ui-store-provider'

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <UIStoreProvider>{children}</UIStoreProvider>
      </body>
    </html>
  )
}
```
Now, any client component can safely use `const { sidebarOpen, toggleSidebar } = useUIStore(state => state)` to interact with the UI state.

### Advanced Zustand Patterns

#### Skip Hydration for Persist Middleware
When using persist middleware, use the skipHydration option to control hydration timing:

```typescript
// src/stores/user-preferences-store.ts
import { create } from 'zustand'
import { persist, createJSONStorage } from 'zustand/middleware'

const usePreferencesStore = create<PreferencesStore>()(
  persist(
    (set) => ({
      theme: 'light',
      fontSize: 'medium',
      setTheme: (theme) => set({ theme }),
    }),
    {
      name: 'user-preferences',
      skipHydration: true, // Prevent automatic hydration
    }
  )
)

// Hydration component
export function PreferencesHydrator() {
  useEffect(() => {
    usePreferencesStore.persist.rehydrate()
  }, [])
  
  return null
}
```

> **⚠️ Zustand v5 + Immer Typing Issue**: There's a known inference issue where Immer drafts get typed as `any`. Either pin to `zustand@5.0.2` or ensure `immer` is explicitly added to your project dependencies.

### The `StoreInitializer` Pattern for Safe Hydration

You often need to initialize a client-side Zustand store with data from the server (e.g., auth status, user theme). The `StoreInitializer` pattern is the safest way to do this without causing UI flicker or hydration errors.

**1. Create the Initializer Component**
This is a client component that does one job: set the store's initial state on its first render. It renders `null`.

```typescript
// src/stores/auth-store-initializer.tsx
'use client'

import { useRef } from 'react'
import { useAuthStore } from './auth-store-provider' // Your custom hook
import type { User } from '@/lib/auth/types'

function AuthStoreInitializer({ user }: { user: User | null }) {
  const initialized = useRef(false)
  if (!initialized.current) {
    // Use setState directly from the store hook on the first render
    useAuthStore.setState({ user, initialized: true })
    initialized.current = true
  }
  return null // This component renders nothing
}

export default AuthStoreInitializer
```

**2. Use it in a Server Component Layout**
Fetch the data in a Server Component (`layout.tsx`) and pass it to the initializer.

```typescript
// src/app/(dashboard)/layout.tsx
import { getCurrentUser } from '@/lib/auth/session'
import AuthStoreInitializer from '@/stores/auth-store-initializer'
import { AuthStoreProvider } from '@/stores/auth-store-provider'

export default async function DashboardLayout({ children }) {
  const user = await getCurrentUser() // Fetches user from secure cookie on the server

  return (
    <AuthStoreProvider>
      {/* The initializer runs first, hydrating the store before other client components render */}
      <AuthStoreInitializer user={user} />
      <main>{children}</main>
    </AuthStoreProvider>
  )
}
```

### ✅ DO: Separate Server Cache State from UI State

A common scenario is managing UI state related to server data (e.g., a list of selected product IDs). Don't mix this UI state into your TanStack Query cache. Keep them separate.

*   **TanStack Query:** Manages the cache of the product list itself.
*   **Zustand:** Manages a `Set<string>` of `selectedProductIds`.

This separation of concerns keeps both state managers clean and predictable.

### Avoiding Unnecessary Re-renders via `useShallow`

When you do:

```typescript
const user = useUIStore((store) => store.user);
```

Your component re-renders if the object `store.user` changes reference, even if it's the same data. Sometimes that's a problem. Zustand offers `useShallow`:

```typescript
import { useShallow } from "zustand/react/shallow";

const userInfo = useUIStore(
  useShallow((store) => ({ 
    name: store.user.name, 
    age: store.user.age 
  }))
);
// Re-renders only if name or age changes
```

You can keep your slices returning objects while skipping re-renders if the actual fields remain the same.

---

## 5. Secure Authentication Pattern

When using an external backend, the Next.js server must act as a **Backend-for-Frontend (BFF)**. The client should **never** handle raw authentication tokens.

### ✅ DO: Use `httpOnly` Cookies Brokered by Server Actions

This pattern prevents tokens from being stolen via XSS attacks.

**1. Login via a Server Action**
The action calls your backend, receives a JWT, and stores it in a secure `httpOnly` cookie.

```typescript
// src/app/(auth)/login/_actions.ts
'use server'
import { cookies } from 'next/headers'
import { redirect } from 'next/navigation'
import { apiClient } from '@/lib/api/client'

export async function login(formData: FormData) {
  try {
    const response = await apiClient.POST('/auth/login', { body: formData });
    const token = response.data?.access_token;

    if (!token) throw new Error('No token received');
    
    cookies().set('auth_token', token, {
      httpOnly: true, // Prevents client-side JS access
      secure: process.env.NODE_ENV === 'production',
      sameSite: 'strict',
      path: '/',
    });

    redirect('/dashboard');
  } catch (error) {
    return { error: 'Invalid credentials.' };
  }
}
```

**2. Protect Routes with Middleware**
Middleware provides the first line of defense by checking for the presence of the auth cookie.

```typescript
// src/middleware.ts
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export function middleware(request: NextRequest) {
  const token = request.cookies.get('auth_token');
  const { pathname } = request.nextUrl;

  // Redirect to login if trying to access a protected route without a token
  if (!token && pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
  
  // Add redundant checks in Server Components for true security
  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*', '/login'],
}
```

### Security Note (CVE-2025-29927)
Ensure your Next.js version is **15.2.3 or higher**. Do not rely solely on middleware for auth; always add redundant, session-validating checks in your sensitive Server Components and Server Actions.

> **Note**: As of Next.js 15 + React 19, `cookies()`, `headers()` and similar functions are **async-only**. Any legacy synchronous calls will break. Always use `await`:

```typescript
// Before (Next.js 14)
const cookieStore = cookies()
const token = cookieStore.get('auth_token')

// After (Next.js 15)
const cookieStore = await cookies()
const token = cookieStore.get('auth_token')
```

---

## 6. End-to-End Type Safety with OpenAPI

Automate your data access layer by generating a type-safe client directly from your backend's OpenAPI schema. This creates a compile-time contract, eliminating an entire class of integration bugs.

### ✅ DO: Automate API Client Generation

**1. Add a Generation Script**
Use a modern tool like `@hey-api/openapi-ts` or `openapi-typescript` with `openapi-fetch`.

```json
// package.json
{
  "scripts": {
    "generate:api": "bunx @hey-api/openapi-ts -i http://localhost:8000/openapi.json -o src/lib/api/generated -c fetch"
  }
}
```

**2. Create a Centralized, Type-Safe API Wrapper**
This wrapper injects the auth token from the `httpOnly` cookie on the server.

```typescript
// src/lib/api/client.ts
import createClient from 'openapi-fetch'
import { cookies } from 'next/headers'
import type { paths } from './generated' // Types from the generated client

export const apiClient = createClient<paths>({
  baseUrl: process.env.NEXT_PUBLIC_API_URL,
  headers: {
    // This function runs on the server for every request
    Authorization: () => {
      const token = cookies().get('auth_token')?.value;
      return token ? `Bearer ${token}` : undefined;
    },
  },
});
```
Now, any call from a Server Component, like `await apiClient.GET('/users/{id}', { params: { path: { id: 123 } } })`, is fully type-safe and automatically authenticated.

### Enhanced Type Safety with Modern OpenAPI Tools

#### Using orval 8.0 for TanStack Query Integration
Orval can output both fetch clients and TanStack Query hooks out-of-the-box:

```json
// package.json
{
  "scripts": {
    "generate:api": "orval --config orval.config.js"
  }
}
```

#### CI/CD Integration with Speakeasy
Speakeasy-CLI can auto-check breaking changes between OpenAPI revisions during CI:

```bash
# In your CI pipeline
speakeasy diff --schema-old previous-openapi.json --schema-new current-openapi.json
```

## Addendum: Advanced Production Patterns

The main guide provides the core architecture for most projects. The following patterns address more advanced scenarios that arise as applications scale in complexity and team size.

### 7. Advanced Caching: Beyond `fetch` with `unstable_cache`

Server Components often need to fetch data from sources other than HTTP endpoints, such as a direct database query with an ORM. The standard `fetch` caching mechanism doesn't apply here. For these cases, use `unstable_cache` from `next/cache` to bring the same powerful caching and revalidation semantics to any function.

#### ✅ DO: Wrap Database Queries and other async functions with `unstable_cache`

This ensures consistent caching behavior across your entire data layer, whether from an API or a database.

```typescript
// src/lib/data/users.ts
import { unstable_cache } from 'next/cache';
import { db } from '@/lib/db'; // Your database client (e.g., Drizzle, Prisma)

// This function is now cached and can be revalidated by tag
export const getUserById = unstable_cache(
  async (userId: string) => {
    // This expensive database query will only run when the cache is empty or stale
    return db.query.users.findFirst({ where: (users, { eq }) => eq(users.id, userId) });
  },
  ['users'], // A base key for the cache entry
  {
    // We can use the same revalidation strategies as fetch
    tags: ['users', `user:${userId}`], 
    revalidate: 3600, // Optional: time-based revalidation (1 hour)
  }
);
```
Now, you can call `revalidateTag('user:123')` in a Server Action to invalidate this specific user's data from the cache.

### 8. Background Processing with the `after()` API

Next.js 15 introduces the stable `after()` API for executing code after the response has finished streaming:

```typescript
// app/api/webhook/route.ts
import { after } from 'next/server'
import { NextResponse } from 'next/server'

export async function POST(request: Request) {
  const data = await request.json()
  
  // Primary task - respond immediately
  const response = NextResponse.json({ received: true })
  
  // Secondary tasks - run after response is sent
  after(async () => {
    // These won't block the response
    await logToAnalytics(data)
    await syncWithExternalSystem(data)
    await sendNotifications(data)
  })
  
  return response
}
```

This is particularly useful for:
- Analytics and logging
- Cache warming
- Data synchronization
- Notification sending

### 9. Client Router Cache Controls

Add control over client-side navigation caching to avoid unnecessary refetches:

```typescript
// next.config.ts
const nextConfig: NextConfig = {
  experimental: {
    staleTimes: {
      dynamic: 30,    // 30 seconds for dynamic segments
      static: 180,    // 180 seconds for static segments
    },
  },
}
```

This is especially useful for list-detail flows where you navigate back and forth frequently.

### 10. Enhanced Observability

#### The `onRequestError` Hook
Next.js 15 provides a new hook for comprehensive error tracking:

```typescript
// instrumentation.ts
export async function onRequestError(
  error: Error,
  request: Request,
  context: {
    routerKind: 'Pages Router' | 'App Router'
    routePath: string
    routeType: 'render' | 'route' | 'action' | 'middleware'
    renderSource: 'react-server-components' | 'react-server-components-payload' | 'server-rendering'
    revalidateReason: 'on-demand' | 'stale' | undefined
    renderType: 'dynamic' | 'dynamic-resume' | 'static' | 'static-bail'
  }
) {
  // Send to your observability platform
  await fetch('https://monitoring.example.com/errors', {
    method: 'POST',
    body: JSON.stringify({
      message: error.message,
      stack: error.stack,
      url: request.url,
      context,
      timestamp: new Date().toISOString(),
    }),
    headers: { 'Content-Type': 'application/json' },
  })
}

export async function register() {
  // Initialize your observability SDKs
  if (process.env.NEXT_RUNTIME === 'nodejs') {
    await import('./instrumentation.node')
  }
}
```

### 11. Edge Runtime Best Practices

#### When to Use Edge Runtime
Choose Edge Runtime for:
- Global low-latency requirements
- Simple computational tasks
- Middleware and authentication
- API routes with minimal dependencies

```typescript
// app/api/geo/route.ts
export const runtime = 'edge' // Opt into Edge Runtime

export async function GET(request: Request) {
  // Edge Runtime provides geo information
  const { geo } = request as any
  
  return Response.json({
    country: geo?.country,
    city: geo?.city,
    region: geo?.region,
  })
}
```

#### Edge Runtime Limitations
- 1-4MB code size limit (varies by platform)
- No Node.js APIs (fs, crypto, etc.)
- Limited npm package compatibility
- No localStorage/sessionStorage

### 12. Performance Monitoring

#### Core Web Vitals Tracking
Implement comprehensive performance monitoring:

```typescript
// app/components/web-vitals.tsx
'use client'

import { useReportWebVitals } from 'next/web-vitals'

export function WebVitals() {
  useReportWebVitals((metric) => {
    // Send to analytics
    window.gtag?.('event', metric.name, {
      value: Math.round(metric.name === 'CLS' ? metric.value * 1000 : metric.value),
      event_label: metric.id,
      non_interaction: true,
    })
    
    // Log to console in development
    if (process.env.NODE_ENV === 'development') {
      console.log(metric)
    }
  })
  
  return null
}
```

### 13. React Compiler Integration

The React Compiler can automatically optimize your components, reducing the need for manual memoization:

```bash
bun install --save-dev @react/compiler-plugin
```

With the compiler enabled (via the config shown at the top), you can write simpler code:

```typescript
// Before: Manual optimization needed
const ExpensiveComponent = memo(({ data }) => {
  const processedData = useMemo(() => processData(data), [data])
  const handleClick = useCallback(() => {}, [])
  return <div>{/* ... */}</div>
})

// After: Compiler handles optimization
function ExpensiveComponent({ data }) {
  const processedData = processData(data) // Automatically memoized
  const handleClick = () => {} // Automatically stable
  return <div>{/* ... */}</div>
}
```

### 14. End-to-End Type Safety: Optimizing the Full Stack

Your generated API client is only as good as the OpenAPI schema it's based on.

#### ✅ DO: Advise your Backend Team to Enrich the Schema

Small changes on the backend (e.g., in FastAPI) can dramatically improve the generated client.

*   **Use `tags`:** `tags=["Users"]` in a path operation will group related functions into a `UsersService` in the client.
*   **Customize `operationId`:** Provide a custom function to create cleaner method names (e.g., `Users_GetAll` instead of `get_all_users_api_v1_users_get`).

#### ✅ DO: Fully Automate Client Generation

Don't rely on manually running `bun run generate:api`. In a monorepo (with Turborepo or Nx), configure the build process to automatically regenerate the API client whenever the backend's `openapi.json` file changes. This creates a truly seamless, unbreakable contract between frontend and backend.

### 15. A More Scalable Component Architecture

For larger projects, a more granular component folder structure prevents code from becoming disorganized. The key is to distinguish between universal primitives (`ui`), application structure (`layout`), and domain-specific functionality (`features`).

*   **`ui/`**: Atomic, reusable design system primitives (Button, Input, Card). Style-focused, no business logic.
*   **`layout/`**: Structural "chrome" of the app (Header, Footer, Sidebar).
*   **`features/`**: Complex components for specific business domains (ProductSearch, CheckoutForm, DashboardWidgetGrid). These often combine `ui` and `layout` components to deliver a complete piece of functionality.

```
/src/components
├── ui/                   # Design system primitives
│   ├── button.tsx
│   ├── card.tsx
│   └── input.tsx
├── layout/              # App structure components
│   ├── header.tsx
│   ├── footer.tsx
│   └── sidebar.tsx
└── features/            # Domain-specific components
    ├── product-search/
    ├── checkout-form/
    └── dashboard-widgets/
```
