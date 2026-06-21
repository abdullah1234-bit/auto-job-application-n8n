# 🤖 Auto Job Application Assistant — n8n + Claude AI

> An AI-powered job application pipeline that searches for Werkstudent jobs in Germany every 6 hours, tailors your CV and cover letter using Claude AI, and emails you for approval before doing anything — fully automated, zero blind applying.

![n8n](https://img.shields.io/badge/n8n-workflow-orange?style=flat-square&logo=n8n)
![Claude AI](https://img.shields.io/badge/Claude-Sonnet_4.6-blueviolet?style=flat-square)
![Gmail](https://img.shields.io/badge/Gmail-OAuth2-red?style=flat-square&logo=gmail)
![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)
![Cost](https://img.shields.io/badge/job_search_API-FREE-brightgreen?style=flat-square)

---

## 🧠 The Problem

As an international MSc student in AI & Autonomous Systems in Germany, I was spending **2–3 hours every week**:

- Manually searching for Werkstudent roles across multiple German job boards
- Copy-pasting the same generic cover letter for every application
- Tweaking my CV bullet points for each role
- Missing postings because they expired within 24–48 hours
- Applying to roles that weren't even a good fit

The process was repetitive, slow, and soul-crushing. So I automated it.

---

## ✅ The Solution

A fully automated n8n workflow that:

1. **Searches** Arbeitnow (free German job API) every 6 hours for new Werkstudent / working student roles
2. **Deduplicates** results so you never see the same posting twice
3. **Scores** each job against your actual CV using Claude AI (0–100 relevance score)
4. **Writes** a tailored cover letter and CV tips for each matching role
5. **Emails you** only the strong matches (above your threshold) with an Approve / Skip button
6. **Sends** a ready-to-apply package the moment you approve — cover letter, CV tweaks, and direct apply link

You stay in full control. The AI does the grunt work. You make the final call.

---

## 🔁 Workflow Diagram

```
⏱ Schedule Trigger (every 6h)
        │
        ▼
⚙️  Job Search Config
    (your CV · query · email · min score)
        │
        ▼
🔍  Search Jobs — Arbeitnow API          ← FREE, no API key needed
        │
        ▼
🗃️  Filter New Jobs Only
    (dedupe via n8n data table)
        │
        ▼
🔄  Process Each Job (loop)
        │
        ▼
✨  Claude AI — Score + Tailor
    relevance_score · cover letter · CV tips
        │
        ├── Score < 70 → ⬇ Silently skipped
        │
        ▼
📧  Email You for Approval
    (cover letter · score · apply link)
        │
        ├── Skip → ⏭ Next job
        │
        ▼
🚀  Send Ready-to-Apply Package
    (formatted email with everything you need)
```

---

## 🛠️ Tech Stack

| Tool | Purpose | Cost |
|------|----------|------|
| **n8n** | Workflow automation engine | Free (self-host) / Cloud |
| **Arbeitnow API** | German job search (Werkstudent focused) | **100% Free** |
| **Claude Sonnet 4.6** | AI scoring + cover letter generation | Pay per use (~$0.02–0.04/run optimised) |
| **Gmail OAuth2** | Approval emails + apply packages | Free |
| **n8n Data Table** | Job deduplication tracker | Free |

---

## 🚀 Getting Started

### Prerequisites
- [n8n](https://n8n.io) account (cloud or self-hosted)
- [Anthropic API key](https://console.anthropic.com) (Claude)
- Gmail account

### Step 1 — Import the workflow
1. Download `workflow.json` from this repo
2. In n8n go to **Workflows → Import**
3. Upload the file

### Step 2 — Configure credentials

**Claude AI (Anthropic)**
1. Open the **"Claude Model"** node
2. Click **"Credential for Anthropic API"** → **Create new**
3. Paste your Anthropic API key → Save

**Gmail**
1. Open the **"Request Approval"** node
2. Click **"Credential for Gmail"** → **Create new**
3. Follow the Google OAuth2 popup → sign in → Allow
4. Do the same on the **"Send Apply Package"** node

> ℹ️ The **Arbeitnow job search requires no API key** — it works out of the box.

### Step 3 — Set your personal config
Open the **"Job Search Config"** node and fill in:

```
searchQuery     →  e.g. "Werkstudent AI Machine Learning"
minRelevance    →  minimum score to get notified (default: 70)
candidateName   →  your full name
notifyEmail     →  your email address
baseCV          →  paste your full CV as plain text
baseCoverLetter →  optional base cover letter for reference
```

**Search query ideas for AI/ML students:**
- `Werkstudent AI Machine Learning`
- `Werkstudent Data Science Python`
- `Werkstudent Machine Learning Munich`
- `Werkstudent NLP Deep Learning`

### Step 4 — Activate
Toggle the workflow to **Active** in the top right. It will run automatically every 6 hours.

---

## 📧 How the Approval Email Works

When a job scores above your threshold, you receive an email like this:

```
Subject: New Werkstudent match: ML Engineer at Acme GmbH

Role: Werkstudent ML Engineer
Company: Acme GmbH
Location: Munich, Germany
Match Score: 84/100
Apply Link: https://...

WHY IT FITS:
Strong alignment with your MSc in AI and experience in PyTorch...

TAILORED COVER LETTER:
Dear Hiring Team at Acme GmbH,
...

CV TWEAKS:
→ Move your PyTorch project to the top of the experience section
→ Highlight your multi-agent systems coursework
...

[ ✅ Apply to this job ]   [ ❌ Skip this job ]
```

Click **Apply** → get the full formatted package sent to your inbox instantly.
Click **Skip** → workflow moves to the next job automatically.

---

## ⚙️ Customisation

### Change how often it runs
In the **Schedule Trigger** node change the interval. Default is every 6 hours.

### Adjust the relevance threshold
In **Job Search Config**, change `minRelevance` from 70 to any value 0–100. Higher = fewer but stronger matches.

### Search multiple job types at once
Duplicate the workflow and use different `searchQuery` values:
- One for AI/ML roles
- One for Data Engineering
- One for Research Assistant positions

### Reduce API costs further
Replace **Claude Sonnet** with **Claude Haiku** in the Claude Model node for the scoring pass. Haiku is ~20x cheaper and fast enough for scoring. Keep Sonnet only for the cover letter generation.

---

## 📊 Performance

| Metric | Value |
|--------|-------|
| Jobs fetched per run | Up to 15 (page 1) |
| Typical new jobs after day 1 | 2–5 per run |
| Time per run (10 new jobs) | ~4–5 minutes |
| Job search API cost | $0 |
| Claude cost per run (optimised) | ~$0.02–0.04 |
| Time saved per week | 2–3 hours |

---

## ⚠️ Important Notes

- **This workflow does not auto-apply to any job.** You always review and approve before anything is sent. The final application is submitted by you on the company's career page.
- **LinkedIn direct applying is not supported** — LinkedIn has no public API and blocks automation. Arbeitnow aggregates postings from multiple boards including LinkedIn, Indeed, and Glassdoor.
- **Claude only uses facts from your CV.** It is instructed never to fabricate employers, dates, or qualifications.
- Your credentials are **never included** in the exported workflow JSON. Keep your API keys private.

---

## 🤝 Contributing

Pull requests are welcome. Ideas for improvement:

- [ ] Multi-page job fetching (pages 2, 3...)
- [ ] Digest mode — score all jobs first, send one ranked email
- [ ] Telegram / Slack notification option
- [ ] Auto-update tracker status after you apply
- [ ] Support for additional German job APIs (StepStone, Indeed DE)

---

## 👤 Author

**Muhammad Abdullah Khan**
MSc AI & Autonomous Systems — Ingolstadt, Germany
AI / ML Engineer

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/ah-khan-51566827a/)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black?style=flat-square&logo=github)](https://github.com/muabdullahkhan28)

---

## 📄 License

MIT License — free to use, modify, and distribute. See `LICENSE` for details.

---

> *Built out of frustration with manual job hunting as an international student in Germany. If this saves you time too, drop a ⭐ on the repo.*
