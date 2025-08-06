
# scan the python code

To **scan Python code for security vulnerabilities, bugs, and code quality issues automatically on GitHub**, you can use GitHub Actions with built-in tools like **CodeQL**, or integrate third-party scanners such as **Snyk**, **Bandit**, or **Safety**. Here’s how you can do it:

## 1. Scanning with GitHub’s Built-in **CodeQL**

**CodeQL** is GitHub’s default static analysis engine for code scanning and works well for Python projects. It's simple to enable and highly recommended for most repositories.

### **Enabling CodeQL Code Scanning: Quick Steps**

1. **Go to your repository’s “Security” tab** on GitHub.
2. Click **“Set up code scanning”**.
3. Choose the **CodeQL Analysis** workflow.
4. **Review and commit** the suggested workflow file (typically at `.github/workflows/codeql.yml`). It will look something like:
```yaml
name: "CodeQL"

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 0 * * 0'  # weekly scan

jobs:
  analyze:
    name: Analyze
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Initialize CodeQL
        uses: github/codeql-action/init@v3
        with:
          languages: python
      - name: Autobuild
        uses: github/codeql-action/autobuild@v3
      - name: Perform CodeQL Analysis
        uses: github/codeql-action/analyze@v3
        with:
          category: '/language:python'
```

- This setup will scan your code for vulnerabilities and errors on every push, pull request, and on a weekly schedule[^1][^2].
- Detected issues will appear in the **Security tab** of your repo, with detailed location and fix guidance[^3][^1][^4].
- You can customize the queries to run, including security-extended suites for more comprehensive checks[^5].


## 2. Scanning with **Bandit** (Popular for Python SAST)

**Bandit** is a specialized static analysis tool for finding common security issues in Python code. You can add it to your workflow like this:

```yaml
name: Bandit security scan

on: [push, pull_request]

jobs:
  bandit_scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Python
        uses: actions/setup-python@v4
      - name: Install Bandit
        run: pip install bandit
      - name: Run Bandit
        run: bandit -r .
```

- You can upload scan results as artifacts, or integrate with code scanning SARIF if you wish[^6].


## 3. Scanning Dependencies with **Safety** or **Snyk**

**Vulnerabilities in dependencies** are a major risk. Safety and Snyk both scan your Python package requirements against public databases of known vulnerabilities.

### **Safety**

```yaml
name: Safety Dependency Scan

on: push

jobs:
  safety:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run Safety
        uses: pyupio/safety-action@v1
        with:
          api-key: ${{ secrets.SAFETY_API_KEY }}
```

- Create a (free) Safety account and add your API key as a repo secret[^7].


### **Snyk**

```yaml
name: Snyk Python Vulnerability Check

on: push

jobs:
  snyk:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run Snyk
        uses: snyk/actions/python@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
```

- Snyk also supports uploading results to the GitHub Security tab with the SARIF format[^8][^9].


## 4. Viewing and Fixing Alerts

- **After running these workflows, results appear in your repository’s Security tab.**
- Click alerts for severity/type/location and fix suggestions.
- Fixed issues will auto-close in the tab after new scans[^3][^1].


## 5. Sample: Comprehensive Python Security Workflow

You can combine linters, Bandit, and dependency scanners for full coverage. Here’s a typical workflow snippet:

```yaml
name: Python Security Checks

on: [push, pull_request]

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4

      # Linting
      - name: Run flake8
        run: pip install flake8 && flake8 .

      # Static Analysis
      - name: Run Bandit
        run: pip install bandit && bandit -r .

      # Dependency Scan
      - name: Run Safety
        uses: pyupio/safety-action@v1
        with:
          api-key: ${{ secrets.SAFETY_API_KEY }}
```


## **Summary Table: Key Python Scanners for GitHub Actions**

| Tool | Purpose | Integration Method | Alerts Shown In |
| :-- | :-- | :-- | :-- |
| CodeQL | Code vulnerabilities | Built-in workflow | Security tab |
| Bandit | Static code analysis | `pip install bandit` + run step | Logs/artifacts, SARIF |
| Safety | Dependency CVEs | `pyupio/safety-action@v1` | Logs/artifacts |
| Snyk | Dependency \& code CVEs | `snyk/actions/python` | Security tab (SARIF) |

**In summary:**
For best results, start by enabling CodeQL for Python in the GitHub UI, then optionally add Bandit and either Safety or Snyk to cover all vulnerability surfaces in your Python code and its dependencies. All major tools are easy to use as GitHub Actions and integrate cleanly with the Security UI[^3][^1][^8][^2][^10][^6][^5][^7][^9].

<div style="text-align: center">⁂</div>

[^1]: https://github.com/advanced-security/demo-python

[^2]: https://ecanarys.com/secure-your-code-with-github-code-scanning-and-codeql/

[^3]: https://docs.github.com/code-security/code-scanning/introduction-to-code-scanning/about-code-scanning

[^4]: https://docs.github.com/en/code-security/code-scanning/introduction-to-code-scanning/about-code-scanning-with-codeql

[^5]: https://docs.github.com/en/code-security/code-scanning/managing-your-code-scanning-configuration/python-built-in-queries

[^6]: https://dev.to/sre_panchanan/learning-github-actions-in-a-simple-way-4i31

[^7]: https://docs.safetycli.com/safety-docs/installation/securing-git-repositories/github/github-actions

[^8]: https://docs.snyk.io/developer-tools/snyk-ci-cd-integrations/github-actions-for-snyk-setup-and-checking-for-vulnerabilities/snyk-python-action

[^9]: https://docs.snyk.io/developer-tools/snyk-ci-cd-integrations/github-actions-for-snyk-setup-and-checking-for-vulnerabilities

[^10]: https://graphite.dev/guides/automating-vulnerability-detection-in-github

[^11]: https://github.com/marketplace/actions/python-lint-code-scanning-action

[^12]: https://docs.github.com/en/code-security/code-scanning/creating-an-advanced-setup-for-code-scanning/customizing-your-advanced-setup-for-code-scanning?learn=code_security_actions\&learnProduct=code-security

[^13]: https://docs.github.com/en/code-security/code-scanning/creating-an-advanced-setup-for-code-scanning/codeql-code-scanning-for-compiled-languages

[^14]: https://docs.github.com/actions/quickstart

[^15]: https://github.blog/developer-skills/github/codeql-zero-to-hero-part-2-getting-started-with-codeql/

[^16]: https://cycode.com/blog/github-actions-vulnerabilities/

[^17]: https://docs.github.com/code-security/secure-coding/setting-up-code-scanning-for-a-repository

[^18]: https://learn.microsoft.com/en-us/azure/devops/repos/security/github-advanced-security-code-scanning?view=azure-devops

[^19]: https://github.com/marketplace/actions/python-safety-check

[^20]: https://docs.github.com/en/code-security/code-scanning/integrating-with-code-scanning

