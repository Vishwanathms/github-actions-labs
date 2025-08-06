
# list the conditions in github actions

GitHub Actions provides flexible conditional logic through the `if` keyword, allowing you to control when entire workflows, jobs, or individual steps run. Here is a list of the main conditions and their usage:

## Types of Conditions in GitHub Actions

### 1. **Workflow, Job, and Step Level**

- **Workflow-level conditions**: You control these with the `on` field (e.g., on: push, pull_request), which determines when workflows are triggered.
- **Job-level conditions**: Add an `if:` at the job level to control whether an entire job should run.
- **Step-level conditions**: Place `if:` on a step to decide whether a single step should execute.


### 2. **Condition Syntax**

- Basic:

```yaml
if: <expression>
```

The expression can use contexts, functions, or logical operators (`&&`, `||`).


#### **Examples:**

- **Only run on main branch:**

```yaml
if: github.ref == 'refs/heads/main'
```

- **Run on push events:**

```yaml
if: github.event_name == 'push'
```


### 3. **Common Condition Functions**

- **success()**: Run if all previous steps/jobs succeeded (default behavior).

```yaml
if: success()
```

- **failure()**: Run if any previous steps/jobs failed.

```yaml
if: failure()
```

- **cancelled()**: Run if the workflow/job was canceled.

```yaml
if: cancelled()
```

- **always()**: Always run the step/job, regardless of previous outcomes.

```yaml
if: always()
```

Use carefully to avoid running critical tasks on failure or cancel unless necessary.


### 4. **Using Contexts and Expressions**

- **GitHub Context**: Access info about the repo, workflow, event, etc.

```yaml
if: github.repository == 'my-org/prod-repo'
```

- **Needs Context**: Check results or outputs from dependent jobs.

```yaml
if: needs.build.result == 'success'
```

- **Steps Context**: Refer to previous step outputs.

```yaml
if: steps.example-step.outputs.output_var == 'ok'
```

- **Environment Variables**:

```yaml
if: env.MY_VAR == 'value'
```


### 5. **Logical Combinations**

- **AND**:

```yaml
if: github.event_name == 'push' && success()
```

- **OR**:

```yaml
if: github.ref == 'refs/heads/main' || github.ref == 'refs/heads/dev'
```

- **Negation** (use `${{ }}` if starting with `!`):

```yaml
if: ${{ !cancelled() }}
```


### 6. **No Native Else/Else-If**

- GitHub Actions doesn’t have an explicit `else`/`else-if` keyword.
- Simulate with multiple steps using bash logic, or build mutually exclusive `if:` expressions for steps.


### **Summary Table of Common Functions and Contexts**

| Condition | Usage Example | Description |
| :-- | :-- | :-- |
| success() | `if: success()` | All prev steps/jobs succeeded |
| failure() | `if: failure()` | Any prev jobs failed |
| cancelled() | `if: cancelled()` | Workflow/job was cancelled |
| always() | `if: always()` | Always run (use with caution!) |
| github Context | `if: github.event_name == 'push'` | Info about event, repo, actor, etc. |
| needs Context | `if: needs.test.result == 'success'` | Info/status from other jobs (when using needs) |
| steps Context | `if: steps.foo.outputs.bar == 'baz'` | Info about outputs of previous steps |
| env Context | `if: env.ENV_NAME == 'prod'` | Info from set environment variables |

**Tip:**
For full control, consult [GitHub’s official docs on job conditions](https://docs.github.com/en/actions/how-tos/writing-workflows/choosing-when-your-workflow-runs/using-conditions-to-control-job-execution)[^1][^2][^3].

You can combine these conditions to make your workflows smart, dynamic, and efficient.

<div style="text-align: center">⁂</div>

[^1]: https://docs.github.com/en/actions/how-tos/writing-workflows/choosing-when-your-workflow-runs/using-conditions-to-control-job-execution

[^2]: https://docs.github.com/actions/reference/evaluate-expressions-in-workflows-and-actions

[^3]: https://runs-on.com/github-actions/conditions/

[^4]: https://docs.github.com/actions/reference/workflow-syntax-for-github-actions

[^5]: https://docs.github.com/en/enterprise-server@3.14/actions/writing-workflows/choosing-when-your-workflow-runs/using-conditions-to-control-job-execution

[^6]: https://thomasthornton.cloud/2023/08/11/if-elseif-or-else-in-github-actions/

[^7]: https://www.youtube.com/watch?v=swS_7tMqHE0

[^8]: https://stackoverflow.com/questions/65844033/conditional-needs-in-github-actions

[^9]: https://cicube.io/blog/github-actions-if-condition/

[^10]: https://stackoverflow.com/questions/60916931/github-actions-does-the-if-have-an-else

[^11]: https://docs.github.com/articles/getting-started-with-github-actions

[^12]: https://stackoverflow.com/questions/72872929/how-to-do-if-else-if-conditions-in-github-actions

[^13]: https://hungvu.tech/advanced-github-actions-conditional-workflow

[^14]: https://docs.github.com/actions/learn-github-actions/events-that-trigger-workflows

