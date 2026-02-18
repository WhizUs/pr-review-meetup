---
theme: "@whizus-labs/slidev-theme-whizus"
date: 17.02.2026
company: WhizUs GmbH
speaker: Julian Zhuang
title: Let the Bot Review it - Reducing Toil with Fossabot
---

# Let the Bot Review it 

Reducing Toil with Fossabot

---
layout: two-cols
---

# Julian Zhuang

**DevOps Engineer @ WhizUs GmbH**

- 🚀 Cloud Native & Kubernetes
- 🔧 CI/CD & GitOps practioner
- 🔒 Supply Chain Security enthusiast

::right::

<div class="flex items-center justify-center h-full">
  <img src="./assets/profil-picture-small.jpg" class="rounded-full w-80"/>
</div>

---
layout: lead
---

# Pull Request Reviews

Why they matter — and why they're hard

---

# What Is a PR Review? | The Basics

A Pull Request Review is a **quality gate** before code reaches production.

Reviewers check for:

- ✅ Correctness — does it do what it should?
- ✅ Security — does it introduce vulnerabilities?
- ✅ Dependencies — are upgrades safe?
- ✅ Breaking changes — will it break existing behavior?
- ✅ Best practices — is the code maintainable?

---

# The Challenge | Manual Reviewing

In practice, manual reviews struggle with:

- 📦 **Dependency updates** — "just a version bump", but what changed upstream?
- ⏱️ **Time pressure** — reviewers skim diffs, approve quickly
- 🔗 **Transitive dependencies** — hidden changes deep in the tree
- 🧩 **Context gaps** — no one reads every changelog for every package
- 🔒 **Security blind spots** — CVEs introduced via minor/patch bumps

> **The more "boring" a PR looks, the more dangerous it can be.**

---
layout: lead
---

# A Real-World Example

Let's look at a Dependabot PR from our CNDA demo application

---

# The PR | CNDA Showcase App



**PR #39**: `chore(deps): bump the npm_and_yarn group with 4 updates`

```
jsonpath    1.1.1  →  1.2.1
lodash      4.17.21  →  4.17.23
qs          6.13.0  →  6.14.2
webpack     5.102.1  →  5.105.2
```

**Statistics**: 1 file changed (`package-lock.json`)

All minor/patch bumps. Would you approve this?

---

<div class="overflow-y-auto h-full">
  <img src="./assets/dependabot_pr.png"/>
</div>

---

**jsonpath 1.2.1**:

<img src="./assets/jsonpath_tags.png"/>

---

**jsonpath commit for 1.2.1**:
<div class="overflow-y-auto h-full">
  <img src="./assets/jsonpath_1_2_1_commit.png"/>
</div>

---

# What's Actually Hiding | Security Issues

| Package | CVE | Severity | Status |
|---------|-----|----------|--------|
| `jsonpath` 1.2.1 | CVE-2025-61140 | 🔴 Prototype Pollution | **No fix available!** |
| `qs` 6.13.0 | CVE-2025-15284 | 🔴 DoS via memory exhaustion | Fixed in 6.14.1+ |
| `lodash` 4.17.23 | CVE-2025-13465 | 🟡 Prototype Pollution | Fixed in this version |
| `webpack` | User info bypass | 🟡 HttpUriPlugin | Fixed in this version |

**Upgrading `jsonpath` actually introduces a known, unfixed CVE!**

---

# The Irony | Patch Bumps ≠ Safe

This PR is supposed to **fix** things, but:

1. `jsonpath` 1.2.1 ships **with** CVE-2025-61140 — no patched version exists yet
2. The fix was pushed to `master` but **not published to npm**
3. Merging this PR puts a known vulnerable package into production
4. A human reviewer would never catch this from the diff alone

> **Minor/patch bumps can introduce security vulnerabilities.**

---
layout: lead
---

# How Can AI Help?

Automated analysis beyond the diff

---

# AI PR Review | The Concept

Instead of reviewing just the diff, AI can:

- 🔍 **Research upstream changelogs** across all updated packages
- 🔒 **Cross-reference CVE databases** for every dependency version
- 📋 **Analyze transitive dependency trees** for hidden risks
- ⚠️ **Flag breaking changes** even in minor/patch bumps
- 🛠️ **Suggest actionable fixes** with specific commands
- 📝 **Generate testing checklists** tailored to the changes

---

# Fossa & Fossabot

**Fossa** is a supply chain security platform that helps teams manage:
- 📦 Dependencies & licenses
- 🔒 Security vulnerabilities (CVE scanning)
- 📋 Compliance with licensing requirements
- 🐙 Requires GitHub.com

**Fossabot** is Fossa's AI-powered PR review assistant:
- 🤖 Automatically reviews dependency update PRs
- 🔍 Analyzes changelogs, CVEs, and breaking changes
- 💬 Comments directly on GitHub/GitLab PRs
- ⚡ Works for npm, Maven, PyPI, Go modules, and more
- 🔒 Uses LLM & AI services from Anthropic through an enterprise agreement - no training or data retention

---

# Fossabot | AI Review for the CNDA App

<div class="overflow-y-auto h-full">
  <img src="./assets/fossabot_review_1.png"/>
</div>

---

# Fossabot | AI Review for the CNDA App

<div class="overflow-y-auto h-full">
  <img src="./assets/fossabot_review_2.png"/>
</div>

---

# Fossabot | What It Checked

Fossabot automatically analyzed:

- ✅ CVE databases for all 4 packages
- ✅ Whether packages are direct or transitive dependencies
- ✅ Actual usage in the application source code
- ✅ Breaking changes in changelogs
- ✅ Runtime compatibility (Node 20)
- ✅ Fix availability and timelines

All of this — **in 5-10 minutes**, on every PR, automatically.

---

# Fossabot | Supported Ecosystems & Tools

<div class="overflow-y-auto h-full">
  <img src="./assets/supported_ecosystems.png"/>
</div>

---
layout: lead
---

# And For DevOps?

Helm Charts, Pipelines, Infrastructure-as-Code

---

# The DevOps Problem | Same Pattern, Higher Stakes

DevOps PRs follow the same pattern:

```diff
dependencies:
  - name: argo-cd
-    version: 9.3.7
+    version: 9.4.2
    repository: https://argoproj.github.io/argo-helm
```

**1 file changed, 1 line added, 1 line deleted.**

---

# Fossabot's Limitation | DevOps Ecosystem

Fossabot works great for **Javascript/Typescript ecosystems**:

- ✅ React, Node.js, frontend dependencies
- ✅ CVE cross-referencing for npm packages
- ✅ Dependency usage analysis in JS/TS source

But for **DevOps artifacts**, it falls short:
- ❌ No Helm chart changelog analysis
- ❌ No understanding of Kubernetes breaking changes
- ❌ No ArgoCD/Flux upgrade guide awareness
- ❌ No infrastructure-level impact assessment

**We need something for DevOps PRs.**

---
layout: lead
---

# AI Reviews with Claude Opus

Deep analysis for DevOps Pull Requests

---

# The Approach | Claude Opus

Using Claude (Opus) we can build **deep PR reviews**:

1. Feed the PR diff + upstream changelogs + upgrade guides
2. Ask for breaking changes, security risks, migration steps
3. Get a structured review with risk assessment

Let's see what Claude found for the ArgoCD bump...

---

# Instructions

```
Please review following pull request: https//www...
- these instructions should be used when reviewing Pull Requests
- point out the changes on a surface level
- point out security risks and issues. If possible, write full CVE name, which CVE's has been fixed and which new ones are introduced.
- although this is a minor update, there could still be underlying breaking changes (e.g. from underlying dependencies). Point these out as well
- do not use emojis
- you can use gh cli
- dump your results in a new file (naming convention PR-<number>-REVIEW.md). If a file is already existing, create a new file and increment <number>.
```

---

# What Claude Found | The ArgoCD PR

What looks like a 1-line change actually contains:

- 📊 **16 commits** between chart versions
- 🚨 **Major version jump**: ArgoCD v3.2.6 → v3.3.0
- 💥 **Breaking changes**: SSA now default sync strategy
- 🔒 **Security fixes**: Helm 3.19.4, Go crypto patches, Redis
- ⚠️ **CRD size limit**: `kubectl apply` will fail
- 📋 **Multiple upgrade guides** to review (v2.14 → v3.3!)

---

# What Claude Found | The ArgoCD PR

  <img src="./assets/opus_version_updates.png"/>

---

# What Claude Found | The ArgoCD PR

  <img src="./assets/opus_found_vulnerabilities.png"/>

---

# Breaking Change #1 | CRD Size Limit

The ApplicationSet CRD exceeds the 262144-byte annotation limit:

```
The CustomResourceDefinition "applicationsets.argoproj.io" is invalid:
metadata.annotations: Too long: may not be more than 262144 bytes
```

- ❌ `kubectl apply` will **fail**
- ✅ `helm upgrade` is **not affected**
- ⚠️ Self-managing ArgoCD must use SSA

---

# Breaking Change #2 | New Health Checks

New health checks may change application status unexpectedly:

- `ceph.rook.io/CephCluster`, `CephObjectStore`
- `keda.sh/ScaledJob`
- `services.cloud.sap.com/ServiceBinding`
- `*.cnrm.cloud.google.com/*` (GCP Config Connector)

**Impact**: Apps previously marked "Healthy" may now show different statuses.

This can trigger alerts and break automated workflows!

---

# Risk Matrix | Claude's Assessment

| Risk Factor | Level | Notes |
|-------------|-------|-------|
| CRD size limit (self-managed) | 🔴 HIGH | Requires SSA pre-config |
| SSA default change | � HIGH | Fundamental behavioral shift |
| Health check changes | 🟡 MEDIUM | Status transitions |
| Kustomize namespace fix | 🟡 MEDIUM | May alter manifests |
| Supply chain | 🟡 MEDIUM | Many transitive updates |
| Stale appVersion | 🟢 LOW | Cosmetic but misleading |

---

# Claude's Verdict | Conditional Approval

### DO NOT MERGE BLINDLY

Despite being a one-line change:

1. ✅ Read **all** upgrade guides (v2.14 → v3.0 → v3.1 → v3.2 → v3.3)
2. ✅ Deploy to staging environment first
3. ✅ Enable `ServerSideApply=true` if self-managing
4. ✅ Update wrapper chart `appVersion` to `v3.3.0`
5. ✅ Monitor for 24-48 hours
6. ✅ Have rollback plan ready

---
layout: lead
---

# The Comparison

Fossabot vs. Claude — different strengths

---
layout: two-cols
---

# Fossabot | JS/TS

**Strengths:**
- ✅ Automatic on every PR
- ✅ CVE database cross-referencing
- ✅ Dependency usage analysis
- ✅ Fix suggestions with commands
- ✅ Free & easy to set up

**Limitations:**
- ❌ requires GitHub.com
- ❌ dev ecosystems only
- ❌ No Helm/K8s awareness
- ❌ No infrastructure context

::right::

# Claude | DevOps/Infra

**Strengths:**
- ✅ Deep changelog analysis
- ✅ Helm/K8s breaking changes
- ✅ Multi-version upgrade paths
- ✅ Security risk assessment
- ✅ Migration step generation

**Limitations:**
- ❌ Requires manual prompting
- ❌ No automatic PR integration (yet)
- ❌ Needs context feeding

---
layout: lead
---

# Key Takeaways

What we learned

---

# The Core Message | Never Trust the Diff

### What a human reviewer sees
> "Bump 4 npm packages" — minor/patch versions ✅
> "Bump argo-cd from 9.3.7 to 9.4.2" — 1 line changed ✅

### What AI uncovers
- � Known CVEs **introduced** by the update
- 🚨 Breaking changes hidden behind semver
- 🔒 Security implications in transitive deps
- ⚠️ Migration requirements spanning multiple versions

---
layout: two-cols
---

# Human + AI | Better Together

**Humans excel at:**
- Business context
- Architecture decisions
- Risk tolerance judgment
- Stakeholder communication

::right::

## &nbsp;

**AI excels at:**
- Reading changelogs exhaustively
- Tracking transitive dependencies
- Identifying breaking changes
- Generating migration steps
- Security vulnerability analysis
  
---
layout: center
class: "text-center"
---

# Thank You!

**Let the Bot Review it** — Reducing Toil with Fossabot

[WhizUs](https://www.whizus.com) / [ArgoCD](https://argo-cd.readthedocs.io) / [Slidev](https://sli.dev)
