
# example of one worflow passing value to another workflow

Certainly! **The standard way for one GitHub Actions workflow to pass values to another is by using reusable workflows** (with `workflow_call`) or by triggering another workflow (`workflow_run` or `repository_dispatch`) and passing required data as inputs, secrets, or through artifacts.

### **Reusable Workflow Example (Recommended Way)**

Suppose you want Workflow A (the “caller”) to provide a value to Workflow B (the “called” or “reusable” workflow):

#### **1. Reusable Workflow (workflow-B.yml)**

Create `.github/workflows/workflow-B.yml` in your repo:

```yaml
name: Reusable Workflow Example

on:
  workflow_call:
    inputs:
      config-path:
        required: true
        type: string
    secrets:
      token:
        required: true

jobs:
  triage:
    runs-on: ubuntu-latest
    steps:
      - name: Show received config path
        run: echo "Config path is: ${{ inputs.config-path }}"
      - name: Show received token
        run: echo "Token is ${{ secrets.token }}"
```


#### **2. Caller Workflow**

Create another workflow (`.github/workflows/workflow-A.yml`):

```yaml
name: Caller Workflow Example
on:
  push:
    branches: [main]

jobs:
  call-reusable-workflow:
    uses: ./.github/workflows/workflow-B.yml
    with:
      config-path: .github/labeler.yml
    secrets:
      token: ${{ secrets.GITHUB_TOKEN }}
```

**How it works:**

- The caller workflow uses the `uses:` keyword to invoke the reusable workflow.
- Values are passed with `with:` (for inputs) and `secrets:` (for secrets).
- The called workflow receives those values at `${{ inputs.config-path }}` and `${{ secrets.token }}`[^1][^2].


### **Alternate: Passing Arbitrary Data Between Completely Separate Workflows**

If workflows are not reusable (e.g., one ends and then triggers the next), you can:

- Publish a file as an artifact in Workflow A.
- Trigger Workflow B with `workflow_run`.
- Workflow B downloads the artifact (e.g., JSON with your custom data) and parses it[^3][^4].

A typical pattern:

1. Workflow A runs, creates a file with data, uploads it as an artifact.
2. Workflow B triggers on `workflow_run` (or `repository_dispatch`) and downloads/parses the artifact.

#### **Summary Table**

| Approach | How to Pass Value | When to Use |
| :-- | :-- | :-- |
| Workflow Call (reusable) | Inputs, Secrets | Direct chaining \& input passing |
| workflow_run/dispatch | Artifacts, API payload | When workflows are entirely separate |

**In practice:**
Use **reusable workflows** for most scenarios, as shown above. For completely independent workflows, use artifacts or dispatch events.

Let me know if you want a full example using the artifact technique as well!

<div style="text-align: center">⁂</div>

[^1]: https://docs.github.com/en/actions/how-tos/reuse-automations/reuse-workflows

[^2]: https://docs.github.com/en/actions/how-tos/sharing-automations/reusing-workflows

[^3]: https://www.youtube.com/watch?v=GewZriF7HV8

[^4]: https://codersee.com/how-to-pass-data-between-workflows-in-github-actions/

[^5]: https://stackoverflow.com/questions/67107607/github-actions-pass-data-from-workflow-to-another-workflow

[^6]: https://docs.github.com/actions/writing-workflows/choosing-what-your-workflow-does/passing-information-between-jobs

[^7]: https://docs.github.com/actions/reference/workflow-syntax-for-github-actions

[^8]: https://docs.github.com/actions/using-workflows/storing-workflow-data-as-artifacts

