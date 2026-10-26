---
name: project-architecture-diagram
description: Animated TrackPilots architecture diagram — source file, published repo, current node/flow decisions, video output and known code mismatches
metadata:
  type: project
---

# TrackPilots architecture diagram (animated single page)

## Files and where they live
- **Source (edit here):** `/Users/apple/Desktop_Backup/QuickAppStudio/trackpilots-application/architecture-animation/architecture-animation.html` (single self-contained SVG + CSS + JS page, viewBox 1760x990).
- **Published copy:** repo folder `/Users/apple/Desktop_Backup/QuickAppStudio/trackpilots-application/architecture.trackpilots.com/` holds `index.html` and `architecture-animation.html`, both identical to the source. Remote: `https://github.com/ec2-learn-instant/architecture.trackpilots.com.git`, branch `main`. Last pushed commit: `d77eaf5`.
- **Local preview:** `http://127.0.0.1:3000/architecture.trackpilots.com/architecture-animation.html` serves the repo folder, so copy the source into the repo folder before the user can see changes.
- **Video:** `/Users/apple/Downloads/trackpilots_architecture_flow.mp4` (about 34 s, one full loop). Recorded with Playwright (python) and converted with ffmpeg.
- The old static page `trackpilots-application/architecture.html` is not part of the repo; it is superseded.

## Structure (current)
- **Animation loop (16 s):** check (0–3.5 s) → online (3.5–9 s) → offline (9–13 s) → online (13–16 s). Phase is set on the `<svg data-phase>` attribute; CSS and JS key off it.
- **Desktop path:** Employee → Desktop App (electron.trackpilots.com) → Local DB (SQLite, store) → API Server (desktop.trackpilots.com) → RabbitMQ (rabbitmq.trackpilots.com, label "Message Queue").
- **Event services** (all named "Service", not "Svc" or "Server"): Tracking, Activity, Screenshot, Icons. Each sends events to the Central Database (PostgreSQL + TimescaleDB).
- **Webhooks Service** (webhooks.trackpilots.com): sits above the four event services. Redis → Webhooks (teal). Light dashed bus from Tracking, Activity, Screenshot to Webhooks. Note: "Webhook ID exists: emit event / No webhook ID: skip".
- **Plan check (red dashed):** Tracking/Activity/Screenshot connect to Redis for the webhook ID check. Label: "webhook ID check".
- **Redis Service** (redis.trackpilots.com): caption lists cached fields (organisationId, plan, plan start & end, webhook IDs, reseller ID). Redis reads Central DB only when cache is empty ("reads Postgres only if cache is empty", dashed grey).
- **Settings Service** (settings.trackpilots.com) ↔ Paddle: create checkout, webhook back. Web App → Settings labelled "billing API". Paddle checkout overlay drawn as dashed blue from Web App to Paddle.
- **Central Database** → **Backups Service** (backups.trackpilots.com) → **Amazon S3** (backup bucket), stacked under the DB.
- **Bottom row:** Web Browser / Mobile App (Admin/Manager/Employee are listed under app.trackpilots.com, not on the browser box) ↔ Web App (app.trackpilots.com) ↔ GraphQL API (graphql.trackpilots.com) ↔ Central Database. All three round trips are single lines with arrows at both ends.
- **Website** (trackpilots.com): only link is the lead form → GraphQL API ("lead form"). It runs along the bottom edge. No Settings link. Browser ↔ Website link exists as a two-way line.
- **Offline tags** ("offline", red, shown only in the offline phase) on Desktop App, API Server, Local DB, Tracking, Activity, Screenshot, Icons.
- **Header row:** title and subtitle at top-left; subtitle sits under the title. Status pill "Checking/Online/Offline WebSocket" at top-right. No footer, no legend.

## Code vs diagram (known mismatches; diagram follows the user's decisions)
- `webhooks.trackpilots.com` uses Postgres (`src/webhooks/slackDelivery.js`) and depends on `amqplib`. The diagram shows Redis as its only data link because the user said so.
- Tracking/Activity/Screenshot do not yet check Redis before emitting (per code review). The diagram shows the intended check.
- `settings.trackpilots.com` calls the Redis sync endpoint (`src/routes/api-routes.js`, around line 55). The user said there is no direct Settings → Redis link, so none is drawn.
- Sign-up form on the website checks a captcha through Settings before calling GraphQL (`src/components/root/sign-up/Banner.js`). Not drawn; the user asked for GraphQL only.
- `backup_postgresql.sh` / `backup_timescaledb.sh` do `pg_dump` then `aws s3 cp`.

## Naming and wording decisions (user-confirmed)
- Use "Service" for the backend components, not "Svc" and not "Server".
- Hostnames shown under each name exactly as the user gave them (e.g., rabbitmq.trackpilots.com).
- Subtitle: "Desktop App → WS Health Check → Online: Pipeline / Offline: Local DB".

See also: [[feedback_architecture_diagram_edits]] for how to make changes and when to commit; [[project_trackpilots_architecture]] for the wider system (note: that memory predates several of these services and may be out of date).
