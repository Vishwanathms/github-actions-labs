# Hands-On Lab: Continuous Deployment to AWS \& Heroku with GitHub Actions

This lab guides you through creating a Continuous Deployment (CD) pipeline with GitHub Actions for popular platforms: **AWS (Elastic Beanstalk)** and **Heroku**. Each section provides step-by-step instructions, including prerequisites and dependencies.

## Section 1: Deploying to AWS Elastic Beanstalk

### Prerequisites

- GitHub repository containing your application code
- AWS account with:
    - Elastic Beanstalk environment set up (e.g., Python/Node.js app)
    - IAM user with programmatic access, with `AWSElasticBeanstalkFullAccess` permissions
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html) configured locally (optional for initial manual deploy)
- The Elastic Beanstalk environment/application name


### Step-by-Step Guide

#### Step 1: Store AWS Credentials in GitHub Secrets

On GitHub, go to your repo:
**Settings** → **Secrets and variables** → **Actions**

Add:

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- (Optional) `AWS_REGION` (e.g., `us-east-1`)


#### Step 2: Create `.github/workflows/deploy-to-eb.yml`

```yaml
name: Deploy to AWS Elastic Beanstalk

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: 3.10  # adjust for your app

      - name: Install dependencies
        run: |
          pip install awsebcli

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}

      - name: Deploy to Elastic Beanstalk
        run: |
          eb init YOUR_APPLICATION --platform python-3.10 --region ${{ secrets.AWS_REGION }}
          eb deploy
```

**Replace `YOUR_APPLICATION` and platform as needed.**

#### Step 3: Commit, Push, and Verify

- Commit the workflow file.
- Push to the `main` branch.
- Check Actions tab and your AWS Elastic Beanstalk dashboard for deployment status.


## Section 2: Deploying to Heroku

### Prerequisites

- GitHub repository with app code
- [Heroku account](https://heroku.com/)
- Heroku app created (`heroku create APP_NAME`)
- [Heroku API key](https://dashboard.heroku.com/account)


### Step-by-Step Guide

#### Step 1: Store Heroku Credentials in GitHub Secrets

Add:

- `HEROKU_API_KEY`
- `HEROKU_APP_NAME`


#### Step 2: Create `.github/workflows/deploy-to-heroku.yml`

```yaml
name: Deploy to Heroku

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Deploy to Heroku
        uses: akhileshns/heroku-deploy@v3.13.15
        with:
          heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
          heroku_app_name: ${{ secrets.HEROKU_APP_NAME }}
          heroku_email: ${{ secrets.HEROKU_EMAIL }}
```

*(Make sure you add `HEROKU_EMAIL` as a secret as well.)*

#### Step 3: Commit, Push, and Watch

- Save and commit the workflow.
- Push to `main`.
- GitHub Actions will deploy to Heroku on each push.


## Section 3: General Continuous Deployment Pipeline Steps

1. **Configure your app (package.json/requirements.txt/etc).**
2. **Push code to GitHub.**
3. **GitHub Action workflow checks out code, installs dependencies, builds/tests, triggers deployment step.**
4. **Secrets in GitHub used for credentials—never hard-code secrets.**
5. **Monitor deployment logs on GitHub Actions and on your deployment platform.**

## Dependencies Summary

| Dependency | AWS Deployment | Heroku Deployment |
| :-- | :-- | :-- |
| GitHub Repo | Required | Required |
| Platform Account | AWS | Heroku |
| CLI Tool | awsebcli (via pip) | n/a (Uses GitHub Action) |
| Secrets in GitHub | AWS credentials | Heroku API key/Email |
| Application config | `eb init` | `heroku create` |
| GitHub Actions YAML | `deploy-to-eb.yml` | `deploy-to-heroku.yml` |

## Optional: Multiple Environments

- Use a workflow input (`workflow_dispatch.inputs.environment`) to deploy to **dev**, **staging**, or **prod** by referencing different app names or environment variables in your workflow YAML.


### Congratulations!

You now have a **hands-on, end-to-end continuous deployment pipeline setup** for both AWS Elastic Beanstalk and Heroku using GitHub Actions. This process is similar for other platforms (Azure, DigitalOcean, Render), just swap the deployment step with the relevant CLI/Action.

