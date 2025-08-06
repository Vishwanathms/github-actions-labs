
# End-to-End Lab Manual: Testing and Understanding Dependency Caching in GitHub Actions

This lab manual provides a structured, hands-on guide to set up, test, and comprehend dependency caching in GitHub Actions CI workflows for Node.js, Python, and Java (Gradle) projects.

## Lab Objectives

- Configure dependency caching in a GitHub Actions workflow.
- Validate caching behavior by observing build times and cache logs.
- Understand correct cache key strategies and common troubleshooting steps.


## A. Lab Prerequisites

- A GitHub account.
- A repository with a Node.js (`package.json`), Python (`requirements.txt`), or Java (Gradle: `build.gradle`, `pom.xml`, or `build.gradle.kts`) project file. You can create a simple sample project or fork an existing repository.
- Basic familiarity with GitHub Actions and YAML workflow files.


## B. Lab Steps

### 1. Fork or Create a Sample Repository

- Fork an existing sample project or create a new one. For this lab, you may use:
    - **Node.js:** A basic `package.json` project (e.g., [nodejs/node-hello-world])
    - **Python:** Any project with a `requirements.txt`
    - **Java (Gradle):** A simple Gradle-based demo project


### 2. Add a GitHub Actions Workflow YAML

1. **Navigate to your repository** on GitHub.
2. **Create the `.github/workflows/ci.yml` file** (if not already present).

#### Example: Node.js Workflow (`.github/workflows/ci.yml`)

```yaml
name: Node.js CI with Caching
on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
      
      # Cache npm dependencies
      - name: Cache npm dependencies
        uses: actions/cache@v4
        with:
          path: ~/.npm
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-node-

      - name: Install Dependencies
        run: npm install

      - name: Run Tests
        run: npm test
```

> **Replace the caching block with a Python or Gradle example as required.**

#### Python Example Cache Step

```yaml
      - name: Cache pip dependencies
        uses: actions/cache@v4
        with:
          path: ~/.cache/pip
          key: ${{ runner.os }}-pip-${{ hashFiles('**/requirements.txt') }}
          restore-keys: |
            ${{ runner.os }}-pip-
```


#### Gradle Example Cache Step

```yaml
      - name: Cache Gradle wrapper and caches
        uses: actions/cache@v4
        with:
          path: |
            ~/.gradle/caches
            ~/.gradle/wrapper
          key: ${{ runner.os }}-gradle-${{ hashFiles('**/gradle/wrapper/gradle-wrapper.properties') }}
          restore-keys: |
            ${{ runner.os }}-gradle-
```


### 3. Commit and Push the Workflow

- Commit your changes with a message like `Add CI workflow with dependency caching`.
- Push to GitHub.


### 4. Trigger the Workflow

- Push a commit or open a pull request to activate the workflow run (Actions tab).
- Wait until the workflow finishes.


### 5. Observe the Caching Behavior

- Go to the **Actions** tab in your repository.
- Open your workflow run.
- **Observe the output for the cache step**:
    - On the **first run**, the cache will be created/saved at the end.
    - On **subsequent runs**, you should see logs like `Cache restored from key:` and faster install times for dependencies.
- **Compare "Install Dependencies" step times** between runs.


### 6. Modify Dependency File and Push

- Make a minor edit to your `package-lock.json`, `requirements.txt`, or `gradle-wrapper.properties`.
- Commit and push.
- Observe the workflow run:
    - The cache key will change, resulting in a cache miss (new cache created).
    - The dependency install time will revert to initial levels, then improve again in following runs.


## C. Validation and Troubleshooting

- **Check cache logs** in the Actions UI to ensure caching is working as expected ("Cache restored" or "Cache not found").
- If cache is not working:
    - Verify correct paths and key patterns.
    - Ensure file patterns are correct (`**/package-lock.json`, etc.).
- Rerun jobs manually to compare speedups between runs with and without cache.


## D. Result Interpretation

- **First run:** Full install, cache created.
- **Second run (no dependency change):** Cache hit, install is faster.
- **After lock file change:** Cache miss, new cache generation.
- **Subsequent runs:** Cache hit again, improved performance.


## E. Clean Up

- Optionally, delete the test repository or workflow.
- You may reuse the workflow in real projects for faster CI.


## F. (Optional) Experiment: Combine Caches

- Some projects use both Node.js and Python — try combining both cache steps in one workflow.
- Experiment with restore-keys for advanced strategies (e.g., fallback on partial matches).


## Conclusion

By following this manual, you should have a working understanding of dependency caching in GitHub Actions, be able to measure its effectiveness, and understand cache key strategies and possible pitfalls. This skillset is transferrable to nearly all modern CI/CD environments.

