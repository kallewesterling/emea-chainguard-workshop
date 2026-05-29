---
slug: provenance
type: challenge
title: "Verify Image Provenance with Cosign"
teaser: Cryptographically prove that a Chainguard Image is authentic and unmodified.
notes:
- type: text
  contents: |
    ## Why Provenance Matters

    Even a CVE-free image could be compromised if:
    - It was built by the wrong party
    - It was tampered with after building

    Chainguard solves this by embedding:
    - **Verifiable signatures** — proof the image was built and signed by Chainguard
    - **SBOM attestations** — a cryptographically signed inventory of every package

    You'll use **cosign** to verify both.
tabs:
- title: Terminal
  type: terminal
  hostname: workstation
difficulty: intermediate
timelimit: 900
---

# Verify Image Provenance with Cosign

## Step 1 — Set variables

```
export IMAGE=python
export TAG=latest
```

## Step 2 — Get Chainguard signing identities for your org

```
CATALOG_SYNCER=$(chainctl iam account-associations describe $ORGANIZATION -o json \
  | jq -r '.[].chainguard.service_bindings.CATALOG_SYNCER')

APKO_BUILDER=$(chainctl iam account-associations describe $ORGANIZATION -o json \
  | jq -r '.[].chainguard.service_bindings.APKO_BUILDER')

echo "CATALOG_SYNCER: $CATALOG_SYNCER"
echo "APKO_BUILDER:   $APKO_BUILDER"
```

## Step 3 — Verify the image signature

```
cosign verify \
  --certificate-oidc-issuer=https://issuer.enforce.dev \
  --certificate-identity-regexp="https://issuer.enforce.dev/(${CATALOG_SYNCER}|${APKO_BUILDER})" \
  cgr.dev/$ORGANIZATION/$IMAGE:$TAG | jq
```

A successful result returns valid certificates, signing identities, and timestamps.
This proves the image was built by Chainguard and hasn't been modified since.

## Step 4 — Download the SBOM attestation

```
cosign download attestation \
  --platform=linux/amd64 \
  --predicate-type=https://spdx.dev/Document \
  cgr.dev/$ORGANIZATION/$IMAGE:$TAG \
  | jq -r .payload | base64 -d | jq .predicate
```

This fetches and decodes the SBOM — a detailed inventory of every component in the image.

## Step 5 — Verify the attestation signature

```
cosign verify-attestation \
  --type https://spdx.dev/Document \
  --certificate-oidc-issuer=https://issuer.enforce.dev \
  --certificate-identity-regexp="https://issuer.enforce.dev/(${CATALOG_SYNCER}|${APKO_BUILDER})" \
  cgr.dev/$ORGANIZATION/$IMAGE:$TAG
```

This confirms the SBOM itself is authentic and came directly from Chainguard's build systems.

## ✅ What you've just proven

> **"Can I trust that this image actually comes from Chainguard and hasn't been tampered with?"**
>
> **Yes.** You've verified both the signature and the provenance attestation end-to-end.

Click **Check** when complete.
