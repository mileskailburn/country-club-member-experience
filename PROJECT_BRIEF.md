# Club Member Engagement Platform — Project Brief

> Working document for the FCCC exploration and build. Last updated: 2025-06-17.

## Status

| Phase | Status |
|-------|--------|
| Phase 0: Discovery exports | **In progress** — GM email sent requesting sample CSVs (60–90 days) |
| Phase 0: Data profiling | Pending receipt of exports |
| Phase 1: Platform build | Blocked on board sign-off after discovery |
| Railway / Next.js app | Not started — target stack for Phase 1 |

## Context

Miles Kailburn serves on the board of **Fort Collins Country Club (FCCC)** as treasurer. The club uses two primary systems:

- **ForeTees** (ClubSystems Group / Clubessential Holdings) — golf tee times, court reservations, events, member CRM
- **Northstar** (Northstar Technologies / Jonas Club Software family) — back office, F&B/dining, accounting, member billing, member website/app

**Goal:** Extract member engagement data, understand what's available, and build a platform that delivers insight back to the club. Longer-term thesis: this foundation could power additional platforms or commercial products through a **separately-owned company**, with appropriate governance.

---

## Governance and Structure

Miles is a board member at FCCC. Any commercial application runs through a separately-owned company. Conflict of interest must be handled, not minimized.

### Operating principles

1. Miles discloses the relationship to the board and recuses from any vote that materially benefits the separate entity.
2. The club gets defined value in Phase 1 regardless of whether commercialization ever happens.
3. All data access is authorized in writing by the club. No screen scraping, no informal credential sharing, no workarounds.
4. Phase 1 uses CSV exports approved by the GM. No API integrations or vendor partner programs until Phase 1 delivers value and the board agrees to expand scope.
5. The separate company registers as a single-client partner if/when vendor-level access is needed. Full marketplace partner status deferred until there's a reason.

### Future commercialization models (decision deferred until Phase 1 results)

| Model | Summary |
|-------|---------|
| **Vendor** (most likely start) | Separate company sells services/tooling to club; club retains data ownership; external use anonymized/aggregated |
| **License** | Club owns dataset and IP; separate company licenses under defined terms with royalty/equity/service discount back |
| **Joint venture** | Club and separate company co-own a new entity |

---

## Platforms

### ForeTees
- Tee times, court reservations (tennis/pickleball/paddle), dining reservations (some configs), events, member CRM
- APIs exist but partner-only, gated through business development. No public developer portal.

### Northstar
- Back office, F&B/dining, accounting, member billing, member website/app
- API capability exists but not publicly documented or self-serve. Same partner-program model.

**Implication:** API access is achievable but slow and gated. CSV exports through admin reporting are immediately available with GM authorization. **Phase 1 is built entirely on CSV.** API replaces manual upload later.

---

## Phase 0: Discovery Exports

**Goal:** Understand shape, quality, and joinability of data before designing anything.

### Files requested (60–90 day sample, stock reports only)

**From ForeTees:**
- Tee times with member number and name
- Court reservations with member number and name

**From Northstar:**
- Member number list
- Member list / roster
- Dining charges with member number

**Delivery:** CSV preferred. Email or shared drive. Whatever format is easiest for staff.

**Authorization path:** Email to GM → GM approves or routes to controller and head of racquets/golf operations → Miles works with staff directly once approved.

### Discovery analysis plan

One-page data profile per file covering:
- Row count and date range
- Unique member count
- Null rates per column
- Distinct value counts for categorical fields
- Joinability to member roster (% of activity records matching roster ID)
- Date/time format and timezone handling
- Test records, staff bookings, comp transactions, or other noise
- Family/household structure (spouse activity under primary ID vs separate)

### Key questions to answer

1. How are no-shows and cancellations coded in each system?
2. How do member IDs reconcile between ForeTees and Northstar?
3. What is cardinality of activity per member per week (individual vs rolling-window scoring)?
4. Which fields are populated consistently enough for metrics?
5. What reference data (venue codes, membership types, reservation statuses) is needed?

**Deliverable:** Written data profile + recommendation on what Phase 1 should measure and what it shouldn't try yet.

---

## Phase 1: Platform Foundation

Built on weekly manual CSV uploads. Designed so API integration drops in as a replacement for the upload step.

### Architectural principles

- Separation of ingestion, storage, transformation, and presentation — each layer swappable
- Raw files preserved on ingest; all transformations downstream
- **Member ID is the primary join key** — names for display only, never as keys
- Family/household rollups as first-class concept
- All metrics defined in one place with documented logic — no inline dashboard calculations

### Suggested stack (subject to change after discovery)

| Layer | Choice |
|-------|--------|
| Hosting | Railway |
| App | Next.js |
| Storage | Postgres or DuckDB (DuckDB for prototype, Postgres if it graduates) |
| Ingestion | File watcher or scheduled job — validate schema, load to raw tables |
| Transformation | dbt or plain SQL with version control (staging → dimensional → metric layers) |
| Presentation | Next.js dashboards (club-level; member-facing deferred) |
| Auth | Deferred — Phase 1 internal use only |

### Initial functional scope (refined after discovery)

- Club-level engagement dashboard: activity by area, membership type, month
- Per-member engagement profile: rounds, court time, dining covers, events, trend over time
- Household rollup view
- Inactive member identification (30/60/90 days)
- High-engagement member identification
- Cross-amenity patterns (golfers who dine, tennis players who use pool, etc.)

### Explicitly out of scope for Phase 1

- Member-facing app or portal
- Real-time data
- Predictive modeling or churn scoring
- Financial analysis beyond activity counts
- Marketing automation or outreach triggers
- HubSpot, Mailchimp, or other integrations

---

## Phase 2+ (placeholder — not committed)

- API integration replacing manual CSV upload (single-client partner status with ForeTees/Northstar)
- Member-facing engagement view, possibly gamified
- Predictive churn and engagement scoring
- Benchmarking across multiple clubs (if commercialization approved)
- Event recommendation engine
- Staff-facing operational tools (proactive outreach lists)

Scoped only after Phase 1 delivers board-valued results.

---

## Working Assumptions

- Club contracts give FCCC ownership of member activity data and right to extract for internal use (verify before external use)
- Stock reports produce CSVs with member IDs attached (or shift to closest available report)
- Member IDs are stable across time within each system
- GM is the right initial point of contact

## Open Questions (discovery)

- Are ForeTees and Northstar member IDs the same, or need name/email reconciliation?
- Does the club have existing data warehouse or BI tooling touching these systems?
- Existing reports to learn from or replicate?
- What does the club currently do with engagement data, if anything?
- Member data privacy policy or handbook clause governing internal use?

---

## Infrastructure Notes

This platform is **independent of OTM infrastructure** for governance reasons (club work, not OTM work). Can borrow architectural patterns from OTM: file-watcher ingestion, dbt-style transformation, raw vs modeled separation.

---

## Immediate Next Steps

- [x] Send GM email requesting Phase 0 discovery exports
- [ ] Identify staff person who runs reports in each system
- [ ] Receive sample files
- [ ] Profile files and write data profile document
- [ ] Bring profile and Phase 1 recommendation to board
- [ ] Begin Phase 1 build after board sign-off
