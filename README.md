# ProblemAnalysisDashboard

# Root Cause Analysis (RCA) Power BI Toolkit

An interactive Power BI application that operationalizes classic quality-management frameworks — **Fishbone (Ishikawa) diagrams**, **5-Why analysis**, and **Pareto (80/20) prioritization** — as a structured, filterable analytical model rather than static whiteboard diagrams.

---

## 🎯 Project Overview

Most organizations run root cause analysis as a one-time workshop exercise: a fishbone diagram gets drawn on a whiteboard, a few "5 Whys" get scribbled down, and the output lives in a slide deck that's never touched again. This project turns that process into a **living, queryable data model** where:

- Problems are logged and scored systematically (not just gut-feel prioritized)
- Root causes are traced through structured cause → sub-cause → contributor hierarchies
- The "5 Whys" for any cause are captured as discrete, level-tagged rows — not free text
- Pareto analysis automatically identifies the "vital few" categories driving the majority of issues
- Everything is cross-filterable: click a goal, a category, or a problem, and every other view (causes, whys, priority scores) updates accordingly

The result is an RCA framework that scales across many problems and goals simultaneously, supports re-analysis over time, and produces defensible, data-backed prioritization instead of anecdotal ranking.

## 🧩 Methodology Implemented

| Framework | How it's modeled |
|---|---|
| **Goal Setting** | A `Goals` table anchors every problem to a strategic objective, timeline, and priority level |
| **Brainstorming & Scoring** | Problems are logged with Impact, Urgency, and Effort ratings (1–5 scale); a weighted **Problem Score** and **Priority Rank** surface the highest-value problems to tackle first |
| **Fishbone / Ishikawa** | The `FishBone` table captures Category → Cause → Sub-Cause → Contributor for each problem, linked back to its Brainstorming entry and Goal |
| **5-Why Analysis** | The `Why` table stores each "why" as its own leveled row per sub-cause, enabling a dynamic **Root Cause** measure that always surfaces the deepest ("terminal") why, plus a **Why Narrative** measure that reconstructs the full why-chain as readable text |
| **Pareto Analysis** | A dedicated set of measures ranks Fishbone categories and individual causes by frequency, calculates cumulative %, and identifies exactly how many categories are needed to reach the 80% threshold — with a ready-made narrative string ("4 of 6 categories") for a Smart Narrative visual |

## 🗂️ Data Model

**6 tables, 4 relationships:**

```
Goals ──────────────┐
  ▲                  │
  │ (Goal Name)       │ (Goal Name)
  │                  │
Brainstorming ───────┘
  ▲
  │ (Problem ID / Problem_ID)
  │
FishBone
  ▲
  │ (Fishbone_ID)
  │
Why
```

- **Goals** – strategic objectives with timeline and priority (L/M/H)
- **Brainstorming** – logged problems with Impact / Urgency / Effort ratings and a calculated priority rank
- **FishBone** – cause taxonomy (Category, Cause, Sub-Cause, Contributor) per problem
- **Why** – leveled 5-Why statements per sub-cause, with a `WhyAnchor` flag marking the terminal why
- **Measures Table** / **claude measures** – 29 DAX measures organized by function (Fishbone summary stats, Pareto analysis, Why/Root-Cause text construction, prioritization scoring)

## 📐 Notable DAX Techniques

- **Dynamic root-cause resolution** — surfaces whichever "why" sits at the deepest level for the currently selected sub-cause, without hardcoding a level number:
  ```dax
  Root Cause =
  VAR SelectedSubCause = SELECTEDVALUE(Fishbone[Sub_Cause])
  VAR MaxLevel = CALCULATE(MAX('Why'[Why_Level]), ALLSELECTED('Why'))
  RETURN IF(ISBLANK(SelectedSubCause), BLANK(),
      CALCULATE(SELECTEDVALUE('Why'[Why_Text]), 'Why'[Why_Level] = MaxLevel))
  ```
- **Context-aware Pareto drill-down** — category-level Pareto ranks re-calculate at the individual-cause level using `ALLSELECTED`, so cross-filtering from a category chart into a cause chart preserves proper cumulative-% math
- **Narrative-ready text measures** — `Why Narrative` and `Vital Few Summary` pre-format DAX output into plain-English strings for direct use in Smart Narrative / natural-language visuals
- **Weighted prioritization scoring** — combines Impact, Urgency, and Effort ratings into a single sortable Problem Score, feeding a stable Priority Rank column

## 🛠️ Tech Stack

- **Power BI Desktop** — semantic model, DAX, report visuals
- **Model authored/iterated via Tabular Object Model (TOM) through an MCP-based modeling workflow** — measures, tables, and relationships were built and refined programmatically alongside manual modeling

## 📌 Skills Demonstrated

- Business process modeling (Fishbone, 5-Why, Pareto) translated into a relational data model
- Intermediate–advanced DAX (context transition, `ALLSELECTED`, dynamic text construction, ranking/cumulative measures)
- Star-schema-style relationship design with controlled active/inactive relationships
- Structured problem prioritization frameworks (Impact/Urgency/Effort scoring)
- Report/measure organization using display folders for end-user usability

## 🚀 How to Use

1. Open the `.pbix` file in Power BI Desktop
2. Add problems to the **Brainstorming** table with Impact/Urgency/Effort ratings
3. Break down root causes in the **FishBone** table by category, cause, and sub-cause
4. Log the **5 Whys** for each sub-cause in the **Why** table, flagging the terminal why with `WhyAnchor`
5. Use the Pareto visuals to identify which categories to prioritize, and the Priority Score to sequence which problems to tackle first



---

*Built as part of an ongoing personal project applying structured problem-solving methodology to Power BI data modeling.*
