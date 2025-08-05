Great question!

`github.event.inputs` is used **only** in workflows triggered by the **`workflow_dispatch`** event — which allows you to **manually trigger** workflows **from the GitHub UI**, and pass in **custom inputs** like environment, version, flags, etc.

---

## ✅ Where `github.event.inputs` is used?

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Deployment environment'
        required: true
        default: 'staging'
      version:
        description: 'Release version'
        required: false
```

Then in your workflow, you access the values with:

```yaml
${{ github.event.inputs.environment }}
${{ github.event.inputs.version }}
```

---

## 📌 What is `github.event.inputs`?

It's a **dictionary of user-defined inputs** provided via the manual trigger UI in GitHub.

### Example JSON-like representation:

```json
{
  "environment": "staging",
  "version": "v1.0.0"
}
```

These are accessible as:

```yaml
${{ github.event.inputs.environment }}
${{ github.event.inputs.version }}
```

---

## 🧪 Example: Full `workflow_dispatch` with `inputs`

```yaml
name: Manual Deployment

on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy to'
        required: true
        default: 'staging'
      version:
        description: 'Release version (e.g., v1.0.0)'
        required: false

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Echo inputs
        run: |
          echo "Deploying to ${{ github.event.inputs.environment }}"
          echo "Version: ${{ github.event.inputs.version }}"
```

---

## 🔍 Where is the UI to input these?

1. Go to your repo
2. Click on **"Actions"**
3. Select the workflow → Click **"Run workflow"**
4. You’ll see a form with your input fields
5. Click "Run workflow" to trigger with values

---

## 🧠 Other dynamic GitHub context options (besides `inputs`):

* `${{ github.actor }}` – user who triggered the event
* `${{ github.ref }}` – branch or tag
* `${{ github.repository }}` – owner/repo name
* `${{ github.event_name }}` – name of the event (`push`, `pull_request`, etc.)
* `${{ github.event.release.tag_name }}` – (if `release` event)
* `${{ github.sha }}` – commit SHA


