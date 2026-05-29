---
slug: pull-images
type: challenge
title: "Pull Chainguard & Public Images"
teaser: Pull the images you'll use throughout this workshop and see the size difference immediately.
notes:
- type: text
  contents: |
    ## Chainguard Images

    Chainguard Images are stored in your organization's private registry at `cgr.dev`.

    Pull format:
    ```
    docker pull cgr.dev/$ORGANIZATION/IMAGE:TAG
    ```

    Each org gets access to a curated catalog. You'll pull both the **minimal** (`latest`)
    and **developer** (`latest-dev`) variants of Python, then compare against public images.
tabs:
- title: Terminal
  type: terminal
  hostname: workstation
difficulty: basic
timelimit: 600
---

# Pull Chainguard & Public Images

## Step 1 — Confirm your organization variable

```
echo "Organization: $ORGANIZATION"
```

> If it's empty, run: `export ORGANIZATION=<your-org-name>`

## Step 2 — Pull the Chainguard Python images

```
docker pull cgr.dev/${ORGANIZATION}/python:latest-dev
docker pull cgr.dev/${ORGANIZATION}/python:latest
```

- `latest-dev` — includes shell (`/bin/sh`) and the `apk` package manager. Use this as a **build stage**.
- `latest` — minimal runtime image. No shell, no extra tools. Tiny attack surface.

## Step 3 — Pull public comparison images

```
docker pull python:latest
docker pull alpine:latest
```

## Step 4 — Compare image sizes

```
docker images --format "table {{.Repository}}:{{.Tag}}\t{{.Size}}" \
  cgr.dev/${ORGANIZATION}/python:latest \
  cgr.dev/${ORGANIZATION}/python:latest-dev \
  python:latest \
  alpine:latest
```

Notice the size difference. Chainguard's minimal image is significantly smaller because
it contains only what's needed to run Python — nothing more.

Click **Check** when all four images are pulled.
