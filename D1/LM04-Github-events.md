### 🔍 **Exploring GitHub Events in GitHub Actions – Detailed Explanation**

GitHub Actions relies on **events** to trigger workflows. These **events** are activities that occur on GitHub repositories—like pushes, pull requests, issue creation, or schedule-based timers—that tell GitHub Actions *when* to run a workflow.

---

## 🧩 **What Are GitHub Events?**

A **GitHub event** is any activity or trigger recognized by GitHub that can start a GitHub Actions workflow. You define the event in your workflow YAML under the `on:` key.

---

## 🛠️ **Common GitHub Events**

### 1. **push**

* Triggered when you push code to a branch.
* Common for CI/CD workflows.

```yaml
on:
  push:
    branches:
      - main
      - 'release/*'
```

📌 **Use Cases**:

* Run tests/linting when code is pushed
* Build Docker image
* Deploy app

---

### 2. **pull\_request**

* Triggered when a pull request is opened, updated, closed, or merged.

```yaml
on:
  pull_request:
    branches:
      - main
```

📌 **Use Cases**:

* Code review checks
* Run CI before merge
* Static code analysis

---

### 3. **workflow\_dispatch**

* Manual trigger via the GitHub UI.

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Choose environment'
        required: true
        default: 'dev'
```

📌 **Use Cases**:

* Manual deployment
* Trigger workflow with custom inputs

---

### 4. **schedule**

* Cron-based event. Useful for running periodic tasks.

```yaml
on:
  schedule:
    - cron: '0 0 * * *' # Runs every day at midnight UTC
```

📌 **Use Cases**:

* Backups
* Security scanning
* Automated reporting

---

### 5. **release**

* Triggered when a release is created, edited, or published.

```yaml
on:
  release:
    types: [published]
```

📌 **Use Cases**:

* Notify teams
* Deploy tagged versions
* Publish binaries

---

### 6. **issue\_comment**

* Triggered when a comment is created on an issue or pull request.

```yaml
on:
  issue_comment:
    types: [created]
```

📌 **Use Cases**:

* ChatOps (triggering workflows via PR comments)
* Bots to label issues based on comments

---

### 7. **issues**

* Triggered on issue lifecycle events.

```yaml
on:
  issues:
    types: [opened, labeled, closed]
```

📌 **Use Cases**:

* Auto-assign issues
* Notify teams
* Labeling based on content

---

### 8. **deployment / deployment\_status**

* Triggered when a deployment is created or its status is updated.

```yaml
on: deployment
```

📌 **Use Cases**:

* Hook into external deployment tools
* Validate deployment results

---

### 9. **check\_run / check\_suite**

* Triggered when external CI checks complete or are requested.

📌 **Use Cases**:

* Custom CI status reporting
* Integrate with third-party tools

---

### 10. **pull\_request\_target**

* Like `pull_request`, but runs in the context of the base repo (useful for forks).

```yaml
on: pull_request_target
```

📌 **Use Cases**:

* Safer workflows for handling PRs from forks
* Secret access in forked PRs (with caution)

---

## 🔒 **Security Note on Events**

Some events (like `pull_request`) do **not expose secrets** to workflows triggered by PRs from forks. Use `pull_request_target` when you need secrets but understand the risks.

---

## 📋 **Combining Events**

You can combine multiple events in a single workflow:

```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 0 * * 1' # Every Monday
```

---

## 🔄 **Event Payloads**

Each event has a **payload** (contextual data) accessible via `github.event` in the workflow.

Example:

```yaml
run: echo "Triggered by ${{ github.event_name }} on ${{ github.ref }}"
```

---

## 🧪 **Hands-On Examples**

**Manual + Push:**

```yaml
on:
  push:
    branches: [main]
  workflow_dispatch:
```

**Scheduled Test Run:**

```yaml
on:
  schedule:
    - cron: '30 9 * * *'
```

**Trigger by Issue Comment:**

```yaml
on:
  issue_comment:
    types: [created]
```

---

## ✅ Summary

| Event Type          | Triggered When...         | Typical Use Case              |
| ------------------- | ------------------------- | ----------------------------- |
| `push`              | Code is pushed to repo    | CI, tests, build, deploy      |
| `pull_request`      | PR is created/updated     | CI validation, test, lint     |
| `workflow_dispatch` | Manually via UI           | Manual deployments or testing |
| `schedule`          | On a timer (cron)         | Nightly builds, audits        |
| `release`           | New release is published  | Packaging, deploy, notify     |
| `issue_comment`     | Comment added to issue/PR | Trigger bot or workflow       |

