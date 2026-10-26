---
name: feedback-architecture-diagram-edits
description: How to change the TrackPilots architecture diagram — edit source, render-check, sync repo copies, commit only when asked, layout rules the user cares about
metadata:
  type: feedback
---

# Working on the architecture diagram

**Rule:** Edit `architecture-animation/architecture-animation.html`, render it headless to check, then copy it to both files in the `architecture.trackpilots.com` repo folder (`architecture-animation.html` and `index.html`). Do not commit or push until the user asks. When they do, commit only those two files and push to `main`.

**Why:** The user reviews each change in a local preview served from the repo folder, so an unsynced copy looks like the change never happened. They have also said "push" many times, and each push goes to a public GitHub repo, so commits are only made on request.

**How to apply:**
- Render with headless Chrome (`--virtual-time-budget=6000 --screenshot`), then crop the area you changed and look at it. Headless renders do not run the requestAnimationFrame loop, so the packets won't appear in screenshots. Verify packet logic separately if needed.
- Commit message: `feat: ...` plus a body, ending with `Co-Authored-By: Claude Haiku 5.5 <noreply@anthropic.com>`.
- Keep text the user typed exactly (e.g., "Admin/Manager/Employee", "rabbitmq.trackpilots.com", "Service" not "Svc").
- When a line crosses another line, say so and offer the fix. Don't silently move several boxes.
- Scaling the diagram (scale 0.92 on the main group) was approved when space ran out; the main group is `translate(0,70) scale(0.92)`.
- Two-way links: one path with `marker-start` and `marker-end` using an `auto-start-reverse` marker. Do not draw a second parallel line.
- Ask one short question when the request is ambiguous or cut off; several earlier turns were lost to guessing.

**Pitfalls seen:**
- Regex deletes across `\n\n` can remove the wrong block (the Central Database box disappeared once). Check after each edit.
- The `.name` and `.host` CSS classes override `font-size` attributes; set sizes with `style="font-size: …"`.
- A background strip behind text is the way to let a line pass under a subtitle.

See [[project_architecture_diagram]] for the current structure and decisions.
