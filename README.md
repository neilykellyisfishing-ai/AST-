# Follow-Up Engine — Your First AI Team (v1)

A beginner-friendly, no-code blueprint for a 4-agent AI team built on Claude.
It makes sure **no person, promise, or follow-up slips through the cracks**, and you stay the one who decides and sends.

**Built for your profile:**
| Your answer | What it means for the design |
|---|---|
| Goal: ops & personal productivity | The team runs your inbox, commitments, and weekly plan. |
| Edge: selling / relationships | Relationships are the core object, not "tasks". Every follow-up protects or grows a relationship. |
| Time: 5–10 hrs/week | About 4–6 hrs to set up in week 1, then about 20 min a day plus a 30-min weekly review. |
| Tech: no-code | Claude Project + Gmail/Drive connectors + one Google Sheet. No code, no paid automation. |

---

## 1. Recommended use case

### Options compared (scored 1–5 for you)

| Use case | Fits your skills | Fast first win | Low risk | Easy to measure | Total |
|---|---|---|---|---|---|
| **Relationship-driven ops ("Follow-Up Engine")** | 5 | 5 | 4 (drafts only) | 5 | **19** |
| Lead generation | 5 | 3 (list building is slow) | 3 (outreach risk) | 4 | 15 |
| Personal productivity (general) | 3 | 4 | 5 | 2 (vague) | 14 |
| Research assistant | 2 | 3 | 5 | 2 | 12 |
| Customer support | 3 | 2 (needs a KB) | 2 (customer-facing) | 4 | 11 |
| Content research & writing | 2 | 3 | 4 | 3 | 12 |

**Recommendation: the Follow-Up Engine.** In relationship-driven work, money is usually lost in the gaps: the reply that waited a week, the "let's catch up next month" nobody scheduled, the warm contact who went cold. This team closes those gaps. It's ops work, so it matches your goal, and it feeds your strength, because you spend more time in real conversations.
It's also the natural base for **lead generation in v2**: once your relationships sheet is clean, prospecting plugs straight in.

**One-sentence mission:** *Every inbound message triaged within 24h, every commitment tracked, and every important relationship touched on time, with the human approving everything that leaves the building.*

---

## 2. Team architecture

Four agents and one human. Work flows one way; each agent owns one artifact.

```
            ┌──────────────────────────── YOU (Owner / Approver) ───────────────────────────┐
            │  approves drafts · sends · decides priorities · edits rules                   │
            └──────▲───────────────────────────▲───────────────────────────▲────────────────┘
                   │ escalations               │ review drafts             │ daily brief
 Inputs      ┌─────┴──────┐   Intake Cards ┌───┴────────────┐  Draft Reqs ┌─┴─────────────┐
 (email,  ──►│ 1. TRIAGE  │───────────────►│ 2. KEEPER      │────────────►│ 3. DRAFTER    │
  notes,     │ classify & │                │ updates Ops Hub│             │ writes Gmail  │
  calls)     │ extract    │                │ sheet, flags   │             │ DRAFTS only   │
             └────────────┘                │ cold contacts  │             └───────────────┘
                                           └───────┬────────┘
                                                   │ sheet state
                                           ┌───────▼────────┐
                                           │ 4. PLANNER     │──► Daily Brief / Weekly Plan
                                           │ chief of staff │
                                           └────────────────┘
```

| Agent | Owns (single source of truth) | Input | Output | Hands off when… |
|---|---|---|---|---|
| **1. Triage** | Intake Cards | Emails, meeting notes, voice-memo transcripts | One Intake Card per item (category, people, asks, dates, urgency) | Every item is carded → to Keeper |
| **2. Keeper** | `Ops Hub` sheet: Relationships + Tasks tabs | Intake Cards | Sheet rows to add or update + Draft Requests + "going cold" flags | Rows are proposed or written → Draft Requests to Drafter |
| **3. Drafter** | Gmail drafts | Draft Requests + Voice Guide | Draft emails (never sent), each with a self-check | Drafts exist → you review |
| **4. Planner** | Daily Brief & Weekly Plan | Ops Hub sheet + calendar | Top-3 priorities, follow-ups due, cold list, time blocks | Brief delivered → you act |

**How overlap is prevented (the "one verb" rule):**
- Triage **classifies**. It never writes to the sheet or drafts replies.
- Keeper **records**. It never writes emails or ranks your day.
- Drafter **writes**. It never decides *whether* to follow up, only *how*.
- Planner **prioritizes**. It never edits records or drafts.

If an agent is asked to do another agent's verb, it says so and hands the work to the right agent.

---

## 3. Agent design (summary)

Full production prompts are in [`agents/`](agents/). Summary:

### 1. Triage Agent — "the sorter"
- **Purpose:** Turn messy input into clean, structured Intake Cards.
- **Responsibilities:** Classify each item, extract people, asks, commitments (yours and theirs), and dates, and flag urgency.
- **Decision rules:** Urgent = money, deadline in 48h or less, a key relationship, or an upset person. Newsletters and notifications → `IGNORE` (it lists them, never deletes them). If it isn't sure, it marks `NEEDS_HUMAN`.
- **Success:** Zero missed asks or commitments on the test set. At least 90% of categories match your judgment.

### 2. Keeper Agent — "the memory"
- **Purpose:** Keep the Ops Hub sheet accurate: who matters, last touch, next step, due date.
- **Decision rules:** Never invents a contact detail. Merges duplicates by email address. A Tier A contact untouched for 14+ days → `COLD` flag (21 days for Tier B, 45 for Tier C). Every commitment you made → a Task with a due date.
- **Success:** The sheet matches reality in your weekly spot-check of 10 rows. No duplicate contacts.

### 3. Drafter Agent — "the voice"
- **Purpose:** Write follow-ups and replies that sound like you and move the relationship forward.
- **Decision rules:** One clear ask per email, under 120 words by default, no invented facts or promises, and anything uncertain goes in `[BRACKETS]` for you to fill in. Gmail **drafts only**.
- **Success:** You send at least 70% of drafts with light edits or none (track this as the edit rate).

### 4. Planner Agent — "the chief of staff"
- **Purpose:** Tell you what matters today and this week.
- **Decision rules:** Order: (1) overdue commitments you made, (2) urgent inbound, (3) Tier A follow-ups due, (4) cold Tier A/B contacts, (5) everything else. Maximum 3 "must-dos" a day.
- **Success:** You use the brief at least 5 days a week. Overdue commitments trend toward 0.

---

## 4. Workflow

### Daily loop (about 20 minutes of your time)
| Step | Who | What happens | Review gate |
|---|---|---|---|
| 1 | Triage | Reads last 24h of inbox (Gmail connector, search `newer_than:1d -category:promotions`) and any notes you paste → Intake Cards | You skim `URGENT` and `NEEDS_HUMAN` only (2 min) |
| 2 | Keeper | Turns cards into sheet updates and Draft Requests | **Week 1: you paste the rows yourself.** Later: Keeper writes and you spot-check |
| 3 | Drafter | Creates Gmail drafts for each Draft Request | **You read every draft, edit, and send yourself** |
| 4 | Planner | Daily Brief: top 3, follow-ups due, cold list | You pick the final top 3 |

### Weekly loop (30 minutes, Friday)
1. Planner produces the **Weekly Review**: what got done, overdue items, relationships that warmed or cooled, and next week's plan.
2. You do a **QA spot-check** (section 6) and log the metrics.
3. **Feedback loop:** Every draft you heavily edited → paste the before/after into the Voice Guide's "Corrections" section. Every triage mistake → add a one-line rule to the Rules Doc. The agents read both files every run, so the system learns from your edits.

### Handoff format
Every handoff uses the fenced blocks defined in each prompt (`INTAKE_CARD`, `SHEET_UPDATE`, `DRAFT_REQUEST`). Structured handoffs are what make this a team rather than four chats: each agent can check that its input is complete before it starts.

### Escalation: agents stop and ask you when…
- An email involves money, contracts, pricing, legal, or health.
- Someone is upset, or the relationship is at risk.
- Information is missing and would have to be guessed.
- Any action would **send, delete, publish, share, spend, or change something irreversibly**. Agents propose; you execute.

---

## 5. Tool stack (v1, about $0 beyond your Claude plan)

| Tool | Role in the system | Why it's needed |
|---|---|---|
| **Claude (Project: "Follow-Up Engine")** | Runs all 4 agents. Project knowledge holds the Voice Guide, Rules, and sheet schema | One place, shared context, nothing to host |
| **Gmail connector** | Triage reads mail; Drafter creates **drafts** | The work lives in email. Drafts keep you in control |
| **Google Drive / Docs** | `Voice Guide` + `Rules & Corrections` docs | Your knowledge base, and the feedback loop |
| **Google Sheets: `Ops Hub`** | Tabs: Relationships, Tasks, Intake Log, Metrics | Your database, task manager, and scoreboard in one. Columns are in [`templates/`](templates/) |
| **Google Calendar** (optional) | Planner reads free/busy for time blocks | A plan that ignores your calendar is fiction |
| **Automation: none in week 1** | Week 3+: a Claude scheduled task runs the Morning Brief | Automate only what has worked by hand for 2 weeks |

**Deliberately left out for v1:** a separate CRM, a separate task app, Zapier/n8n, vector databases, and custom code. Each adds failure points before you've proven the workflow. (Your n8n connector also failed to connect in this session. If you want it for v2, reconnect it first.)

**Security:** Connect Gmail and Drive through claude.ai Settings → Connectors (OAuth). Never paste passwords, API keys, or 2FA codes into any agent chat. Keep sensitive threads (medical, legal, financial account details) out of the agents. Triage is told to skip them.

---

## 6. QA plan

### Before launch: the "Golden 20" test
1. Pick **20 real past emails or notes**: 5 urgent, 5 routine replies, 5 FYI/ignore, and 5 tricky (vague asks, multiple people, an upset tone).
2. Write down **your own answer** for each (category, required action) *before* running the agents.
3. Run Triage → Keeper → Drafter on all 20.
4. **Pass bar:** 0 missed commitments, 90% or more of categories match you, 0 invented facts in drafts, and at least 14 of 20 drafts "sendable with light edits".
5. If it fails, fix the prompt or rules and rerun only the failures.

### Review checkpoints
| Checkpoint | When | What you check |
|---|---|---|
| Triage gate | Daily | `URGENT` and `NEEDS_HUMAN` items look right |
| Draft gate | Every draft | Facts, tone, the ask, the recipient. **You press send** |
| Sheet spot-check | Weekly | 10 random rows vs reality |
| Metrics review | Weekly | The scoreboard below |

### Common failure modes and fixes
| Failure | Symptom | Fix |
|---|---|---|
| Hallucinated commitment | Draft promises something you never said | Rule: commitments only from source text; uncertain → `[BRACKETS]` |
| Missed ask | Someone's question ignored | Triage must list *every* question mark and request; recheck on the Golden 20 |
| Wrong voice | Too formal, too long, sounds like AI | Add 5 of your real sent emails to the Voice Guide; log corrections |
| Duplicate / stale CRM | Same person twice, wrong last-touch date | Keeper merges by email; weekly spot-check |
| Scope creep | Agents doing each other's jobs | The "one verb" rule; restart the chat with the right agent |
| Over-trust | You stop reading drafts | Never auto-send in v1. Gmail drafts only |
| Privacy leak | Sensitive content in the sheet | Triage skips sensitive categories; keep the sheet private |

### Scoreboard: is the team helping? (Metrics tab, logged weekly)
| Metric | Baseline (week 0) | Target by week 4 |
|---|---|---|
| Median reply time to important email | measure it | under 24h |
| Follow-ups sent on time / due | measure it | 90% or more |
| Overdue commitments (yours) | count | 0–2 |
| Draft edit rate (heavy edits ÷ drafts) | — | under 30% |
| Tier A contacts gone COLD | count | 0 |
| Minutes per day on inbox/admin | estimate | −30% |
| Replies or meetings booked from follow-ups | count | ↑ week over week |

**Kill / continue rule:** If after 3 weeks the follow-up on-time rate hasn't improved *and* the time spent hasn't dropped, simplify (drop to Triage + Drafter only) before adding anything.

---

## 7. Seven-day launch plan (about 6 hours total)

| Day | Goal | Tasks (≈ time) | Done when |
|---|---|---|---|
| **1** | Foundation | Create the Claude Project "Follow-Up Engine" (10m). Connect Gmail + Drive in claude.ai Connectors (10m). Create the Google Sheet `Ops Hub` by importing the 4 CSVs from `templates/` as tabs (15m). | Project exists, connectors show "connected", sheet has 4 tabs |
| **2** | Knowledge | Copy `templates/voice-guide.md` and `templates/rules.md` into Google Docs or the Project knowledge. Paste 5 real sent emails into the Voice Guide (30m). List your top 20 relationships with tiers A/B/C in the Relationships tab (30m). | Voice Guide has real examples; 20 contacts are tiered |
| **3** | Agents in | Paste the system prompt from `agents/00-shared-rules.md` into the Project instructions. Save the 4 agent prompts as Project knowledge files (20m). Run Triage on today's inbox (15m). | Triage produces valid Intake Cards |
| **4** | Golden 20 | Build the test set and your answer key (45m). Run the full chain (30m). Score it. | Scores recorded in the Metrics tab |
| **5** | Fix & rerun | Fix the top 3 failures by editing the prompts or rules (30m). Rerun the failures (20m). | Pass bar met (section 6) |
| **6** | Live day | Run the full daily loop on real email. Review and send drafts yourself (40m). | First real follow-ups sent from AI drafts |
| **7** | First review | Run Planner's Weekly Review (15m). Log the metrics and corrections (15m). Decide what to change next week, with a maximum of one change. | Scoreboard week 1 filled; one improvement chosen |

**Weeks 2–4 (after launch):** Week 2: let Keeper write directly to the sheet (if spot-checks pass). Week 3: schedule the Morning Brief as a Claude scheduled task. Week 4: decide on v2: add a **Prospector** agent (lead generation) that feeds the same Relationships tab.

---

## Repo map
```
README.md                     ← this blueprint
agents/00-shared-rules.md     ← paste into Project instructions (applies to all agents)
agents/01-triage.md           ← Triage Agent prompt
agents/02-keeper.md           ← Keeper Agent prompt
agents/03-drafter.md          ← Drafter Agent prompt
agents/04-planner.md          ← Planner Agent prompt
templates/*.csv               ← Ops Hub sheet tabs (import into Google Sheets)
templates/voice-guide.md      ← fill in with your voice + corrections
templates/rules.md            ← your house rules, grows from triage mistakes
templates/golden-20.md        ← pre-launch test scorecard
```

---

## START HERE FIRST

Do these three things today (≈ 45 minutes). Don't read further until they're done:

1. **Create the Claude Project** "Follow-Up Engine" and paste in [`agents/00-shared-rules.md`](agents/00-shared-rules.md) as its instructions.
2. **Connect Gmail and Google Drive** in claude.ai → Settings → Connectors. Use the sign-in flow only; never share passwords in chat.
3. **Write down your top 20 relationships** (name, email, tier A/B/C, last time you spoke) in the Relationships tab of a new `Ops Hub` sheet. This list is what makes the team useful to *you*.

Then open a chat in the Project, paste [`agents/01-triage.md`](agents/01-triage.md), and say: **"Triage my inbox from the last 24 hours."** You've started.
