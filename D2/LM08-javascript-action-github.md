# consolidate all the results of each search , with the code, errors, fix , and convert to an extensive lab manual

Creating a custom JavaScript GitHub Action that multiplies two numbers — with all steps, common errors, and fixes — consolidated into an extensive lab manual:

# Lab Manual: Building, Debugging, and Publishing a JavaScript GitHub Action for Multiplying Two Numbers

## Objective

Create a simple GitHub Action in JavaScript that takes two numbers as inputs, multiplies them, outputs the result, and can be reused in workflows. Learn how to fix common errors (missing dependencies) and prepare your action for sharing.

## Part 1: Creating the Action

### Step 1: Setup Folder Structure

Create the following folder in your repository:

```
.github/actions/multiply-numbers/
```


### Step 2: Create `action.yml`

Define your action metadata and I/O in the file `.github/actions/multiply-numbers/action.yml`:

```yaml
name: 'Multiply Numbers'
description: 'Multiplies two input numbers'
inputs:
  num1:
    description: 'First number'
    required: true
  num2:
    description: 'Second number'
    required: true
outputs:
  result:
    description: 'Multiplication result'
runs:
  using: 'node16'
  main: 'index.js'
```


### Step 3: Create JavaScript Logic `index.js`

In `.github/actions/multiply-numbers/index.js`:

```javascript
const core = require('@actions/core');

try {
  const num1 = parseFloat(core.getInput('num1'));
  const num2 = parseFloat(core.getInput('num2'));

  if (isNaN(num1) || isNaN(num2)) {
    throw new Error('Inputs must be numbers');
  }

  const product = num1 * num2;

  core.setOutput('result', product.toString());
  console.log(`Result: ${product}`);
} catch (error) {
  core.setFailed(error.message);
}
```


### Step 4: Initialize npm and Install Required Package

In the `multiply-numbers` folder, initialize npm and install `@actions/core`:

```bash
cd .github/actions/multiply-numbers
npm init -y
npm install @actions/core
```


### Step 5: Commit Everything Including Dependencies

Because GitHub runners don't run `npm install` for local JavaScript actions, you **must commit**:

- `package.json`
- `package-lock.json`
- The `node_modules/` directory with `@actions/core` inside.


## Part 2: Using Your Action in a Workflow

Example `.github/workflows/test-multiply.yml`:

```yaml
name: Test Multiply Action

on:
  push:
    branches: [main]

jobs:
  multiply:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Multiply numbers
        uses: ./.github/actions/multiply-numbers
        id: multiply
        with:
          num1: '3'
          num2: '5'

      - run: echo "The product is ${{ steps.multiply.outputs.result }}"
```

- `id: multiply` identifies the step and lets you access its outputs via `${{ steps.multiply.outputs.result }}`.
- When you push to `main`, the action will run and print the product.


## Part 3: Common Errors and How to Fix Them

### Error 1: Cannot find module '@actions/core'

```text
Error: Cannot find module '@actions/core'
```

**Cause:** Your action is missing the `@actions/core` dependency at runtime.

**Fix:**

- Run `npm install @actions/core` in your action folder.
- Commit `package.json`, `package-lock.json`, and **the entire `node_modules` folder** under `.github/actions/multiply-numbers/`.


### Error 2: Cannot find module '@actions/core/lib/core.js'

```text
Error: Cannot find module '/path/to/node_modules/@actions/core/lib/core.js'. Please verify that the package.json has a valid "main" entry
```

**Cause:** Same as above, missing or improperly installed `@actions/core`.

**Fix:**

- Ensure npm install completed without errors.
- Check that the `node_modules/@actions/core/package.json` file exists and has a `main` entry pointing to `lib/core.js`.
- Commit all dependency files.


## Part 4: Best Practices and Alternatives

### Bundling with `@vercel/ncc` (Recommended for Public Actions)

Avoid committing `node_modules` by bundling everything into a single file:

1. Install the bundler:

```
npm install --save-dev @vercel/ncc
```

2. Add build script in `package.json`:

```json
"scripts": {
  "build": "ncc build index.js -o dist"
}
```

3. Build the action:

```
npm run build
```

4. Update `action.yml` to use bundled file:

```yaml
runs:
  using: 'node16'
  main: 'dist/index.js'
```

5. Commit the `dist/` folder instead of `node_modules`.

### Alternative Action Types

| Action Type | Description | Language/Tech | Use Case |
| :-- | :-- | :-- | :-- |
| Composite Action | YAML-defined step sequence | YAML Shell Steps | Simple commands reuse, easy to maintain |
| JavaScript Action | JavaScript running on Node.js | JavaScript | Complex logic, GitHub API interaction |
| Docker Container | Custom container with any tools/libraries needed | Docker | Full environment control, multi-language support |

## Final Notes

- Local JavaScript actions **must include dependencies** since the runner does not install them.
- Use `id` to access outputs of any step in your workflow.
- Use semantic versioning and possibly bundle your action for distribution.
- Test actions locally with tools like [act](https://github.com/nektos/act) if needed.

This lab manual guides you end-to-end from creating to fixing your JavaScript-based multiplication GitHub Action, including workflow usage and error troubleshooting. Let me know if you need help creating composite or Docker action versions or publishing to the Marketplace!

