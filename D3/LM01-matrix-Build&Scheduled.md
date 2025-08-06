

**Lab Goals:**

- Configure a matrix build to test across Linux and Windows with multiple Node versions.
- Add cron-scheduled maintenance (log cleanup and nightly security audit).
- Use secrets and context in scheduled jobs.
- Observe, troubleshoot, and optimize workflow behaviour.


### **Dependencies \& Setup**

- **A GitHub Account** with permissions to create repositories and secrets.
- **A GitHub repository** (new or existing).
- **Node.js Project** (can use `npm init -y` to create a dummy project).
- (Optional) `gh` CLI, for hands-on artifact cleanup.


## **Part 1: Matrix Build - Multi-OS \& Multi-Version Testing**

### **Step 1: Prepare Your Node.js Repository**

1. Clone or create a new GitHub repo.
2. Initialize a simple Node.js project:

```bash
npm init -y
echo "console.log('Hello, GitHub Actions!')" > index.js
```

3. Add a basic test script:
    - In `package.json`, adjust:

```json
"scripts": {
  "test": "node index.js"
}
```


### **Step 2: Add the Matrix Build Workflow**

1. In `.github/workflows/`, create `matrix-ci.yml`:

```yaml
name: Matrix CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        node: [14, 16, 18]
        exclude:
          - os: windows-latest
            node: 14
        include:
          - os: ubuntu-latest
            node: 20
            experimental: true
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}
      - run: npm ci
      - run: npm test
```

2. Commit and push the workflow:

```bash
git add .github/workflows/matrix-ci.yml
git commit -m "Add matrix CI workflow"
git push
```


### **Step 3: Trigger and Observe**

- Push a change or open a PR.
- In your repo, go to the **Actions** tab.
- Look for the “Matrix CI” workflow.
- You should see multiple jobs running in parallel (Ubuntu/Windows, Node 14/16/18, with one experimental combo).


### **Step 4: Matrix Optimization**

- Try adding these under `strategy:`:

```yaml
fail-fast: false
max-parallel: 3
```

    - Observe how failed jobs don’t immediately stop others and concurrency is limited.


## **Part 2: Scheduled Workflows - Automated Maintenance**

### **Step 5: Add a Scheduled Cleanup Workflow**

1. In `.github/workflows/`, create `scheduled-maintenance.yml`:

```yaml
name: Scheduled Maintenance

on:
  schedule:
    - cron: '0 3 * * *'  # 3:00 AM UTC daily

jobs:
  cleanup:
    runs-on: ubuntu-latest
    steps:
      - name: Delete temp logs
        run: |
          mkdir -p /tmp/app-logs
          echo "Test log" > /tmp/app-logs/log1.txt
          rm -rf /tmp/app-logs/*
          echo "Clean up done at $(date -u)"
```

2. Commit and push:

```bash
git add .github/workflows/scheduled-maintenance.yml
git commit -m "Add scheduled maintenance workflow"
git push
```


### **Step 6: Simulating and Observing Runs**

- Wait for the next UTC run or temporarily add a `workflow_dispatch:` trigger to run manually:

```yaml
on:
  workflow_dispatch:
  schedule:
    - cron: '0 3 * * *'
```

- After a run, check “Actions” tab for logs and output.


### **Step 7: Scheduled Dependency Audit (Nightly Security Scan)**

1. In `scheduled-maintenance.yml`, add a new job:

```yaml
jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm ci
      - run: npm audit --audit-level=high

      - name: Notify on issues
        if: failure()
        run: echo "Security issues found! (In practice, send email/Slack here)"
```

2. **(Optional) Use a secret:**
    - Go to repo Settings ➔ Secrets → Actions → `New repository secret`
    - Name: `SLACK_WEBHOOK`, Value: <your webhook>
    - In your job:

```yaml
env:
 SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}

- name: Notify Slack
  if: failure()
  run: |
    curl -X POST -H 'Content-type: application/json' --data '{"text":"Security issues found!"}' $SLACK_WEBHOOK
```


### **Step 8: Fine-Grained Scheduling**

- Add logic to run specific steps on Mondays:

```yaml
- name: Monday-only Task
  run: |
    if [ "$(date +%u)" -eq 1 ]; then
      echo "Running Monday task..."
    else
      echo "Not Monday, skipping."
    fi
```


## **Troubleshooting**

| Issue | Resolution |
| :-- | :-- |
| Tests fail on some OS/Node combos | Check code compatibility, update dependencies, or use conditional steps with `if:` |
| Scheduled job does not trigger | Check cron is in UTC, workflow is in main branch, repo is public for forks, no invalid syntax |
| Secrets not available | Ensure secret name is correct, referenced as `${{ secrets.NAME }}` |
| Audit always passes | Add `--audit-level=high` to npm audit for stricter results; test with known-vulnerable dependency |

## **Cleanup (Optional)**

- To delete old workflow artifacts:

1. Install GitHub CLI: `gh`.
2. Run:

```bash
gh auth login  # if needed
gh api repos/:owner/:repo/actions/artifacts | jq '.artifacts[] | select(.expired==false) | .id' | xargs -n1 -I {} gh api -X DELETE repos/:owner/:repo/actions/artifacts/{}
```


## **Lab Review \& Best Practices**

- Document all `cron` jobs (add comments in YAML).
- Avoid job overlaps (add logic/checks if jobs run long).
- Always log outcomes and surface issues (failures, audits).
- Use tags and workflow names for clear identification.

By following this manual, your team will master matrix builds and scheduled workflows, see them in action, and know how to troubleshoot and customize them for real-world use.

