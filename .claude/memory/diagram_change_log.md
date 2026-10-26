---
name: diagram-change-log
description: Ordered log of every requested change to the architecture diagram, from first draft to the flow video — use it to see why the diagram looks the way it does
metadata:
  type: project
---

# Architecture diagram: change log (in order)

Files: `architecture-animation/architecture-animation.html` (source), copied to this repo as `index.html` and `architecture-animation.html`. Video: `trackpilots_architecture_flow.mp4`.

## Early versions
1. Static page `architecture.html`: Electron agent (electron.trackpilots.com) → desktop.trackpilots.com router → activity, screenshot and icons services.
2. Animated page built from a reference GIF (`trackpilots_architecture.gif`): Desktop App → API Server → RabbitMQ → Tracking, Activity, Screenshot services → DB. Health check arc, online/offline phases, status pill.
3. Added the web app (app.trackpilots.com) and GraphQL API (graphql.trackpilots.com) with a PostgreSQL link.
4. Merged the three per-service databases into one Central Database.
5. Removed the event chips in the middle, then added the Employee node (top-left) and "Admin/Manager/Employee" as web users.
6. Added a small Local DB linked from Desktop App.
7. Added Icons service (between Tracking and Activity), then evenly spaced the four services and moved Icons to the bottom.
8. Renamed "Svc" to "Service" (then briefly "Server", then back to "Service" as the accurate term).

## Settings, Paddle and Redis
9. Added Settings Service (settings.trackpilots.com) with Paddle checkout: create checkout, webhook back, billing API from the web app, and Paddle checkout overlay (browser UI).
10. Added Redis Service (redis.trackpilots.com): Settings → Redis link (later removed: no direct Settings ↔ Redis), Redis reads Postgres only if cache is empty.
11. Added Webhooks Service (webhooks.trackpilots.com), fed only from Redis (user decision; code also uses Postgres).
12. Added event flow: Tracking, Activity and Screenshot store to the DB, check the webhook ID in Redis, then emit. Paid/free note removed; final rule is "Webhook ID exists: emit event / No webhook ID: skip".
13. Redis caption lists cached fields: organisationId, plan, plan start and end, webhook IDs, reseller ID.
14. Webhook connectors: all three services connect to Webhooks; plan check connectors from all three to Redis; then the Webhooks box was moved above the services.

## Layout and labels
15. Removed the legend and status button, then restored the status pill and moved it to the top.
16. Header: title and subtitle on one row, subtitle left-aligned under the title, health status pill larger; footer added then removed.
17. Reduced font sizes; scaled the main group to 0.92 to free bottom space.
18. Web Browser: enlarged and renamed "Web Browser / Mobile App" (user-named). "Admin/Manager/Employee" moved under app.trackpilots.com.
19. Central Database: label "PostgreSQL + TimescaleDB"; "Message Queue" label under RabbitMQ; RabbitMQ host rabbitmq.trackpilots.com.
20. Backups Service (backups.trackpilots.com) and Amazon S3 stacked under the database (pg_dump then aws s3 cp).

## Website and lead form
21. Added the public website (trackpilots.com) linked to Settings and Web App, then changed to only the lead form to GraphQL API (createOrganisationRequest). Captcha via Settings is not drawn.
22. Website moved to the bottom row; Web Browser moved so the lead-form line runs above it.
23. Browser ↔ Website two-way link (user later said the website only talks to GraphQL, so the browser link was kept as the only other link).

## Two-way flows
24. Browser ↔ Web App ↔ GraphQL API ↔ Central DB: each round trip is one line with arrows at both ends.
25. Local DB flow: Desktop App stores locally, then emits from Local DB to the API Server (online). Offline notes under Local DB: store locally, skip cloud emit.
26. Offline tags (red, only in offline phase) added to Desktop App, API Server, Local DB, Tracking, Activity, Screenshot, Icons.

## Repos and outputs
27. Committed and pushed to `ec2-learn-instant/architecture.trackpilots.com`: `1e938a6`, `99b4865`, `3b0ada5`, `d77eaf5` (main).
28. Flow video recorded (Playwright, about 34 s) to `~/Downloads/trackpilots_architecture_flow.mp4`, then copied into the repo and pushed (`630ed1b`).
29. Memory notes saved locally and then into the repo under `.claude/memory/` (`ad152f8`).

## Open items (not done)
- Tracking, Activity and Screenshot do not yet check Redis before emitting (per code review).
- Webhooks Service code uses Postgres and amqplib; the diagram shows Redis only, per user decision.
- Website captcha path (Settings) not drawn.
- Whether the offline tags should be always visible (currently shown only in the offline phase).
