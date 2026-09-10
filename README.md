# IT Support Ticket Analysis

## Background

I spent 3 years as a system administrator at a school, handling exactly this kind of IT support data — password resets, network issues, account lockouts, SLA-bound tickets. After a career gap, I'm returning to the data side of IT operations as a data analyst. This project uses my sysadmin background to analyze IT support ticket data the way I used to generate it, and turn it into structured insights.

## Dataset

- **Source:** Synthetic IT Support Tickets (Kaggle)
- **Size:** 745 incident tickets, all resolved/closed
- **Format:** Ticket details are stored as nested JSON inside a single CSV column, alongside diagnostic notes, root cause descriptions, and resolution details.

## Tools

Python (pandas), Google Colab

## Progress so far

- Loaded the raw dataset and inspected its structure
- Discovered that core ticket details (title, description, priority, SLA plan, device environment) are stored as a JSON object inside a single text column
- Parsed the nested JSON for all 745 rows and extracted structured fields, including `priority` and `sla_plan`
- Found `sla_plan` contained **281 inconsistent text variants** of what should be a handful of categories (e.g. `"Standard"`, `"standard"`, `"Standard Business Hours"`, `"BusinessHours"` all referring to the same plan)
- Cleaned and standardized these into 4 real categories — **Standard, Gold, Enterprise/24x7, Other** — using text normalization and keyword-based rules
- Priority levels are nearly evenly split: **medium (254), high (249), low (242)**

## In progress / next steps

- [ ] Cross-tabulate `priority` against `sla_category` to check for mismatches (e.g. high-priority tickets not getting faster SLA plans)
- [ ] Extract and analyze device/environment fields (OS, platform, region)
- [ ] Analyze `root_cause` text for recurring failure themes
- [ ] Add visualizations (priority distribution, SLA category breakdown, mismatch analysis)
- [ ] Write up final findings and recommendations

## Why this project

Most IT ticket datasets are already clean. This one wasn't — it required parsing nested JSON and reconciling messy, human-entered categorical data, which reflects the kind of real-world data cleaning work analysts actually do, not just polished example datasets.

