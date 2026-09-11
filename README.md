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
- Analyzed the free-text `root_cause` field and built a 9-category classification (Identity/Authentication, Certificates, Device Compliance/MDM, Driver/Printer, Endpoint Security, DNS/Network, Email/Sync, API/Integration, Access/Permission) using keyword-based rules, reducing unclassified rows to under 3%
- Built visualizations for ticket priority distribution, root cause category distribution, and SLA category by priority

## Key findings

- **Priority is nearly evenly split:** medium (254), high (249), low (242) — no major skew.
- **SLA assignment is broadly priority-aware:** 41% of high-priority tickets received an expedited SLA plan (Gold or Enterprise/24x7), versus just 2-4% of medium/low priority tickets. However, 51% of high-priority tickets still received only a Standard SLA — worth flagging as a possible inconsistency in how "high priority" is applied.
- **Gold-tier SLA is reserved almost exclusively for urgent issues:** 91% of Gold SLA tickets (71 of 78) are high-priority.
- **Identity/Authentication (159 tickets, ~21%) and Certificates (157 tickets, ~21%) are the two dominant root causes**, together accounting for over 40% of all incidents — well ahead of Device Compliance/MDM, Driver/Printer, and Endpoint Security (each ~75-80 tickets). This suggests self-service password/MFA recovery and proactive certificate renewal monitoring would likely reduce ticket volume more than addressing any other single issue type.
- **Root cause category showed little relationship with priority** — most categories were evenly split across high/medium/low (roughly 30-40% each), suggesting priority is driven more by business impact or user role than by the technical nature of the issue.

## Environment field cleaning

The nested `environment` object (OS, platform, region, user group) was similarly inconsistent and required cleaning:

- **OS** (101 raw variants → 8 categories): Windows Desktop (338), Unknown/missing (181, ~24% of tickets had no OS recorded), iOS (68), Windows Server (66), Mixed (42), Linux/macOS (32), Other (10), Android (8).
- **Region** (84 raw variants → 8 categories): US-East dominates (451, ~60%), followed by EMEA (115) and US-West (79). Only 22 rows (~3%) remained uncategorized.
- **Platform** (296 raw variants → 7 categories): this field mixed several unrelated concepts (device type, hosting location, infrastructure type, network software) rather than one consistent idea, which limited how far categorization could go — 161 rows (~22%) remained in "Other." This is reported as a genuine data quality limitation rather than an analysis gap.
- **User Group** (207 raw variants → 8 categories): Sales (264, ~35%), Internal/General Staff (174), Finance (92), Engineering (76), Other (75), Remote Workers (27), Marketing (20), Application Support (17). Sales tickets dominate the dataset, so department-level comparisons should account for this volume imbalance rather than being read as "Sales has more problems."

## Next steps

- [ ] Join with `user_directory.csv` for a user/department-level view
- [ ] Finalize recommendations section

## Why this project
Most IT ticket datasets are already clean. This one wasn't — it required parsing nested JSON and reconciling messy, human-entered categorical data, which reflects the kind of real-world data cleaning work analysts actually do, not just polished example datasets.
Most IT ticket datasets are already clean. This one wasn't — it required parsing nested JSON and reconciling messy, human-entered categorical data, which reflects the kind of real-world data cleaning work analysts actually do, not just polished example datasets.
