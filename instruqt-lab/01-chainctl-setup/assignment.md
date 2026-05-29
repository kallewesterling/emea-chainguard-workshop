---
slug: chainctl-setup
type: challenge
title: "Set Up chainctl & Docker Auth"
teaser: Authenticate with Chainguard and configure Docker to pull from your private registry.
notes:
- type: text
  contents: |
    ## Welcome to the Chainguard Images Workshop!

    In this workshop you'll learn how to use **Chainguard Images** — secure, minimal,
    continuously verified container images — to eliminate CVEs and build a trustworthy
    software supply chain.

    **What you'll do:**
    - Pull and compare Chainguard Images vs public alternatives
    - Scan for CVEs with Grype and Trivy
    - Verify image provenance with Cosign
    - Build real multi-stage Docker images
    - Convert Dockerfiles with the `dfc` tool

    Your Chainguard organization name is pre-loaded as `$ORGANIZATION`.
    Let's get started!
tabs:
- title: Terminal
  type: terminal
  hostname: workstation
difficulty: basic
timelimit: 600
---

# Set Up chainctl & Docker Auth

All tools have been pre-installed for you. Let's verify the setup and configure Docker
to pull from your Chainguard registry.

## Step 1 — Verify your tools

Run this quick check to confirm everything is installed:

```
chainctl version && docker version --format '{{.Server.Version}}' && grype version && syft --version && trivy --version && jq --version && yq --version && cosign version && dfc version && git --version
```

All tools should return a version string.

## Step 2 — Confirm your organization

Your organization name is already set as an environment variable:

```
echo $ORGANIZATION
```

You should see your Chainguard org (e.g. `mycompany.de`).

## Step 3 — Authenticate chainctl

Your pull token is pre-configured, but you can also authenticate interactively.
In a headless environment (no browser available) use:

```
chainctl auth login --headless
```

Copy the URL shown into a browser, complete the auth, then paste the token back.

> **Skip this step** if `$ORGANIZATION` is already set and Docker is authenticated —
> the sandbox pre-configured your credentials.

## Step 4 — Verify Docker is authenticated for cgr.dev

```
docker pull cgr.dev/chainguard/wolfi-base:latest
```

If the pull succeeds, your Docker client can reach the Chainguard registry. ✅

## Step 5 — View your Chainguard auth status

```
chainctl auth status
```

Click **Check** when all steps are complete.
