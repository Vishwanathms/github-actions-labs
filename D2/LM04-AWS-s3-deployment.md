
# Lab Manual: Deploy Files to AWS S3 Using GitHub Actions

## Objective

Learn how to automate deployment of files from a GitHub repository to an AWS S3 bucket using GitHub Actions.

## Prerequisites

Before the lab starts, ensure you have the following:

- An active AWS account.
- AWS CLI configured locally for basic familiarity (optional).
- An S3 bucket created in AWS (you will deploy files here).
- AWS Access Key ID and Secret Access Key with permissions to write to the S3 bucket.
- A GitHub repository containing the files you want to deploy.
- GitHub repository admin access to configure secrets and workflows.


## Step 1: Create an S3 Bucket

1. Sign in to the AWS Management Console.
2. Go to the S3 service.
3. Click **Create bucket**.
4. Enter a unique bucket name (e.g., `vishwa250805`).
5. Select your preferred AWS Region (e.g., `us-east-2`).
6. Review the default settings or customize bucket policies and permissions as needed.
7. Create the bucket.
8. Note the bucket name for later use.

## Step 2: Create an IAM User for GitHub Actions

1. Navigate to the AWS IAM service.
2. Click **Users** > **Add Users**.
3. Enter a user name (e.g., `github-actions-deployer`).
4. Select **Programmatic access**.
5. Click **Next: Permissions**.
6. Attach existing policies or create a custom policy with these permissions for the S3 bucket:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:*"
      ],
      "Resource": [
        "arn:aws:s3:::vishwa250805",
        "arn:aws:s3:::vishwa250805/*"
      ]
    }
  ]
}
```

7. Complete creating the user and save the **Access Key ID** and **Secret Access Key** securely.

## Step 3: Add AWS Credentials as GitHub Secrets

1. Go to your GitHub repository.
2. Click **Settings** > **Secrets and variables** > **Actions** > **New repository secret**.
3. Create the following secrets:
    - `AWS_ACCESS_KEY_ID`: Your IAM user's Access Key ID.
    - `AWS_SECRET_ACCESS_KEY`: Your IAM user's Secret Access Key.

## Step 4: Add GitHub Actions Workflow File

Create a new file in your repository:

`/.github/workflows/deploy-to-s3.yml`

Paste the following workflow content:

```yaml
name: Deploy to S3

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

      - name: Sync files to S3
        uses: jakejarvis/s3-sync-action@v0.5.1
        with:
          args: --acl public-read --delete
        env:
          AWS_S3_BUCKET: vishwa250805
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_REGION: us-east-2
```


## Step 5: Push Your Code and Test

1. Commit and push your workflow file along with any files you want to deploy to the `main` branch.
2. Go to the **Actions** tab in GitHub to monitor the workflow execution.
3. Once the workflow completes successfully, your files should be synced to the S3 bucket with public read access.

## Optional: Verify the Deployment

- Access the files via the S3 bucket public URL:
`https://vishwa250226rep.s3.us-east-2.amazonaws.com/<filename>`


## Additional Notes and Dependencies

- The GitHub runner comes with the required tools preinstalled, such as `aws-cli` and `bash`.
- The action `jakejarvis/s3-sync-action` uses the AWS CLI internally.
- `--acl public-read` flag makes uploaded files publicly readable. Adjust according to your policy.
- `--delete` deletes files in the bucket that no longer exist in your repo folder, ensuring sync.
- To deploy different folders or patterns, customize the `args` property.
- Use GitHub Environments to add approval workflows if deploying to production.


## Troubleshooting

- Check GitHub Actions logs if deployment fails.
- Make sure IAM user policy permissions are correct.
- Validate AWS secrets are correctly entered in GitHub.
- Confirm your S3 bucket region and name match the workflow config.
- Verify that your repository branch triggering the workflow is `main`.


## TIPS:
# the sync is pushing all the hidden files as well,

How can i avoid it

To **exclude hidden files** (such as dotfiles like `.git`, `.env`, `.DS_Store`, etc.) from being synced to S3 in your GitHub Actions workflow using `jakejarvis/s3-sync-action`, add the appropriate `--exclude` patterns to your `args`. You may use multiple `--exclude` flags—one for each pattern you want to skip.

### Example Workflow Addition

Update your sync step like this:

```yaml
      - name: Sync files to S3
        uses: jakejarvis/s3-sync-action@v0.5.1
        with:
          args: >-
            --acl public-read
            --delete
            --exclude '.*'
            --exclude '*/.*'
            --exclude '.git/*'
            --exclude '.github/*'
        env:
          AWS_S3_BUCKET: vishwa250805
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_REGION: 'us-east-2'
```


#### Explanation:

- `--exclude '.*'` skips all hidden files and folders in the root directory (e.g., `.env`, `.gitignore`).
- `--exclude '*/.*'` skips hidden files and folders in subdirectories.
- `--exclude '.git/*'` skips everything inside the `.git` directory (extra cautious).
- `--exclude '.github/*'` skips everything inside the `.github` directory.
- You can add more `--exclude` rules for any other hidden or unwanted files.


#### Notes:

- Each `--exclude` can only take one pattern. Use multiple flags for multiple exclusions.
- The single quotes around patterns are required for proper shell interpretation on Linux runners[^1][^2][^3].

This configuration will ensure hidden files are **not uploaded** to your S3 bucket.

