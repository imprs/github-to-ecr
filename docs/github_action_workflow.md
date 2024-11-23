### GitHub Action Workflow Setup

Follow the steps below to configure and use GitHub Actions for building and pushing Docker images to your AWS ECR repository.

#### Necessary Steps

1. We will create three GitHub secrets and you can create them from `repository` -> `Settings` -> `Secrets and variables` -> `Actions` -> `New Repository Secret`.

   - `ACCOUNT_ID`: AWS account number

   - `IAM_ROLE`: AWS IAM Role

   - `REGION`: AWS Region

2. You will also have to modify the name of your ECR repository (i.e., `<repo_name>`) and tag (`<tag>`) in the workflow.



#### Workflow Explanation

This section provides a breakdown of the GitHub Actions workflow.

1. First, we specify when a job should be triggered and job details:

   ```yaml
   on: [push, workflow_dispatch]
   jobs:
     push-to-aws: # name of our job
   ```

2. Now, we add the following permissions. `id-token: write` allows GitHub's OIDC provider to create JSON Web Token (JWT) for each run. For more information visit [adding permissions settings on GitHub docs](https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services#adding-permissions-settings):

   ```yaml
   permissions:
     id-token: write # This is required for requesting the JWT
     contents: read  # This is required for actions/checkout
   ```

3. We will use [aws-actions/configure-aws-credentials](https://github.com/aws-actions/configure-aws-credentials) action, which uses the JWT created in the previous step and exchanges it for the AWS cloud access token:

   ```yaml
   - name: Configure AWS credentials from Test account
     uses: aws-actions/configure-aws-credentials@v4
     with:
       role-to-assume: arn:aws:iam::${{ secrets.ACCOUNT_ID }}:role/${{ secrets.IAM_ROLE }}
       aws-region: ${{ secrets.REGION }}
   ```

4. Once AWS credentials are configured, use the [aws-actions/amazon-ecr-login](https://github.com/aws-actions/amazon-ecr-login) action to log in to your AWS ECR repository:

   ```yaml
   - name: Login to AWS ECR
     id: login-ecr
     uses: aws-actions/amazon-ecr-login@v2
   ```

5. Finally, build the Docker image and push it to the ECR repository:

   ```yaml
   - name: Build and push the image
     run: |
       docker build -f docker/Dockerfile -t ${{ secrets.ACCOUNT_ID }}.dkr.ecr.${{ secrets.REGION }}.amazonaws.com/<repo_name>:<tag> .
       docker push ${{ secrets.ACCOUNT_ID }}.dkr.ecr.${{ secrets.REGION }}.amazonaws.com/<repo_name>:<tag>
   ```


