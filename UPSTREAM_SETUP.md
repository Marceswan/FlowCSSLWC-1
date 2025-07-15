# Upstream Repository Setup Instructions

## For the Upstream Repository Owner (meswan-ka/FlowCSSLWC)

To enable automated PR acceptance and better collaboration, configure the following settings in your repository:

### 1. Enable Auto-merge

1. Go to **Settings** → **General**
2. Scroll to **Pull Requests**
3. Check ✅ **Allow auto-merge**
4. Check ✅ **Automatically delete head branches**

### 2. Set up Branch Protection Rules

1. Go to **Settings** → **Branches**
2. Click **Add rule**
3. Branch name pattern: `main`
4. Configure protection settings:
   - ✅ **Require a pull request before merging**
   - ✅ **Require approvals** (set to 0 for auto-merge, or 1+ for manual review)
   - ✅ **Dismiss stale pull request approvals when new commits are pushed**
   - ✅ **Require status checks to pass before merging** (if you have CI/CD)
   - ✅ **Require branches to be up to date before merging**
   - ✅ **Include administrators** (optional)

### 3. Create Auto-merge Workflow (Optional)

Create `.github/workflows/auto-merge.yml` in the upstream repo:

```yaml
name: Auto-merge PRs

on:
  pull_request:
    types: [opened, synchronize, reopened]

permissions:
  contents: write
  pull-requests: write

jobs:
  auto-merge:
    runs-on: ubuntu-latest
    # Only run for trusted contributors
    if: github.actor == 'Marceswan' || github.actor == 'dependabot[bot]'
    steps:
      - name: Enable auto-merge
        uses: peter-evans/enable-pull-request-automerge@v3
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          pull-request-number: ${{ github.event.pull_request.number }}
          merge-method: squash  # or 'merge' or 'rebase'
```

**Important**: The `permissions` section is required for the workflow to have write access.

#### Alternative: Manual Auto-merge

If the workflow approach doesn't work, you can manually enable auto-merge for specific PRs:

1. Open the PR
2. Click the dropdown arrow next to "Merge pull request"
3. Select "Enable auto-merge"
4. Choose your merge method (squash recommended)
5. The PR will merge automatically when all requirements are met

### 4. Add CODEOWNERS File (Optional)

Create `.github/CODEOWNERS`:

```
# Default owners for everything
* @meswan-ka

# Specific paths can have different owners
/force-app/main/default/lwc/ @meswan-ka @Marceswan
```

### 5. Configure Merge Settings

1. Go to **Settings** → **General**
2. Under **Pull Requests**, configure:
   - ✅ **Allow squash merging** (recommended)
   - ✅ **Default to PR title for squash merge commits**
   - ❌ **Allow merge commits** (optional)
   - ❌ **Allow rebase merging** (optional)

### 6. Set up GitHub Apps (Optional but Recommended)

Consider installing these GitHub Apps for better automation:

1. **Mergify**: Advanced merge rules and automation
2. **Renovate**: Automated dependency updates
3. **Kodiak**: Auto-merge with advanced rules

### 7. Create Welcome Bot (Optional)

Create `.github/workflows/welcome.yml`:

```yaml
name: Welcome
on:
  pull_request_target:
    types: [opened]

jobs:
  welcome:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/first-interaction@v1
        with:
          repo-token: ${{ secrets.GITHUB_TOKEN }}
          pr-message: |
            Thanks for your contribution! 🎉
            
            This PR will be automatically reviewed and merged if all checks pass.
```

## For Contributors (Fork Owners)

### Required Setup:

1. **Keep Fork Updated**: The `sync-upstream.yml` workflow will automatically sync daily
2. **Auto PR Creation**: The `auto-pr-upstream.yml` workflow creates PRs on push to main
3. **Manual PR Creation**: You can always create PRs manually using:
   ```bash
   gh pr create --base main --head YourUsername:main --repo meswan-ka/FlowCSSLWC
   ```

### Best Practices:

1. Always sync your fork before making changes:
   ```bash
   git fetch upstream
   git checkout main
   git merge upstream/main
   git push origin main
   ```

2. Create feature branches for complex changes:
   ```bash
   git checkout -b feature/new-feature
   # Make changes
   git push origin feature/new-feature
   # Create PR from feature branch
   ```

3. Keep commits clean and descriptive

## Collaboration Agreement

For smooth automation, establish these guidelines:

1. **Trusted Contributors**: List of users whose PRs can be auto-merged
2. **Review Requirements**: Which changes require manual review
3. **Merge Strategy**: Squash, merge, or rebase
4. **CI/CD Requirements**: What tests must pass

---

**Note**: Share this document with the upstream repository owner to enable full automation capabilities.