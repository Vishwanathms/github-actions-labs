# how to publish into github market place the aboce actions

To **publish your composite GitHub Action to the GitHub Marketplace**, follow this detailed step-by-step guide:

## 1. Repository Preparation

- **Make your repository public**. Only public repositories can be added to the Marketplace[^1][^2].
- Ensure your repository contains ONLY what's necessary for the action:
    - The main `action.yml` in the repository root if you want that action listed (sub-action files inside folders are not listed).
    - Remove workflow files from your action repo. Marketplace does not allow workflows in the repo[^1].


## 2. Add Required Metadata

In your `action.yml`, include these required fields, and optionally enhance discoverability by specifying branding:

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
branding:
  icon: 'user'
  color: 'blue'
```

- **`branding`** (optional, but recommended): Pick an [icon](https://feathericons.com/) and color to help your action stand out in the Marketplace[^3][^4].


## 3. Commit and Push

```bash
git add action.yml
git commit -m "Add metadata for GitHub Marketplace"
git push origin main
```


## 4. Tag a Release

Versioning is required:

```bash
git tag -a v1.0.0 -m "Initial Marketplace release"
git push origin v1.0.0
```


## 5. Publish to the Marketplace

1. **On GitHub**, go to your repo’s main page.
2. After you push your `action.yml`, you should see a banner suggesting you publish to the Marketplace, or:
3. Click the **Releases** section, then **Draft a new release**.
4. In the release creation page:
    - **Check the box for “Publish this Action to the GitHub Marketplace”**
    - *If the box is disabled:* The repo owner must accept the [GitHub Marketplace Developer Agreement]—follow the provided link or have your organization owner do so[^1][^4][^5].
5. Assign a **release title** and select a **version** (matches your tag, e.g., `v1.0.0`).
6. Choose matching **primary** and **secondary categories** to help users find your action.
7. Complete other fields (description, README, etc.).
8. Click **Publish release**.

## 6. Verification

Once published, your Action will be visible in the [GitHub Marketplace][^1][^5]. Anyone can find and use your action as:

```
on: [push]

jobs:
  greet:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: cloudvishwa/greet-user@v1.0
        with:
          name: "World"
          items: "one,two,three"
```


## 7. Common Errors \& Fixes

| Error/Issue | Resolution |
| :-- | :-- |
| **"Publish" checkbox is disabled** | Accept the Marketplace Developer Agreement as repo/org owner[^1] |
| **Missing metadata errors** | Ensure `name` \& `description` present in `action.yml` and values are unique/valid[^1] |
| **Action not listed after publishing** | Repo must be public and not contain workflows; `action.yml` must be at repo root[^1][^2] |
| **Name conflicts** | Action name (in `action.yml`) must be unique (not match users/orgs/built-in names)[^1][^4] |
| **Branding not showing** | Only supported [icon names](https://feathericons.com/) and pre-set colors are allowed[^3][^4] |

For best results:

- Include a descriptive `README.md` with usage instructions and examples.
- Use semantic versioning for tags.
- Maintain your action with updates and document all versions.

You can unpublish a specific version by deleting its release/tag, but the action overall will remain in the Marketplace as long as at least one release is published[^6].

