# JIBUILD Workspace

## Overview

JIBUILD is a full-stack Youth-Driven Economic Intelligence, Skills & Funding Hub built for Kenya. Youth can search economic opportunities by county/sector, learn skills, form Chamas (groups), collaborate, track milestones, and connect with investors/NGOs/donors for funding and mentorship.

**Funding & Partnership Module** includes: funding applications with multi-step approval workflow, investor portal with approve/reject/release-tranche controls, mentorship matching, milestone-linked fund releases (3-tranche system), and in-app notifications.

**AI Economic Intelligence Engine** (v2): Powered by Replit AI Integrations (OpenAI gpt-5.2, no API key needed). Three streaming SSE endpoints:
- `POST /api/analysis/ai-search` — Generates full market analysis (county + sector specific)
- `POST /api/analysis/simulate` — 12-month business outcome simulator with financial projections
- `POST /api/analysis/chat` — Context-aware follow-up AI advisor chat
Frontend: 7-tab analysis page with streaming indicators, area charts, bar charts, contact cards, execution plan timeline, and integrated AI chat.

**Note**: GitHub integration was dismissed by user. If needed in the future, use the GitHub connector (connector:ccfg_github_01K4B9XD3VRVD2F99YM91YTCAF) or store a Personal Access Token as a secret.

## Stack

- **Monorepo tool**: pnpm workspaces
- **Node.js version**: 24
- **Package manager**: pnpm
- **TypeScript version**: 5.9
- **Frontend**: React + Vite + Tailwind CSS + shadcn/ui + Recharts + Framer Motion
- **API framework**: Express 5
- **Database**: PostgreSQL + Drizzle ORM
- **Validation**: Zod (`zod/v4`), `drizzle-zod`
- **API codegen**: Orval (from OpenAPI spec)
- **Build**: esbuild (CJS bundle)

## Structure

```text
artifacts-monorepo/
├── artifacts/
│   ├── jibuild/            # React + Vite frontend (served at /)
│   └── api-server/         # Express API server (served at /api)
├── lib/
│   ├── api-spec/           # OpenAPI spec + Orval codegen config
│   ├── api-client-react/   # Generated React Query hooks
│   ├── api-zod/            # Generated Zod schemas from OpenAPI
│   └── db/                 # Drizzle ORM schema + DB connection
├── scripts/                # Utility scripts
└── pnpm-workspace.yaml
```

## Features

### 1. AI Economic Analysis (`/api/analysis`)
- POST `/api/analysis/search` — generates AI-driven market gap analysis for any Kenyan county + sector
- GET `/api/analysis/counties` — all 20 Kenyan counties
- GET `/api/analysis/sectors` — 12 economic sectors
- Outputs: market gaps, ranked opportunities, contacts (brokers/suppliers/mentors/NGOs), 12-month trend data, risk factors, funding suggestions, skill recommendations

### 2. Skills Learning Hub (`/api/skills`)
- 5 skill modules: Farming, Bricklaying, Entrepreneurship, Bookkeeping, Digital Marketing
- Each module has lessons + quiz
- Completing a quiz issues a digital certificate and awards 50 points

### 3. Chamas Module (`/api/chamas`)
- Create chamas (groups) with county + sector focus
- Member roles: Chairman, Secretary, Treasurer, Member
- Real-time chat (polling every 5s)
- Milestone tracker: started → funded → revenue → growth
- Track funding received per chama

### 4. Funding Portal (`/api/funding`)
- 6 pre-seeded funding opportunities (YEDF, Uwezo Fund, FSD Kenya, Equity Bank, KCIC, African Youth Impact Fund)
- Donors/investors can fund any chama with amount + purpose + optional mentorship offer

### 5. Users & Profiles (`/api/users`)
- Simple onboarding: name + county + role (no password)
- User state stored in localStorage + React context
- Profile shows: completed skills, chama memberships, certificates, total funding, leaderboard rank

### 6. Gamification (`/api/leaderboard`, `/api/certificates`)
- Points awarded for skill completions (50 pts each)
- Certificates issued for skills and milestones
- Global leaderboard ranked by points

## Database Schema

- `users` — name, email, county, role, points, badges
- `skill_modules` — module metadata + lessons + quiz (JSON)
- `skill_completions` — user skill completions
- `chamas` — group name, county, sector, funding total, milestone status
- `chama_members` — role-based membership
- `chama_messages` — chat messages
- `chama_milestones` — 4-stage milestone tracker
- `funding_opportunities` — seeded investor/NGO/government programs
- `donations` — donations to chamas
- `certificates` — digital certificates issued

## Pages

- **Onboarding** — name/county/role entry (localStorage-based auth)
- **Home** — hero + stats + quick actions
- **Analyze Economy** — county+sector search → rich AI analysis output with charts
- **Skills Hub** — module grid → lesson viewer → quiz → certificate
- **Chamas** — list + create + detail with chat + milestones + funding
- **Funding Portal** — investor/NGO listings + "Fund a Chama" modal
- **Leaderboard** — top users by points + certificate gallery

## API Codegen

Run after any OpenAPI spec change:
```bash
pnpm --filter @workspace/api-spec run codegen
```

## Database Migrations

Push schema changes in development:
```bash
pnpm --filter @workspace/db run push
```
