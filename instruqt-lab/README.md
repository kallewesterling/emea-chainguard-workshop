# Chainguard Images — Instruqt Lab

This directory contains the [Instruqt](https://instruqt.com) track definition for the **Chainguard Images Hands-On Workshop**. It converts the [EMEA Chainguard Workshop](https://github.com/julianklodzinski/emea-chainguard-workshop) into a self-contained, browser-based lab that learners can run without any local tooling.

## Track Overview

| # | Challenge | Key skills |
|---|-----------|-----------|
| 1 | Set Up chainctl & Docker Auth | Verify tooling, confirm org, test registry access |
| 2 | Pull Chainguard & Public Images | Compare image sizes across CG minimal, CG dev, public |
| 3 | Security Scanning with Grype & Trivy | CVE comparison, scanner differences, defense in depth |
| 4 | Verify Image Provenance with Cosign | `cosign verify`, SBOM attestation download & verify |
| 5 | Build & Run a Chainguard Python App | Simple single-stage build with minimal CG image |
| 6 | Multi-Stage Builds | builder (`latest-dev`) → runtime (`latest`) pattern |
| 7 | Convert Dockerfiles with dfc | Automated Dockerfile migration to Chainguard |
| 8 | Explore Wolfi Packages with apk | Interactive `apk search`, Custom Assembly pointer |

**Estimated time:** 90–120 minutes  
**Sandbox:** Ubuntu 22.04 VM (n1-standard-4) with Docker pre-installed

---

## File Structure

```
instruqt-lab/
├── README.md                          # This file
├── track.yml                          # Track metadata (title, tags, timelimit, theme)
├── config.yml                         # Sandbox definition (VM + secrets)
├── track_scripts/
│   ├── setup-workstation              # Installs all tools, clones repo, warms scan DBs
│   └── cleanup-workstation            # Prunes Docker resources on teardown
├── 01-chainctl-setup/
│   ├── assignment.md
│   └── check-workstation
├── 02-pull-images/
│   ├── assignment.md
│   └── check-workstation
├── 03-scan-images/
│   ├── assignment.md
│   ├── setup-workstation              # Creates /root/scan-results.txt notes file
│   └── check-workstation
├── 04-provenance/
│   ├── assignment.md
│   └── check-workstation
├── 05-build-starter/
│   ├── assignment.md
│   ├── setup-workstation              # Pre-substitutes $ORGANIZATION into Dockerfile
│   └── check-workstation
├── 06-multi-stage/
│   ├── assignment.md
│   ├── setup-workstation
│   └── check-workstation
├── 07-dockerfile-converter/
│   ├── assignment.md
│   └── check-workstation
└── 08-apk-explore/
    ├── assignment.md
    └── check-workstation
```

---

## Prerequisites

### 1. Instruqt account

You need an [Instruqt](https://instruqt.com) account with a team set up. Install the CLI:

```bash
# macOS
brew install instruqt/tap/instruqt

# Linux
curl -sSL https://instruqt.com/install | bash
```

Authenticate:

```bash
instruqt auth login
```

### 2. Instruqt CLI — set your team

```bash
instruqt config set-team <your-team-slug>
```

### 3. Configure Secrets

The track uses two **Instruqt secrets** that are injected as environment variables into the sandbox:

| Secret name | Description | Where to get it |
|-------------|-------------|-----------------|
| `CHAINGUARD_ORG` | Your Chainguard organization name (e.g. `mycompany.de`) | Top-left of [console.chainguard.dev](https://console.chainguard.dev) |
| `CHAINGUARD_PULL_TOKEN` | A pull token for the org's registry | Console → Settings → Pull Tokens → Create |

Create the secrets in Instruqt under **Team Settings → Secrets**, then reference them in `config.yml` (already wired up).

> For workshop delivery with multiple learners, create a dedicated **workshop organization** in Chainguard and a single pull token scoped to that org. Distribute the same `CHAINGUARD_ORG` / `CHAINGUARD_PULL_TOKEN` pair to all sandbox instances via secrets.

---

## Pushing the Track to Instruqt

From inside the `instruqt-lab/` directory:

```bash
# First push (creates the track)
instruqt track push

# Subsequent pushes
instruqt track push
```

To open and test the track immediately:

```bash
instruqt track open
```

> Changes pushed via CLI are immediately live. The `track.yml` `id` field will be auto-populated after the first push — commit that change back to the repo.

---

## Sandbox Configuration Notes

### VM vs Container

The track uses a **Virtual Machine** (`virtualmachines:` in `config.yml`) rather than a container. This is intentional — Docker-in-Docker in Instruqt containers is unreliable for heavy image pulls. The VM gives Docker a proper kernel and full disk space.

The VM image used is `projects/instruqt-core/global/images/ubuntu-2204-lts` (Instruqt's standard Ubuntu 22.04). If this image slug has changed, update `config.yml` accordingly. Check current available images with:

```bash
instruqt sandbox list-images
```

### Tool installation

All tools are installed in `track_scripts/setup-workstation`, which runs once when the sandbox spins up (before the learner sees challenge 1). Tools installed:

- Docker CE
- chainctl (latest)
- grype + syft (Anchore)
- trivy (Aqua)
- cosign (Sigstore)
- dfc (Chainguard)
- jq, yq, git

The script also pre-warms the Grype and Trivy vulnerability databases so learners don't wait for DB downloads during scanning challenges.

---

## Known Caveats & Things to Review

### Challenge 4 — Provenance

The `chainctl iam account-associations describe` command requires an **authenticated chainctl session** (not just a pull token). Options:

- **Option A (recommended for workshops):** Pre-authenticate chainctl in `track_scripts/setup-workstation` using a chainctl service account token stored as a third secret (`CHAINCTL_TOKEN`). Add:
  ```bash
  chainctl auth login --identity-token "${CHAINCTL_TOKEN}"
  ```
- **Option B:** Make challenge 4 an instructor-led demo and mark it non-blocking (`skipping_enabled: true` in `track.yml`).

### `dfc` example Dockerfile

Challenge 7 assumes the workshop repo's `examples/dfc/dockerfile` exists. Verify it's present in [the source repo](https://github.com/julianklodzinski/emea-chainguard-workshop/tree/main/examples/dfc) before running the lab. If the path has changed, update the challenge `assignment.md` accordingly.

### Grype/Trivy check scripts

The check scripts for challenge 3 verify that Grype and Trivy have been used by checking their local cache directories. This is a proxy check — it confirms the tools ran, but not which images were scanned. For stricter validation, consider writing scan output to a file and checking that file in the check script.

### Image availability

Challenges 2–7 require learners to have access to `cgr.dev/${ORGANIZATION}/python:latest` and `cgr.dev/${ORGANIZATION}/python:latest-dev`. Confirm the pull token has read access to both tags in the workshop org before running the lab.

---

## Customization

- **Add a challenge:** Create a new `0N-slug/` directory with `assignment.md` and optional `setup-workstation` / `check-workstation` scripts, then run `instruqt track push`.
- **Change the theme:** Edit `lab_config.theme.name` in `track.yml` (`modern-dark` or `original`).
- **Adjust timelimit:** `timelimit` in `track.yml` is in seconds (currently `7200` = 2 hours). Per-challenge limits are set in each `assignment.md` front-matter.
- **Extend to other images:** The Python exercises can be adapted for Node, Java, Go, etc. by updating the image names and example app directories.
