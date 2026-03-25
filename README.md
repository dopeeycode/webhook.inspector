<p align="center">
  <img src="https://img.shields.io/badge/status-in%20development-yellow?style=for-the-badge" alt="Status: In Development" />
  <img src="https://img.shields.io/badge/license-ISC-blue?style=for-the-badge" alt="License: ISC" />
</p>

# 🔍 Webhook Inspector

A fullstack application for **capturing, storing, and inspecting webhook requests** in real-time. Built as part of the [Rocketseat](https://rocketseat.com.br/) Fullstack with AI challenge.

> Simulate and inspect incoming webhook payloads (e.g., Stripe events) through a clean web dashboard backed by a performant API.

---

## 📐 Architecture

This project is a **pnpm monorepo** organized into two workspaces:

```
desafio-fullstack-com-ia/
├── api/          → Backend (Fastify + Drizzle ORM + PostgreSQL)
├── web/          → Frontend (React + Vite + TypeScript)
├── pnpm-workspace.yaml
└── package.json
```

| Layer       | Stack                                                    |
| ----------- | -------------------------------------------------------- |
| **API**     | Fastify · Drizzle ORM · Zod · PostgreSQL · Swagger/Scalar |
| **Web**     | React 19 · Vite 8 · TypeScript                          |
| **Infra**   | Docker Compose (PostgreSQL 17) · pnpm workspaces         |
| **Tooling** | Biome (format + lint) · tsx · @faker-js/faker (seed)     |

---

## ✅ Prerequisites

Make sure you have the following installed before getting started:

- [Node.js](https://nodejs.org/) **v20+**
- [pnpm](https://pnpm.io/) **v10+**
- [Docker](https://www.docker.com/) & [Docker Compose](https://docs.docker.com/compose/)

---

## 🚀 Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/webhook-inspector.git
cd webhook-inspector
```

### 2. Install dependencies

```bash
pnpm install
```

### 3. Start the database

```bash
docker compose -f api/docker-compose.yml up -d
```

This starts a **PostgreSQL 17** container with the following defaults:

| Variable            | Value    |
| ------------------- | -------- |
| `POSTGRES_USER`     | `docker` |
| `POSTGRES_PASSWORD` | `docker` |
| `POSTGRES_DB`       | `webhooks` |
| Port                | `5432`   |

### 4. Configure environment variables

Create a `.env` file inside the `api/` directory (one is already provided):

```env
DATABASE_URL="postgresql://docker:docker@localhost:5432/webhooks"
```

> The API also reads `NODE_ENV` (default: `development`) and `PORT` (default: `3333`) from environment.

### 5. Run database migrations

```bash
cd api
pnpm db:generate
pnpm db:migrate
```

### 6. Seed the database (optional)

Populate the database with **60 fake Stripe webhook events** for testing:

```bash
pnpm db:seed
```

### 7. Start the development servers

**API** (runs on `http://localhost:3333`):

```bash
# from the api/ directory
pnpm dev
```

**Web** (runs on `http://localhost:5173`):

```bash
# from the web/ directory
pnpm dev
```

---

## 📖 API Documentation

Once the API is running, interactive documentation is available via **Scalar**:

```
http://localhost:3333/docs
```

### Available Endpoints

| Method | Route            | Description           |
| ------ | ---------------- | --------------------- |
| `GET`  | `/api/webhook`   | List webhook requests |

#### Query Parameters

| Param   | Type     | Default | Description                      |
| ------- | -------- | ------- | -------------------------------- |
| `limit` | `number` | `20`    | Number of results (min 1, max 100) |

---

## 🗄️ Database Schema

The `webhooks` table stores captured webhook requests:

| Column           | Type        | Description                       |
| ---------------- | ----------- | --------------------------------- |
| `id`             | `text` (PK) | UUIDv7, auto-generated           |
| `method`         | `text`      | HTTP method (POST, GET, etc.)    |
| `pathname`       | `text`      | Request path                     |
| `ip`             | `text`      | Source IP address                |
| `status_code`    | `integer`   | Response status code (default 200)|
| `content_type`   | `text`      | Content-Type header              |
| `content_length` | `integer`   | Body size in bytes               |
| `query_params`   | `jsonb`     | URL query parameters             |
| `headers`        | `jsonb`     | Request headers                  |
| `body`           | `text`      | Raw request body                 |
| `created_at`     | `timestamp` | When the request was received    |

---

## 🛠️ Available Scripts

### Root

```bash
pnpm install          # Install all workspace dependencies
```

### API (`api/`)

```bash
pnpm dev              # Start dev server with hot-reload
pnpm start            # Start production server
pnpm format           # Format code with Biome
pnpm db:generate      # Generate Drizzle migrations
pnpm db:migrate       # Run pending migrations
pnpm db:studio        # Open Drizzle Studio (DB GUI)
pnpm db:seed          # Seed database with fake data
```

### Web (`web/`)

```bash
pnpm dev              # Start Vite dev server
pnpm build            # Build for production
pnpm preview          # Preview production build
```

---

## 📁 Project Structure

```
api/
├── src/
│   ├── db/
│   │   ├── schema/
│   │   │   ├── index.ts         # Schema barrel export
│   │   │   └── webhooks.ts      # Webhooks table definition
│   │   ├── migrations/          # Drizzle auto-generated migrations
│   │   ├── index.ts             # Database connection
│   │   └── seed.ts              # Faker-powered seed script
│   ├── routes/
│   │   └── list-webhooks.ts     # GET /api/webhook
│   ├── env.ts                   # Zod environment validation
│   └── server.ts                # Fastify app bootstrap
├── biome.json                   # Biome config (lint + format)
├── docker-compose.yml           # PostgreSQL container
├── drizzle.config.ts            # Drizzle Kit config
└── tsconfig.json

web/
├── src/
│   ├── app.tsx                  # Root component
│   └── main.tsx                 # React entry point
├── index.html
├── vite.config.ts
└── tsconfig.json
```

---

## 🧰 Tech Stack Details

- **[Fastify](https://fastify.dev/)** — High-performance Node.js web framework
- **[Drizzle ORM](https://orm.drizzle.team/)** — Type-safe SQL ORM with migration toolkit
- **[Zod](https://zod.dev/)** — Schema validation for request/response and env vars
- **[Scalar](https://scalar.com/)** — Beautiful API reference docs from OpenAPI specs
- **[React 19](https://react.dev/)** — UI library for building the dashboard
- **[Vite 8](https://vite.dev/)** — Lightning-fast frontend build tool
- **[Biome](https://biomejs.dev/)** — All-in-one linter and formatter
- **[Faker.js](https://fakerjs.dev/)** — Realistic fake data generation for seeds
- **[UUIDv7](https://github.com/nicosResworkssat/uuidv7)** — Time-ordered unique IDs

---

## 📝 License

This project is licensed under the **ISC** license.

---

<p align="center">
  Made with 💜 during the <a href="https://rocketseat.com.br/">Rocketseat</a> Fullstack with AI Challenge
</p>
