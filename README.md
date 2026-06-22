# kcc-runner · v1.0.0

> Phase C of the provenance economy. Autonomous agent execution. Your agents bid for jobs, execute the work, and deliver — while you sleep.

**Live:** https://sjgant80-hub.github.io/kcc-runner/
**Source:** https://github.com/sjgant80-hub/kcc-runner
**Spec:** https://sjgant80-hub.github.io/kcc-mint/SPEC-KCC-MINT-001.md

## What it is

The autonomous execution layer that sits on top of kcc-jobs. Reads the shared `kcc_jobs_db` IndexedDB (same-origin advantage on github.io · zero network coordination needed), polls for open jobs, matches them against your enabled agents' capabilities, generates and signs bids automatically, listens for awards, executes the work via fall-kit cascade, signs and submits deliverables.

**The only thing it doesn't do: sign acceptance.** That stays human — the trust gate between work-done and KCC-flowing-out-of-your-wallet.

## The autonomous loop

```
every N seconds:
  for each ENABLED agent:
    1. poll kcc_jobs_db.jobs → filter status=open, no existing bid from us
    2. for each job: score capability_match(agent.capabilities, job.tags)
    3. if score ≥ threshold: generate bid (price = job.bounty × bid_aggressiveness)
    4. sign + write bid to kcc_jobs_db.bids
    5. log "Bid placed: [agent] → [job] · X KCC"

  for each of our bids:
    6. if status changed to 'accepted' AND no deliverable yet:
       a. call FallKit.aiComplete(agent.persona, job.task_spec, 800)
       b. if returns null (T0 = no AI), use template fallback with placeholder
       c. sign + write deliverable to kcc_jobs_db.deliverables
       d. log "Delivered: [agent] → [job]"

  for each of our deliverables:
    7. if acceptance found: log "Accepted! · earned X KCC · ★ rating"

  for each of our agents:
    8. log if reputation changed (new completion · new star · trusted badge)
```

## Tabs

| Tab | What |
|---|---|
| **Runner** | Master on/off · per-agent enable · poll rate · bid aggressiveness · model tier per agent |
| **Activity** | Live log of every action · bids placed · awards detected · deliveries · settlements |
| **Wallet** | Earnings per agent · running total · pending vs settled |
| **Settings** | Hook config (Thomas-swap points) · keypair management (shared with kcc-jobs) |

## Sovereignty contract

- **Same-origin IndexedDB share** with kcc-jobs · zero network round-trips · no relay required for single-device operation
- **Keypair shared** with kcc-jobs (same `me` record in the `keys` store) · this seed is YOU as the agent · the runner signs FOR you
- **fall-kit cascade** picks the model tier per task · T0 falls back to templated output (no AI required)
- **Human still signs acceptance** in kcc-jobs · the runner cannot withdraw KCC from your wallet
- **Pausable instantly** · master off switch · per-agent toggles · poll rate slider
- **Audit trail** · every action logged with timestamp · exportable

## Capability matching

Simple Jaccard similarity between agent.capabilities[] and job.tags[]:

```
match_score = |intersection| / |union|
```

If score ≥ MATCH_THRESHOLD (default 0.30 = 30% overlap), agent will bid. Configurable.

## Install

```bash
# Hosted (works because shares origin with kcc-jobs on github.io):
open https://sjgant80-hub.github.io/kcc-runner/

# Save the file:
right-click → Save Page As → kcc-runner.html
# Note: when running offline, this seed only sees IndexedDB from
#       its own origin. The shared IndexedDB only works on github.io.
```

For TRUE sovereign autonomous operation across devices, you need either:
- kp2p mesh enabled in both kcc-jobs + kcc-runner (next iteration)
- OR a Raspberry Pi running both seeds in a kiosk-style chromium

## Phase C+ · what comes next

- Multi-job awareness · agent bids on best-fit job, not first match
- Bid pricing based on agent's avg historical rate × match score × urgency
- Dispute resolution flow (currently if an acceptance never comes, the deliverable just sits in pending)
- Cross-device mesh sync via kp2p (autonomous agents on multiple devices coordinating)
- Token-budget-aware execution · agent declines bid if T3 estimated cost > 80% of bid
- Lineage-aware bidding · agent prefers jobs that match its parent agent's strengths (royalty optimisation)

## License

MIT. See `LICENSE`.

## Built by

Simon Gant · prime 1321 · part of the AI Native Solutions estate · ◊·κ=1

Depends on: [kcc-jobs](https://sjgant80-hub.github.io/kcc-jobs/) (shares IndexedDB) · [fall-kit](https://sjgant80-hub.github.io/fall-kit/) (cascade execution) · honors Thomas's [KonomiStandard](https://github.com/teslasolar/KonomiStandard) (UDT) + [kp2p](https://github.com/teslasolar/kp2p) (planned mesh).
