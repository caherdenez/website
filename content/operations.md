---
title: "Reduce avoidable demurrage, detention, and terminal storage exposure"
description: "Focused software for drayage operations: surface charge exposure, prioritize at-risk containers, and support dispatch decisions before costs accumulate."
eyebrow: "Software for drayage operations"
---

Drayage dispatch teams need a clear view of which containers require action now, not another disconnected system to monitor. I build focused operational software that turns tariff rules, container milestones, and dispatch constraints into a prioritized view of the work that can prevent additional charges.

## Where I can help

### Charge exposure

Model demurrage, detention or per diem, and terminal storage as distinct charge windows, including scoped tariffs, free time, tiered rates, and effective dates.

### Operational priorities

Surface current and projected exposure so dispatch teams can evaluate which available move should happen next instead of treating every container as equally urgent.

### Workflow integration

Connect operational data through APIs, synchronization workflows, and internal tools that fit the way a team already works.

## Public evidence

[drayage-watch](https://github.com/caherdenez/drayage-watch) is my public portfolio demo for this problem. It combines a versioned tariff engine, charge-exposure projections, route estimates, and constrained dispatch planning in a FastAPI and React application.

The demo uses synthetic fleet data and cited or illustrative tariff snapshots. It demonstrates the engineering approach, not production deployment or measured customer savings.

[route-planner](https://github.com/caherdenez/route-planner) is a complementary demo behind the route-estimation piece: a standalone Excel-to-map delivery planner built on the same self-hosted OSRM infrastructure, covering overlapping Colombia and southeastern U.S. demo markets. It turns an uploaded stop list into an OSRM-solved delivery order with per-stop ETAs and time-window alerts. The two are separate codebases, not one integrated product, but they demonstrate the same self-hosted routing approach this page's dispatch and route-estimate claims rely on.
