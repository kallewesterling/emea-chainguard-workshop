---
slug: build-starter
type: challenge
title: "Build & Run a Chainguard Python App"
teaser: "\"Surely these minimal images can't work?\" — They do. Build one and see."
notes:
- type: text
  contents: |
    ## Minimal ≠ Broken

    Chainguard's minimal `python:latest` image has no shell and no package manager.
    That sounds limiting — but for running a Python application, you don't need either.

    The starter app in this challenge is a simple Python script that prints the OS
    it's running on. You'll build it with Chainguard Python and run it.
tabs:
- title: Terminal
  type: terminal
  hostname: workstation
- title: Editor
  type: code
  hostname: workstation
  path: /root/workshop/examples/python/starter
difficulty: basic
timelimit: 600
---

# Build & Run a Chainguard Python App

## Step 1 — Navigate to the starter example

```
cd /root/workshop/examples/python/starter
ls -la
```

Open the **Editor** tab to inspect the files:
- `app.py` — a simple Python script
- `dockerfile` — the Dockerfile using Chainguard Python

## Step 2 — Update the Dockerfile

Open `dockerfile` in the Editor tab and replace `{{ORGANIZATION}}` with your org name:

```
sed -i "s|{{ORGANIZATION}}|${ORGANIZATION}|g" dockerfile
```

Verify the change:

```
cat dockerfile
```

## Step 3 — Build the image

```
docker build -f dockerfile -t cgr-python:standard .
```

## Step 4 — Run the app

```
docker run --rm -v .:/app cgr-python:standard
```

You should see output like:
```
Hello World! From Linux operating system on 64bit ELF architecture
```

## Step 5 — (Optional) Compare with public Python

Edit the Dockerfile to use `python:latest` instead of the Chainguard image, rebuild,
and run it. Notice any differences in output or build time.

## Step 6 — Scan your built image

```
grype cgr-python:standard
```

How many CVEs? Compare that to `grype python:latest`.

Click **Check** when you've successfully built and run the image.
