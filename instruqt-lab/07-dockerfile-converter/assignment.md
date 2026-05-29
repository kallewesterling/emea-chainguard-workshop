---
slug: dockerfile-converter
type: challenge
title: "Convert Dockerfiles with dfc"
teaser: Migrate an existing Dockerfile to Chainguard Images in seconds using the dfc tool.
notes:
- type: text
  contents: |
    ## The Dockerfile Converter (dfc)

    Migrating existing Dockerfiles to Chainguard can be fast with `dfc`.

    It automatically:
    - Swaps base images to Chainguard equivalents
    - Restructures builds into multi-stage (dev → runtime)
    - Shifts package installs to the `-dev` image using `apk add`

    This is a great starting point when you have an existing image program and want
    to migrate to Chainguard without rewriting every Dockerfile by hand.
tabs:
- title: Terminal
  type: terminal
  hostname: workstation
- title: Original Dockerfile
  type: code
  hostname: workstation
  path: /root/workshop/examples/dfc/dockerfile
- title: Converted Dockerfile
  type: code
  hostname: workstation
  path: /root/workshop/examples/dfc/dockerfile.cgr
difficulty: basic
timelimit: 600
---

# Convert Dockerfiles with dfc

## Step 1 — Navigate to the dfc example

```
cd /root/workshop/examples/dfc
```

Open the **Original Dockerfile** tab and study the classic build:
- Base image
- Package installs
- App copy and entrypoint

## Step 2 — Run the converter

```
dfc dockerfile --org ${ORGANIZATION} > dockerfile.cgr
```

Open the **Converted Dockerfile** tab and compare.

Typical changes you'll see:
- Base image switched to `cgr.dev/${ORGANIZATION}/...`
- Multi-stage split: builder (`-dev`) + runtime (minimal)
- Package installs moved to the builder stage using `apk add`
- Non-root user configuration

## Step 3 — Inspect the differences

```
diff dockerfile dockerfile.cgr
```

## Step 4 — Build from the converted Dockerfile

```
docker build -f dockerfile.cgr -t app-cgr .
```

## Step 5 — Scan both versions

```
echo "=== Original ===" && grype $(docker inspect app-original --format '{{.Id}}' 2>/dev/null || echo "python:latest") 2>&1 | tail -3
echo "=== Chainguard ===" && grype app-cgr 2>&1 | tail -3
```

Click **Check** when you've generated `dockerfile.cgr` and built `app-cgr`.
