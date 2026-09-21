# PRIMER — how this plan works

The companion to the weekly calendar. Read once; return when a week feels arbitrary.

**Weeks:** 197 · Mon 2026-09-21 → Sun 2030-06-30 · Calendared, gated, and honest.

## The idea

This is not a checklist. It is a **sequence**: every week does a small number of things
that make the *next* weeks possible. The plan is slow on purpose. One major new commitment
per term; growth over intensity; rest protected.

## How to read a week

- **Critical Path — Meetings & Applications.** The lane that matters most. Shows the
  application/meeting *due* this week, the enabling action to *start now* (with its lead
  time), and what is coming over the horizon.
- **Academic / Course Milestones.** Term events — starts, add/drop, withdrawal, exams —
  plus a midterm estimate. Real published UCalgary dates where they exist.
- **Financial Action Items.** Funding, reporting, fees.
- **Training & Artistic Schedule.** Violin, BJJ, swim, yoga — with real venues.
- **Technical & Portfolio Sprints.** The work that builds the portfolio (plus LeetCode).
- **Weekly Time Budget.** Explicit hours, including protected personal/rest time.
- **Gate & Feedback.** The phase gate (what must be TRUE to advance) and one leading
  indicator to watch.

## Gates, not dates

A phase ends when its **gate criteria are true** — not when a date passes. The gate is the
prerequisite for the next phase. If a gate is not met, repair it before advancing.

| Phase | Window | Where | What |
|---|---|---|---|
| 1 | 2026-09-21 → 2026-12-31 | 100 Howse Terrace, Calgary | Pre-Launch (applications + prep) |
| 2 | 2027-01-01 → 2027-04-30 | Calgary | Winter 2027 Open Studies |
| 3 | 2027-05-01 → 2027-08-31 | Calgary | Spring/Summer 2027 Bridge |
| 4 | 2027-09-01 → 2027-12-31 | Calgary | Fall 2027 BSc CS Transition |
| 5 | 2028-01-01 → 2028-04-30 | Calgary | Winter 2028 + Exchange Application |
| 6 | 2028-05-01 → 2028-08-31 | Beijing | Beijing AI/Software Co-op |
| 7 | 2028-09-01 → 2029-06-30 | Beijing | Beijing Exchange (CLIC) |
| 8 | 2029-07-01 → 2029-08-31 | Beijing / Shenzhen | Beijing/Shenzhen Internship |
| 9 | 2029-09-01 → 2030-06-30 | Calgary | Final Year |

## The prerequisite chain

| Track | Sequence |
|---|---|
| Programming | C++ & Python fundamentals -> ROS2 nodes -> Isaac Sim -> Project 1 (VLM->kinematics) -> Project 2 (LLM agent) -> Capstone |
| Algorithms | Blind 75 -> NeetCode 150 (pass 1) -> timed pass 2 -> contests -> timed pass 3 -> interviews |
| Chinese | Prep -> CHIN 205 -> 207 -> 301 -> 303 (~HSK 4) -> exchange -> HSK 5 |
| Academic | Open Studies -> Admission Guarantee -> BSc CS major -> co-op -> exchange -> final year -> capstone |
| Money | Income Support (to Dec 2026) -> Student Aid full-time stream (Jan 2027+) via >=9 units / approved co-op |

## Daily minimums

The habit grid's tick marks are the floor, not the ceiling:

- Violin — **45 min** (30 in work-term phases), every day.
- BJJ — **3×/wk** (Mon/Wed/Fri), one live round minimum.
- Swim — **3×/wk** (Tue/Thu/Sat), log the distance.
- Yoga — **3×/wk** (Sun/Tue/Thu), active recovery, not extra load.
- LeetCode — **1 new + 1 review**, accepted, daily.
- Project — **a real commit pushed**, weekend focus.
- Personal — **protected rest, actually taken**.

## Standing rules

- **No coursework before January 2027.** Phase 1 (now → Dec 2026) is an application and
  preparation season.
- **Never invent a date.** Published UCalgary values are used where they exist and labelled
  *tentative* or *provisional* otherwise. Unpublished add/drop, withdrawal, exam and tuition
  dates are marked, not guessed.
- **The real admission gate** is the **Undergraduate Admission Guarantee** (18 Open Studies
  units + GPA ≥ 2.50 Science + English 30-1) — not a "10-course" rule.
- **Full-time (≥ 9 units) keeps Alberta Student Aid active** from Jan 2027.

## How the calendar stays current

A dedicated planning agent (see `AGENT.md`) runs every morning:

1. Re-checks open opportunity windows (hackathons, grants, research, jobs, auditions).
2. Flags matches onto the calendar, with the exact link and the reason it fits.
3. Confirms or corrects any tentative/provisional date as the source publishes it.
4. Re-sequences the near weeks if a gate slips.

The opportunity feed lives in `opportunities.json` — that is the file the agent edits.
Knowledge (what an item means, how to do it, who to contact) lives in `knowledge.py`.
