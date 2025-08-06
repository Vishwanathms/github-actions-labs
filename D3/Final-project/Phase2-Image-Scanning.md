<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# how to do docker image scanning using trivy in the github actions

To scan Docker images for vulnerabilities using Trivy in GitHub Actions, you can seamlessly integrate the official Trivy Action into your CI/CD pipeline. Below is a practical, up-to-date workflow you can adapt immediately:

## 1. **Add Trivy to Your GitHub Actions Workflow**

Here's a sample YAML configuration that builds a Docker image, scans it with Trivy, and uploads the results to GitHub's Security tab:

```yaml
name: Trivy Docker Security Scan

on:
  push:
    branches:
      - main
  pull_request:

permissions:
  actions: read
  security-events: write

jobs:
  docker_security_scan:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Build Docker image
        run: docker build -t my-app:${{ github.sha }} .

      - name: Trivy vulnerability scan
        uses: aquasecurity/trivy-action@0.30.0
        with:
          image-ref: 'my-app:${{ github.sha }}'
          format: 'sarif'
          severity: 'CRITICAL,HIGH'
          output: 'trivy-results.sarif'
          exit-code: 0 # Allows the workflow to continue even if vulnerabilities are found

      - name: Upload Trivy scan results to GitHub Security tab
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: 'trivy-results.sarif'
```

**What this does:**

- **Builds** your Docker image.
- **Scans** it for vulnerabilities (CRITICAL, HIGH) using Trivy.
- **Uploads** the scan results to GitHub’s Security tab in SARIF format for easy review.


## 2. **Customizing the Scan**

- To **fail the workflow if vulnerabilities are detected**, set `exit-code: 1` in the Trivy step.
- For different output formats (e.g., table, json, sarif), adjust the `format:` key.
- Scanning images from a private registry? Add `env:` with your credentials (see Trivy docs).


## 3. **Visualization**

- After the run, view detected vulnerabilities in the repository’s **Security** > **Code scanning alerts** tab.


## 4. **References and Documentation**

- [Trivy GitHub Action (official)](https://github.com/aquasecurity/trivy-action)[^1][^2][^3]
- More details: Trivy documentation on advanced scanning options[^4][^5]

**Tip:**
Trivy can also scan IaC (like Terraform, Kubernetes YAML), file systems, or remote repositories—just adjust `scan-type` and `scan-ref` in the action as needed.

If you need extended configurations or want to scan before pushing to Docker Hub, just let me know!

<div style="text-align: center">⁂</div>

[^1]: https://github.com/aquasecurity/trivy-action

[^2]: https://github.com/marketplace/actions/aqua-security-trivy

[^3]: https://thomasthornton.cloud/2025/03/18/setting-up-trivy-in-your-github-actions/

[^4]: https://github.com/aquasecurity/trivy

[^5]: https://aquasecurity.github.io/trivy/v0.38/docs/target/container_image/

[^6]: https://aquasecurity.github.io/trivy/v0.19.2/advanced/integrations/github-actions/

[^7]: https://www.youtube.com/watch?v=GGWinWTHqCY

[^8]: https://github.com/marketplace/actions/trivy-action

[^9]: https://devopscube.com/scan-docker-images-using-trivy/

[^10]: https://www.aquasec.com/blog/trivy-github-actions-security-cicd-pipeline/

[^11]: https://www.youtube.com/watch?v=Sh1FP68rs3M

[^12]: https://www.skaylink.com/en/insights/blog/trivy/

[^13]: https://developer.harness.io/docs/security-testing-orchestration/sto-techref-category/trivy/container-scan-aqua-trivy/

[^14]: https://dev.to/suzuki0430/enhance-code-security-with-github-actions-automatically-commenting-prs-with-docker-scans-48ap

[^15]: https://aquasecurity.github.io/trivy/v0.29.2/docs/misconfiguration/scanning/

[^16]: https://hub.docker.com/r/aquasec/trivy/

[^17]: https://escape.tech/blog/devsecops-part-4-scanning-docker-images-with-trivy/

