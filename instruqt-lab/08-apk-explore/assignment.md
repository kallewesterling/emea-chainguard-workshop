---
slug: apk-explore
type: challenge
title: "Explore Wolfi Packages with apk"
teaser: Discover what packages are available in Chainguard images and how to find what you need.
notes:
- type: text
  contents: |
    ## apk — The Wolfi Package Manager

    Chainguard images use **apk** — the same package manager as Alpine, but backed by
    the **Wolfi** repository (`packages.wolfi.dev`).

    Wolfi packages are:
    - Rebuilt continuously with upstream patches applied
    - Pinned to exact versions with reproducible builds
    - Scanned and fixed before publishing

    You can explore what's available interactively by running a `wolfi-base` container.

    ## Custom Assembly

    Want to add packages permanently to a Chainguard image? **Custom Assembly** lets you
    create your own "Golden Image" — a modified Chainguard image with your chosen
    packages pre-installed, fully signed, with provenance and SBOM attached automatically.

    Try it in the [Chainguard Console](https://console.chainguard.dev) after this lab!
tabs:
- title: Terminal
  type: terminal
  hostname: workstation
difficulty: basic
timelimit: 600
---

# Explore Wolfi Packages with apk

## Step 1 — Start an interactive Wolfi shell

```
docker run -it --rm --entrypoint /bin/sh cgr.dev/chainguard/wolfi-base
```

This drops you into a shell inside a Wolfi container.

## Step 2 — Update the package index

```
apk update
```

## Step 3 — Search for packages

Find PHP 8.2 XML-related packages:

```
apk search php*8.2*xml*
```

Use wildcards to match patterns, versions, or submodules.

## Step 4 — Search by command name

Find which package provides a specific binary:

```
apk search cmd:useradd
```

Expected output: `shadow-<version>`

## Step 5 — Inspect package dependencies

```
apk -R info shadow
```

See what libraries a package depends on.

## Step 6 — Exit the container

```
exit
```

## ➡️ Custom Assembly (UI walkthrough)

Now that you know what packages exist, you can create a **Custom Assembly** image —
your own "Golden Image" with selected packages pre-installed:

1. Go to [console.chainguard.dev](https://console.chainguard.dev)
2. Navigate to your Python image
3. Click **Customize Image** in the top-right corner
4. Search for and add `curl` and `bash`
5. Preview your customized image

The result is a signed, SBOM-backed image built by Chainguard's infrastructure — ready
to use as a foundation for your teams.

Click **Check** when you've explored the Wolfi package index.
