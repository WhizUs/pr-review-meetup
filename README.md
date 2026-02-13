# ArgoCD Demo Repository

This repository manages an ArgoCD Helm chart deployment and is configured with
**GitHub Dependabot** to automatically detect and propose version updates.

## Purpose

This repo is set up to demonstrate how Dependabot creates Pull Requests for
Helm chart dependency updates — specifically the **ArgoCD** chart from
[argoproj/argo-helm](https://github.com/argoproj/argo-helm).

The current pinned version is **9.3.7**. Dependabot will detect that a newer
version (e.g. 9.4.x) is available and open a PR to bump the version in
`charts/argocd/Chart.yaml`.

## Structure

```
.
├── .github/
│   └── dependabot.yml          # Dependabot configuration (helm ecosystem)
├── charts/
│   └── argocd/
│       ├── Chart.yaml           # Wrapper chart — declares argo-cd 9.3.7 as dependency
│       └── values.yaml          # Value overrides for the argo-cd sub-chart
└── README.md
```

## How It Works

1. `Chart.yaml` declares `argo-cd` version `9.3.7` as a Helm dependency from
   `https://argoproj.github.io/argo-helm`.
2. `.github/dependabot.yml` tells Dependabot to monitor the `helm` ecosystem
   in the `/charts/argocd` directory on a daily schedule.
3. Dependabot checks the upstream Helm repository for new chart versions.
4. When a newer version is found (e.g. `9.4.x`), Dependabot opens a PR that
   bumps the `version` field in the `dependencies` section of `Chart.yaml`.

## Why 9.3.7 → 9.4.x Is Interesting

The ArgoCD chart 9.4.x update appears to be a minor version bump, but it
contains **underlying breaking changes** that are not immediately visible in
the diff. This makes it an excellent candidate for demonstrating AI-powered
code review (e.g. using Claude) on Dependabot PRs:

- The reviewer needs to look beyond the simple version number change
- Breaking changes may affect Helm values, templates, or default behavior
- A thorough review requires understanding the upstream changelog

## Quick Start

```bash
# Create a new GitHub repository
gh repo create argocd-demo --public --source=. --push

# Dependabot will start scanning on the next scheduled run
# You can also trigger it manually from the repository's Insights > Dependency Graph > Dependabot tab
```
