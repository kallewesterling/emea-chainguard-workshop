---
slug: scan-images
type: challenge
title: "Security Scanning with Grype & Trivy"
teaser: Scan images for CVEs and discover why one scanner is never enough.
notes:
- type: text
  contents: |
    ## How Vulnerability Scanners Work

    Tools like **Grype** and **Trivy** inspect container images to identify known CVEs:

    1. **Dependency mapping** — catalog all packages (direct + transitive)
    2. **SBOM ingestion** — read Software Bills of Materials for accuracy
    3. **Vulnerability matching** — compare versions against NVD, vendor advisories, Wolfi SecDB
    4. **Reporting** — list CVEs by severity with fix availability

    ⚠️ **Key insight:** Trivy only reports CVEs where a fix is available. Grype reports
    all known CVEs. This means the same image can look different depending on which
    scanner you use — which is exactly why you need both.
tabs:
- title: Terminal
  type: terminal
  hostname: workstation
- title: Notes
  type: code
  hostname: workstation
  path: /root/scan-results.txt
difficulty: basic
timelimit: 900
---

# Security Scanning with Grype & Trivy

Open the **Notes** tab and record your findings as you go.

## Part 1 — Scan Alpine

### With Trivy:
```
trivy image alpine:latest
```

Record: number of vulnerabilities, severities.

### With Grype:
```
grype alpine:latest
```

Record: number of vulnerabilities.

> **Notice:** The counts likely differ. Why? Trivy only shows CVEs with available fixes.
> Grype shows everything known.

## Part 2 — Scan public Python

```
grype python:latest 2>&1 | tail -5
trivy image python:latest 2>&1 | tail -20
```

Record totals. The public Python image ships with a full Debian/Ubuntu base — lots of packages, lots of CVEs.

## Part 3 — Scan Chainguard Python

```
grype cgr.dev/${ORGANIZATION}/python:latest
grype cgr.dev/${ORGANIZATION}/python:latest-dev
```

```
trivy image cgr.dev/${ORGANIZATION}/python:latest
trivy image cgr.dev/${ORGANIZATION}/python:latest-dev
```

**Questions to consider:**
- Which image had more packages?
- Did either show vulnerabilities?
- What explains the difference between `latest` and `latest-dev`?

## ⚠️ Scanner Limitations

Because scanners can miss things (different feeds, transitive deps, fix-availability logic),
build defense in depth:

✅ Run multiple scanners and compare  
✅ Require and verify SBOMs  
✅ Sign images and verify signatures  
✅ Enforce reproducible builds  
✅ Use provenance/attestation standards (SLSA)

Click **Check** when you've scanned all four images.
