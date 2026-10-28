# Shashka Arena

A responsive Uzbek-language starter for online Russian draughts (8×8), built with React, Express, Socket.IO, Prisma and PostgreSQL.

## What is included
- Username/password registration and login; bcrypt password hashing and JWT in an HttpOnly cookie
- Admin-only tournament creation and start action
- Tournament registration and game listings
- Live Socket.IO game rooms and server-side move validation
- PostgreSQL persistence and responsive UI
- Single-service deployment configuration: Express serves the built React app and Socket.IO from the same origin

## Run on Windows
Requirements: Node.js 20+ and Docker Desktop.

1. Extract the ZIP.
2. Copy `.env.example` in the project root to `.env`.
3. In `.env`, change `JWT_SECRET` to a unique random string of at least 32 characters.
4. Open a terminal in the project folder and run:
   ```bash
   docker compose up -d db
   npm install
   npm run install:all
   npm run db:generate
   npm run db:push
   npm run dev
   ```
5. Open `http://localhost:5173`.

## Make your first account an admin
1. Register a normal account in the website.
2. Open a second terminal in the project root:
   ```bash
   npm run make-admin -- YOUR_USERNAME
   ```
3. Replace `YOUR_USERNAME` with the username you registered. Sign out and back in.

Never expose the admin promotion command to the public or commit secrets to GitHub.

## Deploy publicly on Render
1. Create a GitHub repository and upload the extracted project files (not the ZIP file itself).
2. In Render, create a PostgreSQL database and copy its **Internal Database URL**.
3. In Render choose **New → Web Service**, connect your repository, and select the `main` branch.
4. Use these settings:
   - Runtime: Node
   - Build Command: `npm install && npm run install:all && npm run db:generate && npm run build`
   - Start Command: `npm run db:push && npm start`
   - Health Check Path: `/api/health`
5. Add environment variables to the web service:
   - `DATABASE_URL` = Internal Database URL copied from Render Postgres
   - `JWT_SECRET` = a new unique random secret of at least 32 characters
   - `NODE_ENV` = `production`
   - `CLIENT_ORIGIN` = the exact `https://YOUR-SERVICE.onrender.com` URL after the service name is assigned
6. Deploy. If you change `CLIENT_ORIGIN` after the first deploy, save the environment variables and redeploy.
7. Open the Render URL and register an account. To make the first admin, run the one-time admin command against the production database using a secure deployment shell/job; do not make a public endpoint for promoting admins.

Render's free web services may sleep when inactive, and free PostgreSQL databases have limits/expiry; check the current plan details before relying on it for a public tournament. See the official docs:
- https://render.com/docs/web-services
- https://render.com/docs/postgresql
- https://render.com/docs/free

## Important limitations before public launch
This is a runnable MVP foundation, not a security-audited public tournament product. The current tournament starter does not automatically progress every winner through all Olympic/single-elimination rounds, and odd-player bye handling needs completion. Reconnect/recovery, draw/stalemate rules, rate limiting, account recovery, anti-abuse measures, monitoring, backups, load tests, and an independent security review should be completed before opening it to a broad public audience. Do not use real personal or sensitive data until the service has been reviewed.
