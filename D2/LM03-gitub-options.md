Absolutely! Here's a **comprehensive table** of commonly used `github.*` context variables in **GitHub Actions**, organized by their purpose.

---

## ✅ `github` Context Reference Table

| Variable                         | Description                                             | Example Value                                                                                |
| -------------------------------- | ------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| `${{ github.actor }}`            | The user or app that initiated the workflow             | `vishwanathms`                                                                               |
| `${{ github.repository }}`       | The full repository name                                | `owner/repo`                                                                                 |
| `${{ github.repository_owner }}` | The username/org that owns the repo                     | `owner`                                                                                      |
| `${{ github.ref }}`              | The git ref (branch or tag) that triggered the workflow | `refs/heads/main` or `refs/tags/v1.0.0`                                                      |
| `${{ github.ref_name }}`         | The short name of the ref                               | `main` or `v1.0.0`                                                                           |
| `${{ github.ref_type }}`         | Type of ref                                             | `branch` or `tag`                                                                            |
| `${{ github.head_ref }}`         | The source branch of a pull request                     | `feature-xyz`                                                                                |
| `${{ github.base_ref }}`         | The target branch of a pull request                     | `main`                                                                                       |
| `${{ github.sha }}`              | The commit SHA that triggered the workflow              | `ffac537e6cbbf934b08745a378932722df287a53`                                                   |
| `${{ github.event_name }}`       | The name of the event that triggered the workflow       | `push`, `pull_request`, `workflow_dispatch`, `release`                                       |
| `${{ github.event }}`            | The complete webhook payload (JSON object)              | Full event data (access with `github.event.inputs`, `github.event.pull_request.title`, etc.) |
| `${{ github.workflow }}`         | The name of the workflow                                | `CI/CD Pipeline`                                                                             |
| `${{ github.run_id }}`           | Unique ID for the workflow run                          | `1234567890`                                                                                 |
| `${{ github.run_number }}`       | Sequential number of this run                           | `14`                                                                                         |
| `${{ github.job }}`              | The job ID defined in the workflow                      | `build`, `deploy`                                                                            |
| `${{ github.triggering_actor }}` | The user who caused a reusable workflow to run          | `vishwanathms`                                                                               |
| `${{ github.server_url }}`       | GitHub's URL                                            | `https://github.com`                                                                         |
| `${{ github.api_url }}`          | GitHub API base URL                                     | `https://api.github.com`                                                                     |
| `${{ github.graphql_url }}`      | GitHub GraphQL API URL                                  | `https://api.github.com/graphql`                                                             |
| `${{ github.workspace }}`        | The absolute path to the GitHub workspace on the runner | `/home/runner/work/your-repo/your-repo`                                                      |
| `${{ github.token }}`            | Automatically created secret token (used internally)    | *Used by GitHub Actions only*                                                                |

---

## 🧪 Special (Event-Specific) Context

| Variable                                       | Event Type          | Description                             |
| ---------------------------------------------- | ------------------- | --------------------------------------- |
| `${{ github.event.inputs.<input_name> }}`      | `workflow_dispatch` | Input values defined in manual triggers |
| `${{ github.event.release.tag_name }}`         | `release`           | Tag name of the release                 |
| `${{ github.event.pull_request.title }}`       | `pull_request`      | Title of the pull request               |
| `${{ github.event.push.head_commit.message }}` | `push`              | Latest commit message                   |

---

## 🧠 Tip: Use `github.event` to Explore Event Payload

To debug what’s inside the event, add a step like this:

```yaml
- name: Dump event payload
  run: echo "${{ toJson(github.event) }}"
```
