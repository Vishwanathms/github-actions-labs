To **access variable values defined in a GitHub Environment**, you use the `secrets` or `env` context, depending on where the variable is stored in the **Environment configuration**.

---

## 🔐 Step 1: Define Environment and Variables in GitHub

1. Go to your repo → **Settings** → **Environments**
2. Click **"New environment"** (e.g., `dev`, `qa`, `prod`)
3. Inside each environment, define:

   * **Secrets** (sensitive values)
   * **Environment variables** (non-sensitive values)

---

## ✅ Step 2: Use the Variables in Workflow

### 🔹 A. **Secrets**

Refer using `${{ secrets.<NAME> }}`

```yaml
- name: Use secret value
  run: echo "Database password is ${{ secrets.DB_PASSWORD }}"
```

### 🔹 B. **Environment Variables (non-secret)**

GitHub automatically injects these at runtime, and you can access them using shell variable syntax:

```yaml
- name: Use environment variable
  run: echo "My app mode is $MY_ENV_VAR"
```

> No need to use `${{ env.MY_ENV_VAR }}` unless outside of `run:` (e.g., in `if:` or `with:`).

---

## ✅ Full Example with GitHub Environment Variables

```yaml
name: Use GitHub Environment Variables

on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Choose environment'
        required: true
        default: 'dev'
        type: choice
        options:
          - dev
          - qa
          - prod

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: ${{ github.event.inputs.environment }}

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Use environment variable
        run: echo "This environment variable value is: $MY_ENV_VAR"

      - name: Use secret
        run: echo "Secret is ${{ secrets.MY_SECRET }}"
```

---

### ❗Important Notes

* GitHub environment variables are automatically exposed to the shell — you do **not** need to write `env:` explicitly.
* If you want to use environment variables in `if:` conditions or expressions, you can use:

  ```yaml
  if: env.MY_ENV_VAR == 'prod'
  ```


