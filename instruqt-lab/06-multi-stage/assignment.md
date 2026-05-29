---
slug: multi-stage
type: challenge
title: "Multi-Stage Builds with Chainguard Python"
teaser: Install dependencies in the -dev image, then ship only what you need in the minimal runtime.
notes:
- type: text
  contents: |
    ## Multi-Stage Builds

    When your app has Python dependencies, you need `pip` to install them — but `pip`
    shouldn't be in your production image (larger attack surface).

    The solution: **multi-stage builds**.

    | Stage | Image | Purpose |
    |-------|-------|---------|
    | builder | `python:latest-dev` | Has shell + apk + pip. Install deps into a venv. |
    | runtime | `python:latest` | Minimal. Copy only the venv + app code. |

    Result: a production image with your app's dependencies but **none of the build tooling**.
tabs:
- title: Terminal
  type: terminal
  hostname: workstation
- title: Editor
  type: code
  hostname: workstation
  path: /root/workshop/examples/python/multi-stage
difficulty: intermediate
timelimit: 900
---

# Multi-Stage Builds with Chainguard Python

## Step 1 — Explore the example

```
cd /root/workshop/examples/python/multi-stage
ls -la
```

Open the **Editor** tab and read through:
- `Dockerfile` — the multi-stage build definition
- `linky.py` — the Python app
- `requirements.txt` — Python dependencies

## Step 2 — Update the Dockerfile

Replace `{{ORGANIZATION}}` with your org name:

```
sed -i "s|{{ORGANIZATION}}|${ORGANIZATION}|g" Dockerfile
cat Dockerfile
```

Notice the two `FROM` statements:
1. `FROM cgr.dev/${ORGANIZATION}/python:latest-dev AS builder` — installs deps
2. `FROM cgr.dev/${ORGANIZATION}/python:latest` — copies only the venv

## Step 3 — Download the demo asset

```
curl -O https://raw.githubusercontent.com/chainguard-dev/edu-images-demos/main/python/linky/linky.png
```

## Step 4 — Build the image

```
docker build . --pull -t linky
```

Watch the output — you'll see both stages run: the builder installs packages,
then the runtime stage copies only the essentials.

## Step 5 — Run the app

```
docker run --rm linky
```

## Step 6 — Scan the final image

```
grype linky
trivy image linky
```

**Bonus questions:**
- What ended up in the final image vs what's left behind in the builder?
- How would you gate this image in CI (scan, SBOM, provenance verify)?

Click **Check** when you've built and run `linky`.
