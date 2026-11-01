# n8n workflow: daily job digest from the German Federal Employment Agency (Arbeitsagentur) → Telegram

An [n8n](https://n8n.io) workflow that searches the official **Jobsuche API** of the Bundesagentur für Arbeit for your keywords around a German postcode, removes duplicates, old postings and anything it has already sent, ranks the rest and posts one compact digest to Telegram every morning.

No API key and no account needed: the Jobsuche API uses the public header `X-API-Key: jobboerse-jobsuche`.

![flow](https://img.shields.io/badge/n8n-workflow-EA4B71) ![api](https://img.shields.io/badge/API-Bundesagentur%20f%C3%BCr%20Arbeit-005CA9)

## What it does

```
Schedule (07:00) → Search settings → Build queries → Jobsuche API (v6)
                 → Filter & rank → Format digest → Telegram
```

1. **Search settings** — keywords, postcode, radius, max age, exclude words, Telegram chat ID.
2. **Build queries** — one request per keyword (the API takes one search term at a time), sent one by one with a short pause.
3. **Filter & rank** — merges results, drops postings older than your window or containing excluded words, skips everything already sent (kept 60 days in workflow static data) and ranks by number of matching keywords, keyword in title, home office and distance.
4. **Format digest** — HTML message, split automatically below Telegram's length limit. If nothing new was found, nothing is sent.

Example message:

```
🧭 Neue Stellen 01.11.2026 — 20 Treffer rund um 79098 (50 km), hier die 15 besten

Senior Solution Consultant Digitalisierung (m/w/d)
Mobile Function GmbH · Villingen-Schwenningen · 44 km · 🏠 Homeoffice
🔎 Digitalisierung
Stellenanzeige öffnen
```

## Setup (≈5 minutes)

1. In n8n: **Workflows → Import from file** → `workflow.json`.
2. Open **Search settings** and set keywords, postcode, radius and your Telegram chat ID (e.g. via @userinfobot).
3. Add a Telegram bot credential to **Send to Telegram**.
4. Activate the workflow.

## Good to know

- Use a **postcode** (PLZ), not a town name. Town names are ambiguous — `wo=Lahr` resolves to Lahr in the Eifel, not Lahr/Schwarzwald.
- List endpoint is `/pc/v6/jobs`; the older v4/v5 list endpoints return 403.
- The `veroeffentlichtseit` parameter does not filter reliably, so the date check happens in **Filter & rank**.
- "Already sent" memory only persists in **active** (scheduled) runs, not in manual test executions.
- Prefer email, Slack or Google Sheets? Replace the last node.

Filtering and formatting logic tested against the live Jobsuche API (November 2026).

---

## Deutsch — kurz

Ein n8n-Workflow, der jeden Morgen die Jobsuche der Bundesagentur für Arbeit nach Ihren Stichworten rund um eine Postleitzahl abfragt, Dubletten und bereits gemeldete Stellen aussortiert und eine kompakte Übersicht per Telegram schickt. Kein API-Schlüssel nötig. Einrichtung: `workflow.json` importieren, Stichworte, PLZ, Umkreis und Telegram-Chat-ID im Knoten **Search settings** eintragen, Telegram-Zugang hinterlegen, aktivieren.

---

Built by **Denis Shokhirev** · [DennisCraft AI Studio](https://gerdennisai.com/de/), Freiburg im Breisgau — n8n automation and AI agents for small and mid-sized businesses ([n8n-Experte Freiburg](https://gerdennisai.com/de/n8n-experte-freiburg/), [LinkedIn](https://www.linkedin.com/company/denniscraft-ai-studio/)).

License: MIT
