# Mentor AI — Agent Rules & Constraints

## 1. Development Commands

### Root Commands (run from project root)
```bash
# Development
pnpm dev                    # Start all apps in parallel
pnpm build                  # Build all packages
pnpm build:force            # Force build all packages
pnpm build:watch            # Build in watch mode
pnpm build:clean            # Clean build artifacts

# Quality & Testing
pnpm lint                   # Lint all packages recursively
pnpm typecheck             # Type check all packages
pnpm test                  # Run all tests recursively
pnpm clean                 # Clean all build artifacts
```

### App-Specific Commands

#### Web App (apps/web)
```bash
pnpm --filter @mentor-ai/web dev            # Start development server
pnpm --filter @mentor-ai/web build          # Build for production
pnpm --filter @mentor-ai/web start          # Start production server
pnpm --filter @mentor-ai/web lint           # Lint web code
pnpm --filter @mentor-ai/web typecheck      # Type check web code
pnpm --filter @mentor-ai/web format         # Format with Prettier
pnpm --filter @mentor-ai/web format:check   # Check formatting
pnpm --filter @mentor-ai/web knip           # Remove unused code/files
```

#### API (apps/api)
```bash
pnpm --filter @mentor-ai/api dev            # Start API server with hot reload
pnpm --filter @mentor-ai/api build          # Build API
pnpm --filter @mentor-ai/api start          # Start production server
pnpm --filter @mentor-ai/api lint           # Lint API code
pnpm --filter @mentor-ai/api typecheck      # Type check API code
pnpm --filter @mentor-ai/api route:list     # List all API endpoints
```

#### Packages
```bash
pnpm --filter @mentor-ai/types typecheck    # Type check types package
pnpm --filter @mentor-ai/utils typecheck   # Type check utils package
pnpm --filter @mentor-ai/database typecheck # Type check database package
```

---

## 2. Code Style Guidelines

### Import Rules
* **Type-only imports**: Use `import type` for TypeScript types
* **No duplicate imports**: Import everything from a module once
* **Order**: External libraries → Internal packages → Local modules
* **Absolute imports**: Use workspace package names (`@mentor-ai/types`)

```typescript
// ✅ Correct
import type { User } from '@mentor-ai/types'
import { z } from 'zod'
import { userService } from '../services/user.service'

// ❌ Wrong
import { User } from '@mentor-ai/types'
import { z } from 'zod'
import { z } from 'zod' // duplicate
```

### TypeScript Rules
* **Strict mode**: Always enabled, no `any` types
* **Unused variables**: Prefix with `_` if intentionally unused
* **Type annotations**: Use for function returns and complex objects
* **Runtime validation**: Use Zod schemas for all external data

### Naming Conventions
* **Files**: kebab-case (e.g., `user.service.ts`, `auth.controller.ts`)
* **Variables**: camelCase
* **Constants**: UPPER_SNAKE_CASE
* **Types/Interfaces**: PascalCase
* **Database models**: PascalCase (Prisma convention)
* **Components**: PascalCase (e.g., `UserProfile.tsx`)

### Formatting
* **Prettier** for code formatting (apps/web has built-in config)
* **Prettier plugins**: @trivago/prettier-plugin-sort-imports, prettier-plugin-tailwindcss
* **Run format**: `pnpm --filter @mentor-ai/web format`
* **Check format**: `pnpm --filter @mentor-ai/web format:check`

---

## 3. Project Structure

### Required Structure
```
apps/
  web/        # React + Vite + TypeScript frontend
  api/        # Express.js backend

packages/
  types/      # Shared TypeScript types & Zod schemas
  utils/      # Shared utilities
  database/   # Prisma schema & client
```

### File Structure Patterns (Backend)
* **Controllers**: `*.controller.ts` - Handle HTTP requests
* **Services**: `*.service.ts` - Business logic
* **Middleware**: `*.middleware.ts` - Express middleware
* **Routes**: `*.routes.ts` - Route definitions
* **Schemas**: `*.schema.ts` - Zod validation schemas

---

## 4. Architecture Rules

### Backend (apps/api)
* **Express.js** mandatory
* **Stateless** design
* **Controller → Service → Domain** separation
* **Prisma** for all database operations
* **Zod** for input validation
* **AI SDK**: Langchain (`Langchain.js` package) for LLM interactions

### Frontend (apps/web)
* **React 19 + Vite + TypeScript** only
* **shadcn/ui** component system (Radix UI primitives)
* **Tailwind CSS v4** mandatory
* **TanStack Query** for server state
* **TanStack Router** for routing
* **Zustand** for client state
* **No business logic** in components - use hooks/services

### Database
* **PostgreSQL** only
* **Prisma ORM** mandatory
* **Migrations** versioned in `packages/database/prisma/migrations`
* **Client** imported from `@mentor-ai/database` only

---

## 5. AI/LLM Rules

* **LLM as reasoning engine**, not decision-maker
* **Langchain SDK** (`langchain` package) mandatory
* **Provider-agnostic** - support multiple providers (OpenAI, Google)
* **Backend controls** all AI interactions - frontend never calls LLM directly

---

## 6. Security Rules

* **JWT authentication** mandatory
* **Role-based access control** (RBAC)
* **Rate limiting** on all APIs (express-rate-limit)
* **No secrets** in frontend code
* **Input validation** with Zod
* **Helmet.js** for HTTP security headers
* **CORS** configured properly

---

## 7. Quality Rules

* **ESLint + Prettier** mandatory
* **Type checking** before build (`pnpm typecheck`)
* **knip** for detecting unused code, files, dependencies
* **No unused dependencies**
* **No dead code**
* **No commented-out logic**

---

## 8. Testing Guidelines

* **Test framework**: Not yet configured (currently no test files)
* **When adding tests**: Use Vitest for both frontend and backend
* **Test location**: `*.test.ts` or `*.spec.ts` alongside source files
* **Run tests**:
  - All: `pnpm test`
  - Web: `pnpm --filter @mentor-ai/web test`
  - API: `pnpm --filter @mentor-ai/api test`

---

## 9. Guiding Principle

> **The backend owns truth. The AI advises. The system remains deterministic.**

---

## 10. Common Tasks

### Adding a new package
1. Create folder in `packages/`
2. Add `package.json` with `workspace:*` dependencies
3. Add to `pnpm-workspace.yaml` if needed
4. Run `pnpm install`

### Adding a new API endpoint
1. Create controller in `apps/api/src/controllers/`
2. Create service in `apps/api/src/services/`
3. Create routes in `apps/api/src/routes/`
4. Register routes in `apps/api/src/index.ts`
5. Add Zod schema for validation

### Adding a new frontend page
1. Create route in `apps/web/src/routes/`
2. Create component in `apps/web/src/components/` or `apps/web/src/features/`
3. Use TanStack Router for routing
4. Use shadcn/ui components from `@/components/ui/`
5. Follow color schema from the `apps/web/src/styles/index.css`

### Nested Routes with Layouts (TanStack Router)
When a parent route needs children (e.g. `/admin/page1`, `/admin/page2`), the parent **must** be a layout route that renders `<Outlet />`.

**Pattern:**
```
routes/_authenticated/my-section/
  route.tsx          # Layout with auth guard + <Outlet />
  index.tsx          # Default content at /my-section
  child1.tsx         # Content at /my-section/child1
  child2.tsx         # Content at /my-section/child2
```

**Example:** `help-center/admin` uses this pattern:
- `admin/route.tsx` — layout with `beforeLoad` admin guard + `<Outlet />`
- `admin/index.tsx` — User Queries page (default)
- `admin/faqs.tsx` — FAQ Management page

Never create a child route like `admin.faqs.tsx` alongside `admin.tsx` if `admin.tsx` does not render `<Outlet />`. The child will match but never render.

---

## 11. Agent Modules (.agents folder)

### Structure
```
.agents/
  <module-name>/
    README.md       # Module documentation
```

### Root Agent File
Each module should have a corresponding entry in the root `AGENTS.md` file:

```markdown
### <Module Name>
- **Path**: `.agents/<module-name>/`
- **Purpose**: Brief description of the module
- **Context**: Link to `.agents/<module-name>/README.md`
```

### Creating a New Module
1. Create folder `.agents/<module-name>/`
2. Add `README.md` with module documentation
3. Add entry to this file under "Agent Modules" section

---

### AI Guidance Module
- **Path**: `.agents/ai-guidance/`
- **Purpose**: Provides context and rules for LLM interactions and AI-powered features
- **Context**: [`.agents/ai-guidance/README.md`](.agents/ai-guidance/README.md)

### LLM Provider Management Module
- **Path**: `.agents/llm-management/`
- **Purpose**: Admin-configurable AI model provider management with encrypted API keys at rest
- **Context**: [`.agents/llm-management/README.md`](.agents/llm-management/README.md)

---

## 12. Existing Features Reference

### FAQ Management (Admin CRUD)
Full CRUD for FAQs, admin-only access. Public read, admin write.

**Backend Endpoints** (`apps/api/src/routes/support.routes.ts`):
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| GET | `/api/support/faqs` | Public | List active FAQs |
| GET | `/api/support/faqs/categories` | Public | List FAQ categories |
| POST | `/api/support/admin/faqs` | Admin | Create FAQ |
| GET | `/api/support/admin/faqs/:faqId` | Admin | Get single FAQ |
| PATCH | `/api/support/admin/faqs/:faqId` | Admin | Update FAQ |
| DELETE | `/api/support/admin/faqs/:faqId` | Admin | Delete FAQ |

**Backend Files:**
- Controller: `apps/api/src/controllers/support.controller.ts` (`createFAQ`, `getFAQById`, `updateFAQ`, `deleteFAQ`)
- Service: `apps/api/src/services/support.service.ts` (`createFAQ`, `getFAQById`, `updateFAQ`, `deleteFAQ`)
- Schemas: `apps/api/src/schemas/support.schema.ts` (`createFAQSchema`, `updateFAQSchema`, `faqIdParamsSchema`)

**Frontend Files:**
- API Client: `apps/web/src/lib/apis/support.ts` (`supportClient` with FAQ + ticket methods)
- Hooks: `apps/web/src/hooks/use-support.ts` (`useFAQs`, `useCreateFAQ`, `useUpdateFAQ`, `useDeleteFAQ`)
- Admin Page: `apps/web/src/features/help-center/faq-admin-page.tsx`
- Route: `apps/web/src/routes/_authenticated/help-center/admin/faqs.tsx`

**Route Structure** (nested layout pattern):
```
routes/_authenticated/help-center/
  admin/
    route.tsx      # Layout: auth guard + <Outlet />
    index.tsx      # /help-center/admin → User Queries
    faqs.tsx       # /help-center/admin/faqs → FAQ Management
```

**Sidebar Link** (`apps/web/src/components/layout/data/sidebar-data.ts`):
```typescript
{
  title: 'FAQs',
  url: '/help-center/admin/faqs',
  icon: CircleHelp,
}
```

**Seeder** (`packages/database/seeder/faqs/FAQSeeder.ts`):
```bash
pnpm --filter @mentor-ai/database db:seed --seeder=FAQSeeder
```

### Support Tickets
Public users create tickets. Admins view and respond.

**Backend Endpoints** (`apps/api/src/routes/support.routes.ts`):
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| GET | `/api/support/tickets` | User | My tickets |
| POST | `/api/support/tickets` | User | Create ticket |
| GET | `/api/support/tickets/:ticketId` | User | Ticket detail |
| GET | `/api/support/admin/tickets` | Admin | All tickets |
| PATCH | `/api/support/admin/tickets/:ticketId` | Admin | Update ticket |

**Frontend Files:**
- API Client: `apps/web/src/lib/apis/support.ts`
- Hooks: `apps/web/src/hooks/use-support.ts` (`useUserTickets`, `useAllTickets`, `useCreateTicket`, `useUpdateTicket`)
- Admin Page: `apps/web/src/features/help-center/help-center-admin-page.tsx`
- Route: `apps/web/src/routes/_authenticated/help-center/admin/index.tsx`


## 13. Tailwind CSS Color Schema Rule

### Source of Truth
All colors used in the frontend (`apps/web`) **must** come from the design system defined in `apps/web/src/styles/index.css`. This file registers a Tailwind CSS v4 `@theme` block that maps semantic color names to CSS custom properties.

### Allowed Colors
Use **only** these theme tokens. They automatically adapt to light/dark mode via the CSS variables.

| Token | Usage |
|-------|-------|
| `background` | Page / surface background |
| `foreground` | Primary text color |
| `card` / `card-foreground` | Card surfaces and text |
| `popover` / `popover-foreground` | Dropdown / popover surfaces |
| `primary` / `primary-foreground` | Main buttons, active states, links |
| `secondary` / `secondary-foreground` | Secondary buttons, badges |
| `muted` / `muted-foreground` | Subtle backgrounds, helper text |
| `accent` / `accent-foreground` | Highlights, hovered items |
| `destructive` / `destructive-foreground` | Errors, dangerous actions |
| `border` | Dividers, input borders |
| `input` | Form field backgrounds |
| `ring` | Focus rings |
| `chart-1` … `chart-5` | Data visualization |
| `sidebar` / `sidebar-foreground` | Sidebar background / text |
| `sidebar-primary` / `sidebar-primary-foreground` | Sidebar active items |
| `sidebar-accent` / `sidebar-accent-foreground` | Sidebar hovered items |
| `sidebar-border` / `sidebar-ring` | Sidebar borders / focus |

### Rules
1. **Never use static Tailwind colors** such as `bg-blue-500`, `text-red-600`, `border-gray-300`, `bg-white`, `text-black`, etc., unless explicitly instructed otherwise by the user.
2. **Never hard-code hex, rgb, hsl, or oklch values** in class names (e.g., `bg-[#ff0000]`, `text-[oklch(...)]`) unless the user explicitly requests a custom color.
3. **Always prefer semantic tokens**: `bg-primary`, `text-muted-foreground`, `border-border`, `bg-destructive`, etc.
4. **Opacity modifiers are allowed** on theme tokens: `bg-primary/10`, `text-foreground/50`, `ring-border/20`.
5. **Gradient utilities** should use theme tokens: `bg-gradient-to-r from-primary to-secondary`.
6. **Custom inline styles** (`style={{ color: '...' }}`) must also use CSS variables where possible: `style={{ color: 'var(--primary)' }}`.
7. **Rare exceptions** (e.g., a one-off marketing banner with a brand color) require explicit user approval.

### Reference
See the full theme definition in `apps/web/src/styles/index.css`.
