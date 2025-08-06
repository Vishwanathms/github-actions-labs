
# example of an for loop

GitHub Actions workflow YAML does not have a native `for` loop syntax like traditional programming languages. Instead, you achieve looping behavior by using **matrix strategy** to run a job or steps multiple times with different parameters, or by using expression syntax with **`steps` that generate multiple commands**.

Here are two common patterns to mimic a "for loop" effect:

### 1. Using a **Matrix Strategy** to loop over multiple values in parallel jobs

This runs multiple instances of a job, each with a different value.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [14, 16, 18]   # looping over these values
    steps:
      - uses: actions/checkout@v4
      
      - name: Use Node.js version ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      
      - run: npm install
      - run: npm test
```

**Explanation:**
This runs 3 separate jobs, one for each Node.js version, effectively looping through the list [].

### 2. Looping inside a **run** step (shell script style)

If you want to run commands sequentially inside a single step, you can write a shell script loop:

```yaml
jobs:
  example:
    runs-on: ubuntu-latest
    steps:
      - name: Loop example in bash
        run: |
          for version in 14 16 18; do
            echo "Testing Node.js version $version"
            # Mock commands, e.g.:
            # nvm use $version
            # npm test
          done
```


### Summary

| Pattern | Usage |
| :-- | :-- |
| Matrix strategy | Parallel execution over multiple values (jobs) |
| Shell script inside `run` step | Sequential looping inside a single step |

If you want an example for a specific language or tool, let me know!

