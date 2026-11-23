# 100days

Personal 100-day workspace. Its main tool is a planning agent that researches
an evidence-based plan, interviews you until the plan fits your real life,
then pushes a short briefing to your phone (ntfy) and your desktop
(`notify-send`).

## What the agent does

1. **Loads context** — reads `AGENT.md`, plus the latest `week<N>.md` and
   `workout<N>.md` when the request is about food or training.
2. **Researches** — uses web search and sourced material, prioritising
   systematic reviews and position stands, with Dr. Andrew Huberman (sleep,
   light, stress, focus, habits) and Dr. Lauren Colenso-Semple (exercise,
   hypertrophy, nutrition) as preferred voices in their lanes.
3. **Interviews you** — asks 3–6 focused questions per round, with options and
   defaults, until it can restate your constraints correctly. It pushes back
   on plans that will not survive your week.
4. **Writes the plan** — concrete times, doses, minimum versions, metrics, and
   a review checkpoint. Nothing is written until you confirm the plan. Then:
   - meals go to `week<N>.md`
   - workout, sleep and daily rhythm go to `workout<N>.md`

   where N is the week being planned (for example week 2 → `week2.md` and
   `workout2.md`).
5. **Delivers** — sends a title plus at most five short lines to your phone
   and desktop with `./bin/notify`, queuing one week (Mon–Sun) per batch.

## Setup

1. Install **ntfy** on your phone (Android: Play Store/F-Droid, iOS: App
   Store).
2. Read the topic name and subscribe to it in the app:

   ```bash
   cat ~/project/100days/.ntfy-topic
   cat ~/project/100days/.ntfy-server
   ```

   Subscribe to `<server>/<that-topic>`, for example
   `https://t2knock-lab.turkey-alhena.ts.net:8443/<topic>`, and log in with
   the ntfy account. This server denies anonymous read and write.

3. Install the desktop pieces (Linux):

   ```bash
   sudo apt install libnotify-bin   # provides notify-send
   ```

   A running notification daemon is required for popups (most desktops have
   one).

4. Test from this directory:

   ```bash
   ./bin/notify --title "100days" --priority high "Notifications are working"
   ```

   The phone should buzz and the desktop should show a popup.

5. On Android, disable battery optimisation for ntfy if pushes arrive late.

## Running the agent

```bash
cd ~/project/100days
opencode --agent notifier
```

Example prompts:

- "Help me build a four-week strength plan around my current schedule. Ask me
  what you need."
- "My sleep has been bad for two weeks. Research it and propose a protocol."
- "Send me a plan for tomorrow morning."
- "Remind me in 45 minutes to stretch."
- "At 18:00 tonight, remind me to review my progress — high priority."

## Standing rules

`AGENT.md` is the source of truth. In short:

- Only `./bin/notify` is allowed as a shell command. No cron, no installs, no
  calendar changes.
- Notifications are short: a title plus at most five lines.
- Food is described in **grams only** — no calories, no kcal totals — and the
  word "PCOS" is never used.
- Eating window 12:00–20:00; 12:00–21:00 on Tue/Thu.
- Weekday wake 06:30, bed 22:30 (22:45 Tue/Thu). Weekend 07:30 / 23:30.
- Runs are Tue/Thu 19:45–20:30, 3 km.

The agent may edit only `AGENT.md` (queue state), `week<N>.md`, and
`workout<N>.md`. It will not overwrite a per-week file that already has
content without asking.

## The `notify` command

```bash
./bin/notify "Body"
./bin/notify --title "Title" --priority high "Body"
./bin/notify --delay 30m "Body"          # relative delay
./bin/notify --at "today 18:00" "Body"   # local clock time
./bin/notify --at "2026-11-16 06:00" "Body"
./bin/notify --tags clipboard "Body"     # optional ntfy tag
./bin/notify --no-desktop "Body"         # phone only
```

| Flag | Meaning |
| --- | --- |
| `-t`, `--title` | Notification title |
| `-p`, `--priority` | `low`, `default`, `high`, `urgent` |
| `-d`, `--delay` | Relative delay: `30s`, `45m`, `3h`, `2 days` |
| `--at` | Any time GNU `date` understands; converted to a local timestamp |
| `--tags` | ntfy tags/emoji, comma-separated |
| `--no-desktop` | Skip the desktop popup |

Write `\n` in the body for a line break.

Configuration, in order of precedence:

| Setting | Environment | File | Default |
| --- | --- | --- | --- |
| Server | `NTFY_SERVER` | `.ntfy-server` | `https://ntfy.sh` |
| Topic | `NTFY_TOPIC` | `.ntfy-topic` | — |
| Access token | `NTFY_TOKEN` | `.ntfy-token` | — |
| Account | `NTFY_USER` + `NTFY_PASSWORD` | `.ntfy-credentials` (line 1 user, line 2 password) | — |

This project uses the self-hosted server in `.ntfy-server` with the account
in `.ntfy-credentials`.

Delivery behaviour:

- **Immediate** messages go to the phone and, on a graphical session, to the
  desktop.
- **Scheduled** messages reach the phone only; no desktop popup fires at send
  time.
- The server accepts schedules at least **7 days** ahead (verified), so the
  agent queues one week at a time: Mon–Sun of the following week.
- Publishing is rate-limited to about 60 requests per 5 minutes; large
  batches need pacing (HTTP 429 if exceeded).
- Repeating schedules are not supported. Send one message per occurrence.

## Files

| Path | Purpose |
| --- | --- |
| `bin/notify` | The only command the agent may run |
| `.ntfy-server` | Server base URL |
| `.ntfy-topic` | Secret topic name (mode `600`, gitignored) |
| `.ntfy-credentials` | Username/password (mode `600`, gitignored) |
| `.ntfy-token` | Optional access token (gitignored) |
| `AGENT.md` | Standing rules and notification queue state |
| `week<N>.md` | Meal plan for week N, e.g. `week1.md` |
| `workout<N>.md` | Training, sleep and rhythm for week N, e.g. `workout1.md` |
| `.opencode/agents/notifier.md` | Agent prompt and permissions |
| `opencode.jsonc` | Project config and default model |

## Troubleshooting

- **No phone notification** — confirm the topic in the app matches
  `.ntfy-topic`; on Android, allow background activity for ntfy.
- **Phone only, no desktop** — scheduled messages by design; for immediate
  messages, check `notify-send` is installed and `DISPLAY` or
  `WAYLAND_DISPLAY` is set.
- **`HTTP 403` from notify** — missing or invalid credentials. Check
  `.ntfy-credentials` (username on line 1, password on line 2) and confirm
  the account may publish to the topic on the server.
- **`HTTP 400` from notify** — the delay is under 10 seconds, beyond the
  server's limit, or the `--at` time cannot be parsed. Use an explicit local
  time such as `"tomorrow 06:00"`.
- **`HTTP 429` from notify** — too many messages too quickly (about 60 per
  5 minutes). Wait a few minutes and resend the remaining batch.
- **Agent refuses a command** — shell access is intentionally limited to
  `./bin/notify`.

## Privacy

The topic name is effectively a password. `.ntfy-topic`,
`.ntfy-credentials` and `.ntfy-token` are gitignored and mode `600`. Anyone
with the topic and an authorised account can read and publish to it, and the
server operator can see message contents. Do not send secrets.
