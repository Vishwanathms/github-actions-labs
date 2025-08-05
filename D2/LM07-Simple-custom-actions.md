<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# name: 'Greet User'

description: 'Prints a greeting message'
inputs:
  name:
    description: 'Name of the user'
    required: true
  items:
    description: 'comma seperated list of items'
    required: true
runs:
  using: 'composite'
  steps:
    - run: echo "Hello, \${{ inputs.name }}!"
      shell: bash

this is action.yml

below is workflow yaml

on: [push]

jobs:
  greet:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: ./.github/actions/greet-user
        with:
          name: "World"
          items: "one,two,three"

create an comprehensive and extendive lab manual , covering all the detailed steps and errors and fix

Certainly! Below is a comprehensive and extensive lab manual for creating, using, and troubleshooting your custom GitHub Action given your `action.yml` and workflow YAML. This covers **all detailed steps, common errors, and fixes**.

# Lab Manual: Creating and Using a Custom Composite GitHub Action

## Table of Contents

1. Overview
2. Pre-requisites
3. Step 1: Create the Custom Action (action.yml)
4. Step 2: Create the Workflow File
5. Step 3: Run and Verify the Workflow
6. Troubleshooting and Common Errors
7. FAQs

## 1. Overview

This lab will guide you through creating a GitHub Composite Action that prints a greeting message and accepts two inputs:

- `name` (required): The name of the user to greet.
- `items` (required): A comma-separated list of items (though your current action does not process this yet).

You will create the action, define a GitHub Actions workflow to invoke it, and understand common errors and fixes.

## 2. Pre-requisites

- A GitHub repository with admin or write access.
- Familiarity with GitHub, GitHub Actions.
- A basic text editor or IDE (VS Code recommended).
- Installed Git on your local machine (if running locally).
- `git` configured, and ability to push to GitHub.


## 3. Step 1: Create the Custom Composite Action

### 3.1 Create the directory structure

Inside the root of your repository, create the following path for your custom action:

```plaintext
/.github/actions/greet-user/
```


### 3.2 Create `action.yml` inside `greet-user` folder

Create a file `action.yml` with the following content:

```yaml
name: 'Greet User'
description: 'Prints a greeting message'
inputs:
  name:
    description: 'Name of the user'
    required: true
  items:
    description: 'comma separated list of items'
    required: true
runs:
  using: 'composite'
  steps:
    - run: echo "Hello, ${{ inputs.name }}!"
      shell: bash
```

**Note:**

- You have two inputs, but currently only `name` is used in the echo statement.
- `items` input exists but is unused currently.


### 3.3 Save and Commit

```bash
git add .github/actions/greet-user/action.yml
git commit -m "Add greet-user composite action"
git push origin main
```


## 4. Step 2: Create the Workflow File

### 4.1 Create Workflow YAML

Create a GitHub workflow file `.github/workflows/greet.yml` with the following content:

```yaml
name: Greet Workflow

on: [push]

jobs:
  greet:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: ./.github/actions/greet-user
        with:
          name: "World"
          items: "one,two,three"
```


### 4.2 Save, Commit, and Push

```bash
git add .github/workflows/greet.yml
git commit -m "Add workflow to run greet-user action"
git push origin main
```


## 5. Step 3: Run and Verify the Workflow

1. Go to your GitHub repository.
2. Click on the **Actions** tab.
3. Select the workflow named "Greet Workflow".
4. Check the latest run triggered by your push event.
5. Inside the Logs, expand the `greet` job.
6. Verify output includes:

```
Hello, World!
```


## 6. Troubleshooting and Common Errors

Here we list common errors based on your current setup and how to fix them.

### 6.1 Error: "Invalid Workflow File"

**Possible causes:**

- Incorrect YAML indentation.
- Missing or misspelled keys.
- Invalid syntax for composite action or inputs.

**Fix:**

- Use YAML linters or GitHub workflow schema checkers.
- Ensure `action.yml` and workflow YAML indentations are correct.
- Example: Action `inputs` should be properly indented under `inputs`, etc.


### 6.2 Error: Action "uses" path not found

**Message:**

```
Error: Resource not accessible by integration
or
The repository or path ./.github/actions/greet-user does not exist
```

**Cause:**

- Path `.github/actions/greet-user` incorrect or action missing.
- Workflow is trying to use a relative path but action folder not pushed.

**Fix:**

- Verify the folder `.github/actions/greet-user` exists in your repo.
- Verify `action.yml` exists inside that folder.
- Ensure your workflow uses the exact relative path to the action folder.


### 6.3 Error: Input Not Provided or Missing Inputs

**Message:**

```
The workflow was not valid. Missing required input 'name' or 'items'
```

**Cause:**

- You did not provide inputs declared as `required: true`.

**Fix:**

- Include all required inputs under `with` in workflow YAML.
- Remember inputs are case sensitive.


### 6.4 Error: Echo output not showing the expected value

**Symptom:**

- Instead of `Hello, World!`, you get `Hello, !` or `Hello, ${{ inputs.name }}!` literally.

**Cause:**

- Composite actions in GitHub Actions evaluate `${{ }}` differently inside `runs`.

**Fix:**

- For composite actions, input variables are referenced using `${{ inputs.name }}` in `action.yml` correctly.
- Make sure you are not confusing this with script or shell runtime variables.
- The current approach is correct.


### 6.5 The `items` input is not used

Currently, your action does not utilize `items`. You could extend the action to process or print it.

Example modification inside `action.yml`:

```yaml
runs:
  using: 'composite'
  steps:
    - run: |
        echo "Hello, ${{ inputs.name }}!"
        echo "Items: ${{ inputs.items }}"
      shell: bash
```


## 7. FAQs

### Q1: Can composite actions use scripts?

- Composite actions can run shell commands or use `run` steps, but cannot execute JavaScript code directly.
- For complex logic, use JavaScript or Docker container actions.


### Q2: Can I pass secrets to composite actions?

- Pass secrets as inputs marked `required: false`.
- Use `secrets:` fields or environment variables in the workflow.


### Q3: Can composite actions call other actions?

- Yes, composite actions can call other actions inside their steps.


# Summary

You have created a composite GitHub Action to greet a user, designed a workflow to run it on push events, and learned how to troubleshoot common problems. Testing on GitHub Actions interface is critical to ensure correctness.

If you want me to add instructions for specific enhancements or troubleshoot an error you encountered, let me know!

