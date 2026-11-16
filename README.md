# Vultr vs Google Cloud: Is Vultr Cheaper at Your Instance Size? 30 Matched Tiers, Stopped-Instance Billing, and When GCP Wins

Across 30 matched Compute Engine and Vultr Cloud Compute tiers, Vultr is cheaper in 24 and loses 6. Where it wins, Vultr is 24.6% cheaper on average, with the widest gap at 45.6% on 8 vCPU / 32 GB. The six losses are just as specific: Google's E2 tiers beat Vultr's shared AMD plans by 18.5% at 1 vCPU / 4 GB, 2 vCPU / 8 GB, 4 vCPU / 16 GB and 32 vCPU / 128 GB.

So "is Vultr cheaper than Google Cloud" depends on the instance size you actually need — and on a second variable that most comparison tables omit: what the provider charges after you stop the machine.

Both numbers matter for a SaaS backend, an API tier or a CI runner. This breakdown covers the matched-tier price data, Vultr's full Cloud Compute line-up, the billing mechanics that flip the math on bursty workloads, and the cases where staying on Google Cloud is the correct call.

> **For most teams running a 4 vCPU-and-up production service:** Vultr VX1 General Purpose `vx1-g-4c-16g` is $87.60/month for 4 dedicated AMD vCPUs and 16 GB RAM. The closest-spec Google N2 tier, `n2-standard-4`, lists at $141.79/month. That is roughly $54 a month, or about $650 a year, on one box.
> 👉 [Check the VX1 4 vCPU / 16 GB price](https://www.vultr.com/products/vx1-compute/?ref=9738262-9J)

## Vultr Cloud Compute pricing: the whole line-up

Vultr's public Cloud Compute catalogue spans 122 pricing tiers, from 1 vCPU / 0.5 GB at $2.48/month up to 96 vCPU / 384 GB at $2,102.40/month. The plans split into three shared-vCPU families (Regular, High Frequency, High Performance) and three dedicated-vCPU lines (Optimized Cloud Compute, VX1 General Purpose, VX1 Memory Optimized).

Prices below are on-demand, USD, monthly, and reflect what you pay when an instance runs around the clock.

**Shared vCPU**

| Plan | vCPU / RAM | Monthly | Order |
| --- | --- | --- | --- |
| `vc2-1c-0.5gb-v6` | 1 / 0.5 GB | $2.48 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `vc2-4c-8gb` | 4 / 8 GB | $40.00 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `vhp-4c-8gb-amd` | 4 / 8 GB | $48.00 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `vhf-4c-16gb` | 4 / 16 GB | $96.00 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `vc2-8c-32gb` | 8 / 32 GB | $160.02 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `vhf-8c-32gb` | 8 / 32 GB | $191.99 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `vc2-16c-64gb` | 16 / 64 GB | $320.03 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |

**Dedicated vCPU**

| Plan | vCPU / RAM | Monthly | Order |
| --- | --- | --- | --- |
| `voc-g-1c-4gb-30s-amd` | 1 / 4 GB | $30.00 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `vx1-g-2c-8g` | 2 / 8 GB | $43.80 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `voc-g-2c-8gb-50s-amd` | 2 / 8 GB | $60.01 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `voc-c-4c-8gb-75s-amd` | 4 / 8 GB | $80.00 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `vx1-g-4c-16g` | 4 / 16 GB | $87.60 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `voc-g-4c-16gb-80s-amd` | 4 / 16 GB | $120.01 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `vx1-g-8c-32g` | 8 / 32 GB | $175.20 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `voc-g-8c-32gb-160s-amd` | 8 / 32 GB | $240.02 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `vx1-g-16c-64g` | 16 / 64 GB | $350.40 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `voc-g-16c-64gb-320s-amd` | 16 / 64 GB | $479.97 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `vx1-g-32c-128g` | 32 / 128 GB | $700.80 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `voc-g-32c-128gb-640s-amd` | 32 / 128 GB | $960.02 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `vx1-g-48c-192g` | 48 / 192 GB | $1,051.20 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `vx1-m-48c-384gb` | 48 / 384 GB | $1,401.60 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `vx1-g-64c-256g` | 64 / 256 GB | $1,401.60 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |
| `vx1-m-96c-768gb` | 96 / 768 GB | $2,803.20 | [View plan](https://www.vultr.com/pricing/?ref=9738262-9J) |

Two naming details change what you get. The `-30s`, `-80s`, `-160s` and similar suffixes on Optimized Cloud Compute plans denote included local NVMe storage. VX1 plans without a numeric suffix boot from high-performance NVMe block storage that is billed separately (around $0.10/GB-month); plans carrying `-120s`, `-240s`, `-480s` and so on include local NVMe instead. A `vx1-g-4c-16g` at $87.60 and a `vx1-g-4c-16g-240s` at $111.69 are the same CPU and RAM with a different storage arrangement, not a discount tier.

VX1 General Purpose and Memory Optimized use dedicated AMD EPYC vCPUs and are documented as supporting nested virtualisation. Optimized Cloud Compute is also dedicated. The three `vc2`/`vhp`/`vhf` families are shared vCPU — fine for a web tier, less fine for anything latency-sensitive.

## Where Vultr vs Google Cloud pricing actually diverges

Third-party matched-tier tracking from September 2026 compared 30 equivalent on-demand configurations, default region, USD. Vultr won 24, Google won 6, no ties. Across the comparable set, hourly prices ranged from $0.0335 to $5.92.

The pattern in the wins is consistent: the gap widens as you move up in vCPU count, and it widens most against Google's N2 and C3 families.

| Spec | Google Cloud | Vultr | Cheaper |
| --- | --- | --- | --- |
| 1 vCPU / 4 GB | `e2-medium` $24.46/mo | `voc-g-1c-4gb-30s-amd` $30.00/mo | Google, 18.5% |
| 2 vCPU / 8 GB | `e2-standard-2` $48.92/mo | `voc-g-2c-8gb-50s-amd` $60.01/mo | Google, 18.5% |
| 2 vCPU / 8 GB | `n2-standard-2` $70.90/mo | `vcg-a16-2c-8g-2vram` $43.00/mo | Vultr, 39.4% |
| 4 vCPU / 16 GB | `e2-standard-4` $97.84/mo | `voc-g-4c-16gb-80s-amd` $120.01/mo | Google, 18.5% |
| 4 vCPU / 16 GB | `n2-standard-4` $141.79/mo | `vx1-g-4c-16g` $87.60/mo | Vultr, 38.2% |
| 4 vCPU / 16 GB | `c3-standard-4` $147.17/mo | `vhf-4c-16gb` $96.00/mo | Vultr, 34.8% |
| 8 vCPU / 32 GB | `c3-standard-8` $294.35/mo | `vc2-8c-32gb` $160.02/mo | Vultr, 45.6% |
| 32 vCPU / 128 GB | `n2-standard-32` $1,134.34/mo | `vx1-g-32c-128g` $700.80/mo | Vultr, 38.2% |
| 32 vCPU / 128 GB | `e2-standard-32` $782.69/mo | `voc-g-32c-128gb-640s-amd` $960.02/mo | Google, 18.5% |

Read the Google wins carefully, because they are not random. Every one of the six lands on a Google E2 tier or a Tau T2D tier compared against a Vultr shared-AMD plan. E2 is Google's efficiency-optimised shared-core family — Google's own answer to cheap general-purpose compute — and it holds a flat 18.5% edge over Vultr's `voc-g` line at every size from 1 vCPU to 32 vCPU. If your workload is a low-traffic WordPress install, a staging box, a small internal API or a dev environment, E2 is the cheaper option and there is no configuration of Vultr's catalogue that changes that.

Above 4 vCPU on a production service, the picture inverts. VX1 against N2 is 38.2% cheaper at 4, 32, 48 and 64 vCPUs — a consistent ratio, not a one-off. Against N2D the gap narrows to 9.5%, and against N4 to 16.2%. Google's newer N4 generation has closed most of the distance, so if your existing deployment already uses N4, migration savings will be smaller than a comparison against N2 suggests.

Compute-optimised shapes show the widest spread. Vultr `vc2-8c-32gb` at $160.02 against `c3-standard-8` at $294.35 is 45.6% — but note you are comparing a shared-vCPU Vultr plan to a dedicated-core Google plan, so the raw percentage flatters Vultr somewhat. On the dedicated side, `vhf-4c-16gb` against `c3-standard-4` is 34.8%, which is the fairer read.

Vultr's own marketing makes a different claim with a different denominator: VX1 is "up to 34% more affordable per vCPU compared to equivalent efficiency-optimized Google Cloud plans," meaning the E2 family specifically. That is a vendor figure and it does not reconcile with the independent E2 comparison above at small sizes — different plan pairing, different methodology. Treat the two as separate numbers and do not blend them into one savings estimate. Vultr also states that switching delivers "30–50% savings on common core cloud workloads" and illustrates it with a stack costing above $51,000/month on Google Cloud coming in under $38,000/month on Vultr. No configuration is disclosed for that example, so it is a vendor illustration, not a benchmark.

👉 [See Vultr's full price list by plan and region](https://www.vultr.com/pricing/?ref=9738262-9J)

## The billing rules that decide the real bill

Sticker price per month assumes a machine runs all month. Plenty of workloads do not, and this is where the two providers behave very differently.

**Billing granularity.** Google Compute Engine bills per second with a one-minute minimum. Vultr bills every started hour. A 12-minute CI job costs 12 minutes on Google and a full hour on Vultr. If you run ephemeral containers, short-lived test environments or per-request sandboxes, hourly rounding can erase the per-hour price advantage entirely.

**Monthly cap.** Vultr's shared and Optimized Cloud Compute plans are capped at 672 hours per month, at which point billing stops for that instance. 672 hours is 28 days, so an instance running continuously pays the monthly price and no more — the cap protects you from being charged for 730 hours at the hourly rate. VX1 is billed on actual hours used with no cap, which matters if you run VX1 intermittently.

**Stopped instances.** This is the one that surprises people. Google stops billing for CPU and RAM when an instance is stopped; disks and external IPs keep charging. Vultr has no suspend state: a stopped instance keeps billing at full rate until you destroy it. Shutting down a $120/month Vultr box for three weeks to save money costs you $120 anyway. If your pattern is "spin up for a demo, tear down, rebuild next month," budget for the destroy step, not the stop step.

**Snapshots and backups.** Google supports on-demand and scheduled snapshots. Vultr's snapshots are manual and can take up to 30 minutes to complete; automated scheduled backups are a paid add-on rather than a default. A pre-migration snapshot of a large volume is not instant on either platform, but on Vultr you are scheduling it, not setting it and forgetting it.

**Firewall direction.** Vultr's firewall filters inbound traffic only. Outbound rules have to be configured inside the guest OS. Google handles egress filtering through VPC firewall rules by IP and port, with domain-based egress requiring the paid Cloud NGFW Standard tier. Neither is a blocker, but if your compliance model assumes a provider-level egress deny rule, Vultr will not give you one.

**Quotas.** Google caps vCPU counts per region, varies the cap by project, applies a global CPU ceiling to some new projects, and does not allow free-trial accounts to request increases. Vultr does not publish how many instances a new account can launch — you contact support to raise it. Both involve a support ticket on day one; only one of them has a documented ceiling you can plan against in advance.

👉 [Check how each Cloud Compute plan is priced per hour and per month](https://www.vultr.com/pricing/?ref=9738262-9J)

## Which workloads should stay on Google Cloud

Three categories where the Vultr price advantage does not apply or does not matter.

**Anything that needs Arm64.** Vultr offers Linux and Windows Server on shared or dedicated AMD EPYC vCPUs. There is no Arm64 option. Google covers Intel, AMD and Arm across its instance families. If you are running Graviton-style cost optimisation or an Arm-native build pipeline, that alone settles it.

**Anything that needs Google's data stack.** Reviewers on TrustRadius describe the strongest Google Cloud use case as integration with BigQuery and cross-team data sharing inside the same permission model, and say it works less well when half the stack lives elsewhere. Compute price is a small line item next to egress and re-engineering costs if your analytics, Pub/Sub or Vertex workloads are already wired into Google's IAM.

**Short-lived, high-churn compute.** Per-second billing plus zero CPU charge on stopped instances makes Google structurally cheaper for RL rollouts, burst code interpreters, browser-use agents and similar workloads that start and stop constantly. The hourly price looks worse; the monthly invoice often is not.

Where Vultr is the clear answer: steady-state 4 vCPU-and-up services, 24/7 dev boxes, self-managed databases on dedicated vCPUs, and GPU work that needs AMD silicon. Vultr carries Nvidia L40S, A100, H100, GH200 and B200, plus AMD MI300X, MI325X and MI355X. Google's GPU line runs from T4 and L4 up through H100, H200, B200 and GB200 — deeper Nvidia coverage, no AMD Instinct. If your inference stack is tuned for ROCm, Vultr is one of the few mainstream providers that will rent you the hardware.

Geographically, Google runs 43 regions and 130 zones against Vultr's 33 locations across six continents. For most teams 33 is enough; if you need a specific in-country presence for data residency in a market Vultr does not cover, that decides it.

## What the consoles and support actually feel like

Reviews on TrustRadius describe Google's console as powerful and hard to navigate — "like a maze," with too many menus for new users — while Vultr's is repeatedly described as simple, with fast provisioning and a wide OS selection. Both sets of reviews carry incentive disclosures, and the same reviewer text appears on more than one product page, so treat these as long-standing themes rather than clean attribution.

Two recurring complaints about Google are worth planning around. Initial vCPU quotas are low and require multiple increase requests. And human technical support requires the paid Premium tier; the default is knowledge base and community forums. Vultr reviewers describe support as responsive, though the counterpoint from the same pool is that VPS hosting is self-managed by design — you own the OS, the patching and the incident response. On Vultr's shared-vCPU plans, reviewers also flag noisy-neighbour effects, which is the expected trade-off for shared cores and a reason to pick a dedicated plan for anything with a latency SLA.

## Promotional credit, and what it does not cover

Vultr's coupons page currently lists several new-customer offers. The largest is code `FLY300VULTR`, which grants $300 in free credit. The stated terms: promotional credit applies to select products only, cannot be combined with any other offers, restrictions and terms apply, new customers only. `VULTRMATCH` doubles a first deposit dollar for dollar up to $100 under the same conditions. Neither is a cash refund, and "select products" is the clause to check in the billing panel before you deploy — GPU and bare metal are commonly excluded from this class of credit.

👉 [See the current Vultr promo codes and their terms](https://www.vultr.com/coupons/?ref=9738262-9J)

## FAQ

**Is Vultr cheaper than Google Cloud?**
On 30 matched tiers, Vultr is cheaper in 24 and 24.6% cheaper on average where it wins. It loses on 1 vCPU / 4 GB, 2 vCPU / 8 GB and 4 vCPU / 16 GB against Google's E2 family, where Google is 18.5% cheaper. Above 4 vCPU against N2, Vultr is 38.2% cheaper.

**Does Vultr charge for stopped instances?**
Yes. Vultr has no suspend state; a stopped instance bills at full rate until destroyed. Google stops charging for CPU and RAM on a stopped instance but continues charging for disks and external IPs.

**Can I run Windows on Vultr?**
Windows Server is available and carries a separate licence fee on top of the plan price. Check the specific plan family before deploying — not every Vultr line lists Windows support.

**How many regions does each provider have?**
Vultr operates 33 locations across six continents. Google Cloud runs 43 regions and 130 zones.

**Does Vultr support nested virtualisation?**
It is documented on VX1 and on bare metal. Google supports it on selected Intel machine families and N4D only — not on E2, Arm or other AMD families.

**Can I migrate without downtime?**
Neither provider offers a managed migration path between the two. A snapshot-based move works, but Vultr snapshots are manual and can take up to 30 minutes, so plan a maintenance window rather than an instant cutover.

## The decision rule

If you run 1–2 vCPU boxes for low-traffic sites, staging or internal tooling, stay on Google E2 — it is 18.5% cheaper at those sizes and the gap is structural, not promotional.

If you run 4 vCPU or more for a production service, price Vultr VX1 against your current N2, N2D or N4 shapes before renewing anything. At 4 vCPU / 16 GB that is $87.60 against $141.79, and the ratio holds at 32, 48 and 64 vCPUs.

If your compute starts and stops more than a few hundred times a day, run the monthly arithmetic on both before choosing — per-second billing will likely beat a lower hourly rate.

If you need AMD Instinct GPUs, Arm64, or a data stack already inside BigQuery, the price table is not the deciding factor.

Two things to confirm before you deploy anywhere: the billing granularity that matches your workload's lifetime, and the region that matches your users. Both are on the plan page, and both change the number more than the headline price does.

👉 [Start from Vultr's Cloud Compute catalogue and pick a region](https://www.vultr.com/products/cloud-compute/?ref=9738262-9J)
