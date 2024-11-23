### Setting Up the Identity Provider

- To securely provide access from GitHub to AWS without providing access tokens in secrets, we'll use OpenID Connect (OIDC) as described in the [GitHub documentation](https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services).

#### Setting Up an Identity Provider (AWS Console)

To setup an identity provider in your AWS account:

1. Navigate to the **AWS Console**.
2. From the console, go to the **Identity and Access Management (IAM)** dashboard.
3. Click on **Identity providers** in the left-side menu.
4. Click on **Add Provider**.
5. Select **OpenID Connect** as the provider type.
6. Use the following values as described [here](https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services#adding-the-identity-provider-to-aws).
   - `Provider URL`: `https://token.actions.githubusercontent.com`
   - `Audience`: `sts.amazonaws.com`





<img src="/docs/images/iAM_dashboard.png" alt="iam_dashboard" style="zoom:70%;" />





<img src="/docs/images/identity_providers.png" alt="identity_providers" style="zoom:70%;" />





<img src="/docs/images/add_identity_provider.png" alt="add_identity_provider" style="zoom:70%;" />