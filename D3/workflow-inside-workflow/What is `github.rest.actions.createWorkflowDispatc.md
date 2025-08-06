
### What is `github.rest.actions.createWorkflowDispatch()`?

**`github.rest.actions.createWorkflowDispatch()`** is a function available in the `actions/github-script` Action, which exposes the GitHub REST API to workflow scripts.
This specific function lets you **programmatically trigger another GitHub Actions workflow that is configured with the `workflow_dispatch` event** in the same (or even another) repository. It is the JavaScript binding to the GitHub REST API endpoint that triggers a new workflow run.

**Typical Use-cases:**

- Chain workflows (e.g., CI then CD).
- Pass custom input parameters to the triggered workflow.
- Orchestrate complex builds/deployments.


### Example: Triggering Another Workflow

#### 1. The Target Workflow

Suppose you have this in `.github/workflows/build.yml`:

```yaml
on:
  workflow_dispatch:
    inputs:
      current_version:
        description: "Version to build"
        required: false
        default: ""
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Building version ${{ github.event.inputs.current_version }}"
```


#### 2. The Triggering Workflow

Add a job to call the workflow dispatch API using `actions/github-script@v6`:

```yaml
jobs:
  trigger-build:
    runs-on: ubuntu-latest
    steps:
      - name: Trigger build workflow
        uses: actions/github-script@v6
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            await github.rest.actions.createWorkflowDispatch({
              owner: context.repo.owner,
              repo: context.repo.repo,
              workflow_id: 'build.yml',
              ref: 'main',
              inputs: {
                current_version: 'v1.2.3'
              }
            });
```

**Explanation**:

- `workflow_id`: The target workflow file name (`build.yml`) or the workflow's numeric ID.
- `ref`: The branch or tag the workflow should run on (usually `'main'`).
- `inputs`: Map of input names and values to send to the workflow.

**Security note**:

- The triggered workflow **must** have `on: workflow_dispatch`.
- The user or token must have appropriate permissions (`repo`/`workflow` scope for PAT, or use `${{ secrets.GITHUB_TOKEN }}`).


#### References and Further Reading

- [Official GitHub REST API Docs](https://docs.github.com/rest/actions/workflows#create-a-workflow-dispatch-event)[^1][^2]
- [GitHub Actions: Use the GITHUB_TOKEN with workflow_dispatch](https://github.blog/changelog/2022-09-08-github-actions-use-github_token-with-workflow_dispatch-and-repository_dispatch/)[^3]
- Real-world usage in `actions/github-script` [GitHub Issues][^4][^5][^6]

**Summary:**
Use `github.rest.actions.createWorkflowDispatch()` **in a workflow** (via `actions/github-script`) to trigger another workflow that supports `workflow_dispatch`, passing branch/ref and any needed inputs. It's the programmatic equivalent of clicking "Run workflow" in the Actions UI or using the REST API.

Let me know if you'd like a multi-repo or cross-branch example!

<div style="text-align: center">⁂</div>

[^1]: https://docs.github.com/rest/actions/workflows

[^2]: https://docs.github.com/en/rest/actions/workflows

[^3]: https://github.blog/changelog/2022-09-08-github-actions-use-github_token-with-workflow_dispatch-and-repository_dispatch/

[^4]: https://github.com/actions/github-script/issues/250

[^5]: https://github.com/actions/github-script/issues/128

[^6]: https://saktidwicahyono.name/blogs/action-trigger-other-action/

[^7]: https://stackoverflow.com/questions/78416315/github-actions-createworkflowdispatch

[^8]: https://github.com/actions/github-script/issues/351

[^9]: https://docs.github.com/actions/learn-github-actions/events-that-trigger-workflows

[^10]: https://github.com/marketplace/actions/workflow-dispatch-and-wait

[^11]: https://github.com/marketplace/actions/workflow-dispatch

[^12]: https://docs.github.com/en/rest/actions/workflow-runs

[^13]: https://github.com/actions/github-script/issues/246

[^14]: https://octokit.github.io/rest.js/

