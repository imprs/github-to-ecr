### Create an IAM Role

Next, we will create an IAM role that uses the identity provider that we just set up.

#### Steps to Create the Role

1. Go to **IAM Roles** section in the AWS Console.
2. Click **Create Role**.
3. Under **Select trusted entity**:
     1. Choose **Web Identity** as the **Trusted Entity Type**.
     2. In **Web Identity**:
        - Under the the **Identity provider**, select  `token.actions.githubusercontent.com`.
        - For **Audience**, select `sts.amazonaws.com`.
        - Under **GitHub organization**, specify your GitHub organisation (e.g., `imprs` in my case).
        - Under **GitHub Repository**, name of your repository.
        - Optionally, you can specify the name of the **branch** or leave it empty if access is needed across all branches.



​	<img src="/docs/images/create_iam_role.png" alt="create_iam_role" style="zoom:70%;" />





#### Permissions

We won’t add any permissions to the IAM role at this stage.
#### Name Review and Create

1. In the **Role details** section, provide a name for the role (e.g., `gh-actions-role`).

2. Trust policy should be automatically created but verify the following fields:

   - **AWS Account ID** (`acc_id`)

   - **Audience** (`aud`): Make sure it is `sts.amazonaws.com`.

   - **Subject** (`sub`): Confirm it follows the format `<org_name>/<repo_name>` (e.g., `imprs/github-to-ecr`).

     ```json
     {
         "Version": "2012-10-17",
         "Statement": [
             {
                 "Effect": "Allow",
                 "Principal": {
                     "Federated": "arn:aws:iam::<acc_id>:oidc-provider/token.actions.githubusercontent.com"
                 },
                 "Action": "sts:AssumeRoleWithWebIdentity",
                 "Condition": {
                     "StringEquals": {
                         "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
                     },
                     "StringLike": {
                         "token.actions.githubusercontent.com:sub": "repo:<org_name>/<repo_name>:*"
                     }
                 }
             }
         ]
     }
     ```


3. And we create the role.



#### Going Back to Permissions

Now we update the permissions for our role.



<img src="/docs/images/create_inline_policy.png" alt="create_inline_policy" style="zoom:70%;" />


1. Navigate to the **IAM Roles** and select the newly created role.
2. Go to the **Permissions** tab and select **Add permission**:
     - *Option A*: Select **Create inline policy** to define permissions within the role. We will follow this option here.
     - *Option B*: Create reusable IAM policies beforehand and select **Attach policies** to apply pre-existing policies to the role. This is a good option if you plan to reuse these permissions elsewhere.



​	<img src="/docs/images/create_policy.png" alt="create_policy" style="zoom:70%;" />



3. Now, in the **Specify permissions** section:

   1. Select **Elastic Container Repository** and configure the following:

      - In **Read** select,
        - `BatchCheckLayerAvailability`
        - `BatchGetImage`
        - `GetDownloadUrlForLayer`

      - In **Write**:
        - `CompleteLayerUpload`
        - `InitiateLayerUpload`
        - `PutImage`
        - `UploadLayerPart`

4. Under **Resources**, configure access to your specific **ECR** repository:

   - **Resource region**: `eu-west-2` (based on the region that you want to use)

   - **Resource repository name**: `github-to-ecr` (your **ECR** repository name)

5. Click **Next** and save your changes.

​	<img src="/docs/images/select_permissions.png" alt="select_permissions" style="zoom:70%;" />



- Or if you prefer the full JSON, here it is:

  ```json
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Sid": "VisualEditor0",
              "Effect": "Allow",
              "Action": [
                  "ecr:GetDownloadUrlForLayer",
                  "ecr:BatchGetImage",
                  "ecr:CompleteLayerUpload",
                  "ecr:UploadLayerPart",
                  "ecr:InitiateLayerUpload",
                  "ecr:BatchCheckLayerAvailability",
                  "ecr:PutImage"
              ],
              "Resource": "arn:aws:ecr:<region>:<acc_id>:repository/<ecr_repo_name>"
          },
          {
              "Sid": "VisualEditor1",
              "Effect": "Allow",
              "Action": "ecr:GetAuthorizationToken",
              "Resource": "*"
          }
      ]
  }
  ```





<img src="/docs/images/policy_resources.png" alt="IAM_Permissions_Resources" style="zoom:70%;" />



#### AWS Setup Complete

We have now completed the AWS setup:

- Created an ECR repository for storing Docker images.
- Configured an OpenID Connect identity provider.
- Created an IAM role with appropriate trust and permissions.

Next, we can use this setup in our GitHub workflow to push Docker images to our ECR repository.