---
order: 3
title: Installation
summary: Installaing the frontend component 
sidebar_title: Installation
---

<br>

# Frontend Quick Start

This guide will help you get up and running quickly, whether you’re developing locally or deploying in a containerized environment.

## Prerequisites

- Node.js (LTS): 20.x or 22.x recommended
- npm: 10+ (bundled with Node.js)

## 1. Getting Frontend Component

Clone the repository and enter the project directory:

```bash
git clone <repo-url> frontend
cd vortex-frontend
```

Ensure Node.js (LTS 20.x or 22.x) and npm are installed:

```bash
node -v
npm -v
```

## 2. Configuration

Create a .env.local file in the project root with your API endpoints:

```bash
# Required: API base URL (defaults to http://localhost:8080 if omitted)
NEXT_PUBLIC_API_BASE_URL=http://localhost:8080

# Optional: WebSocket base URL (auto-derived from API if omitted)
# For http://localhost:8080 this becomes ws://localhost:8080
# Set explicitly only if you need a different host/protocol:
# NEXT_PUBLIC_WS_BASE_URL=ws://localhost:8080
```

## 3. Deploy in Development Mode

Install dependencies:

```bash
npm install
```

Start the dev server (Turbopack):

```bash
npm run dev
```

Open `http://localhost:3000`

!!! info "Tips:"
    Ensure your backend is reachable at NEXT_PUBLIC_API_BASE_URL. Update .env.local and restart the dev server if you change variables

## 4. Deploy in Production Mode

Set production environment variables

```bash
export NEXT_PUBLIC_API_BASE_URL=https://your-api.example.com
# Optional:
# export NEXT_PUBLIC_WS_BASE_URL=wss://your-api.example.com
```

Install clean deps, build, and start:
```bash
npm ci
npm run build
npm run start -p 3000
```

!!! info "Notes:"
    NEXT_PUBLIC_* variables are inlined at build time. Set them before npm run build. Ensure the API/WebSocket endpoints are reachable from the deployed environment.
