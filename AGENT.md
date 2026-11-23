# Agent handoff — 100-day notifier

Read this first. Then read `week<N>.md` (menus) and `workout<N>.md` (schedule)
for the week you are working on.

## How I work

- Only `./bin/notify`. No other shell commands.
- Title + max 5 short lines per message.
- The self-hosted ntfy server accepted **8 days ahead** on 21 Nov 2026 (week 3,
  Sun 29 all queued on Sat 21). Queue one week (Mon–Sun) per batch instead of
  batching every 3 days. Do not queue beyond the following week unless I ask. It
  rate-limits publishing to roughly 60 requests per 5 minutes; pace large batches.
- One message per occurrence — no repeating schedules.
- **Message structure — from week 3 (Mon 23 Nov):**

  - **Cook message** (19:00 the evening before): the **only** meal message of the
    day. It carries **tomorrow's** dish and grams plus tomorrow's three meal rows —
    the user cooks at 19:00 for the next day's lunch and dinner. Four lines.
  - **No 20:00 preview, no 06:30 menu.** Both dropped from week 3. The cook message
    replaces them.
  - **Briefing** (07:00 weekday / 08:00 weekend): actions only — the morning walk,
    the lunch walk, the session, the window. No meal grams, no meal lines, no step
    count, no weigh-in. It must not duplicate the cook message.
  - **Step counts stay out of every notification.** Never word them as a target for
    the morning walk: 15 min of walking is only ~1,500–2,000 steps. The floor lives
    in `workout<N>.md`; briefings carry the *actions* (morning walk, lunch walk).
- Write a confirmed plan to the project root: meals to `week<N>.md`, workout,
  sleep and daily rhythm to `workout<N>.md`, where N is the week being
  planned. Do not write any plan file before I confirm.
- If `week<N>.md` or `workout<N>.md` already has content, ask before
  replacing it.

## Standing rules — do not change without asking

- Menus: **food weights in grams only**. No calories, no kcal totals.
- Never use the word "PCOS".
- Eating window 12:00–20:00; Tue/Thu 12:00–21:00 (dinner 20:45 after the run).
- Weekday wake **07:00**, bed **23:15** (ladder: 22:45 week 4, 22:30 week 5).
- Weekend wake **08:00**, bed **00:00**; Sunday night back to 23:15 / 07:00.
- Weekday briefing 07:00, weekend briefing 08:00.
- Cook message 19:00 the evening before (19:15 Mon/Wed — after the 18:30 session).
- Session pings: Mon/Wed 18:30, Tue/Thu 19:45. **No Friday ping** — the 07:00
  briefing carries it.
- Runs are 19:45–20:30, 3 km.
- **Never send body weight, height, BMI or any body measurement** (waist, body
  composition, scale readings) in a notification, to any server. Food weights in
  grams are fine — those are portions, not my body. Briefings carry no weigh-in
  reminders; weigh-in protocol lives in the week files only.

## Queue state (update after every batch)

- Queued through: **Sun 29 Nov 22:15** (week 3), 26 messages.
- Week 3 files: `week3.md` and `workout3.md` written and confirmed.
- Week 3 message types: **cook 19:00** (Sun 22 at 19:45 as the bridge),
  **briefing 07:00 / 08:00**, pings Mon/Wed 18:30, Tue/Thu 19:45, wind-down
  22:15 (23:00 Fri/Sat), checkpoint Sun 29 20:30.
- Week 2's 06:30 menus and 20:00 previews are on the server and cannot be
  recalled. A week-2 Sunday preview may fire at 20:00 on 22 Nov — if it does,
  ignore it; the week-3 cook message is the current menu.
- **Server accepted 8 days ahead** on 21 Nov — no need to split the last day.
- Briefings carry no weigh-in reminder and no step count (standing rule above).
- Next batch: **week 4** — needs plan confirmation before anything is queued.
  Week 4 bedtime 22:45 (ladder). If week 3's four runs and three morning walks
  all happened, Tuesday intervals go to 6.

## Message template — cook (19:00, for the next day)

```
Title: Cook now · for Tue 24 — Mực xào cần tây

Cook: squid 600 g · celery 200 g · onion 100 g · tofu 150 g
Lunch 12:00 — squid 360 g + rice 150 g
Snack 18:00 — yogurt 200 g + banana 100 g
Dinner 20:45 — squid 240 g + rice 120 g
```

Four lines. Line 1 is what to cook **tonight**, copied from `week<N>.md`; lines
2–4 are tomorrow's meals, split 60/40. Run days shift to snack 18:00 and dinner
20:45; that is already in the row times. Grams only — no calories.

**Step counts and weigh-ins are tracked in `workout<N>.md` and `week<N>.md`. They
do not appear in any notification.**
