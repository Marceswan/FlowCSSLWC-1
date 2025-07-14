# Setting Up Personal Access Token for Auto-PR Workflow

The auto-pr workflow needs a Personal Access Token (PAT) to create pull requests in the upstream repository because the default `GITHUB_TOKEN` only has permissions within your own repository.

## Steps to Create and Configure PAT:

### 1. Create a Personal Access Token

1. Go to GitHub Settings: https://github.com/settings/tokens
2. Click **"Generate new token"** → **"Generate new token (classic)"**
3. Give it a descriptive name: `FlowCSSLWC Auto-PR Token`
4. Set expiration (recommend 90 days and set a reminder to rotate)
5. Select scopes:
   - ✅ **repo** (Full control of private repositories)
     - This includes: repo:status, repo_deployment, public_repo, repo:invite
   - That's all you need!
6. Click **"Generate token"**
7. **IMPORTANT**: Copy the token immediately (you won't see it again!)

### 2. Add PAT to Your Fork's Repository Secrets

1. Go to your fork: https://github.com/Marceswan/FlowCSSLWC-1
2. Navigate to **Settings** → **Secrets and variables** → **Actions**
3. Click **"New repository secret"**
4. Name: `PAT_UPSTREAM`
5. Value: Paste your Personal Access Token
6. Click **"Add secret"**

### 3. Test the Workflow

You can manually trigger the workflow to test:
1. Go to **Actions** tab in your repository
2. Select **"Auto PR to Upstream"**
3. Click **"Run workflow"**
4. It should now successfully create PRs!

## Alternative: Using GitHub CLI Locally

If you prefer not to use a PAT, the workflow will output the exact command you can run locally:

```bash
gh pr create --base main --head Marceswan:BRANCH_NAME --repo meswan-ka/FlowCSSLWC --title "Your Title"
```

## Security Notes

- PATs are powerful - treat them like passwords
- Use the minimum required scope (just `repo`)
- Set expiration dates and rotate regularly
- Never commit PATs to code
- If compromised, revoke immediately at https://github.com/settings/tokens

## Troubleshooting

If PRs still fail to create:
1. Verify the PAT has `repo` scope
2. Check the PAT hasn't expired
3. Ensure you're using the secret name `PAT_UPSTREAM`
4. Check workflow logs for detailed error messages