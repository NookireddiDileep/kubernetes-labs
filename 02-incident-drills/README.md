# 02 · Incident drills: live outages, diagnosed under time pressure

Timed break-fix drills on **live, multi-node Kubernetes clusters** with real production-style outages, practised on a live-outage training platform (InfraThrone DevOps Park). They cover what my single-node K3s lab can't: multi-node networking, scheduling across nodes, and multi-cause incidents.

## Method (every drill)

1. Read the incident brief and **start a 30-minute timer**.
2. Diagnose using only `kubectl`, events, logs and metrics, and **talk through my reasoning out loud**, as in an interview.
3. Fix it, verify it, then write the RCA.
4. The drills I learn the most from get a full RCA here, **in my own words**. The platform's mission text and solutions are their content and are not copied here.

**Levels:** 🟢 CoreOps (foundation) → 🟡 BattleOps (multi-cause) → 🔴 EliteOps (advanced)

## Drill log

| # | Date | Incident (my own title) | Area | Level | Time to diagnose | Root cause (one line) | RCA |
|---|---|---|---|---|---|---|---|

<!-- Add a row per drill. Write a full RCA (TEMPLATE.md) for the ones worth keeping: DRILL-NNN-<slug>.md -->

## What I'm tracking

- **Time to root cause** for each area (target: under 15 minutes)
- **The first command I ran:** was it the right one?
- **Repeated weak spots**, which become my next study topics
