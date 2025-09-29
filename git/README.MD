## 🧩 Git & GitHub Scenario-Based Questions

---

### **Question 1: How do you recover from accidentally deleting a branch locally and remotely?**

**Answer:**
1. Recover locally (if reflog has it):
   ```bash
   git reflog
   git checkout -b deleted-branch <commit-hash>
   ```
2. Push to remote:
   ```bash
   git push origin deleted-branch
   ```

**Tips & Best Practices:**
- Always tag important commits before deleting branches.
- Use `git branch -D` cautiously.

**Example:**
```bash
git reflog | grep 'checkout: moving'
git checkout -b feature-login abc1234
git push origin feature-login
```

---

### **Question 2: How do you squash multiple commits before merging a feature branch?**

**Answer:**
1. Rebase interactively:
   ```bash
   git checkout feature-xyz
   git rebase -i HEAD~5
   ```
2. Mark commits as `squash` or `fixup`.

**Tips & Best Practices:**
- Squash before merging to keep history clean.
- Avoid squashing commits that are already pushed/shared.

**Example:**
```bash
git rebase -i HEAD~5
# squash commits
git push origin feature-xyz --force
```

---

### **Question 3: How do you resolve a merge conflict in a CI/CD pipeline triggered by GitHub Actions?**

**Answer:**
1. Detect conflict:
   ```yaml
   jobs:
     build:
       steps:
         - name: Checkout
           uses: actions/checkout@v3
         - name: Merge develop into main
           run: git merge origin/develop
   ```
2. Fix manually or automate with conflict resolution strategy.

**Tips & Best Practices:**
- Use `git merge -X theirs` or `ours` cautiously.
- Prefer manual resolution for critical branches.

**Example:**
```bash
git checkout main
git merge develop
# resolve conflicts
git add .
git commit
git push
```

---

### **Question 4: How do you enforce commit message standards across a team?**

**Answer:**
1. Use a commit-msg hook:
   ```bash
   echo '^[A-Z]+\-[0-9]+: .+' > .git/hooks/commit-msg
   ```
2. Or use `commitlint` with Husky.

**Tips & Best Practices:**
- Integrate with CI to reject bad messages.
- Document standards in CONTRIBUTING.md.

**Example:**
```yaml
# .github/workflows/commit-check.yml
name: Commit Message Check
on: [push]
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npx commitlint --from=HEAD~1 --to=HEAD
```

---

### **Question 5: How do you rollback a bad deployment caused by a GitHub Action?**

**Answer:**
1. Revert commit:
   ```bash
   git revert <bad-commit>
   git push origin main
   ```
2. Trigger rollback workflow:
   ```yaml
   on:
     push:
       branches: [main]
   jobs:
     rollback:
       steps:
         - run: ./rollback.sh
   ```

**Tips & Best Practices:**
- Use tags to mark stable releases.
- Automate rollback with versioned artifacts.

**Example:**
```bash
git revert abc1234
git push origin main
```

---

### **Question 6: How do you handle multiple developers pushing to the same branch?**

**Answer:**
1. Use protected branches.
2. Enforce pull requests with reviews.

**Tips & Best Practices:**
- Enable branch protection rules in GitHub.
- Use feature branches per developer.

**Example:**
```yaml
# GitHub branch protection
require_pull_request_reviews: true
enforce_admins: true
```

---

### **Question 7: How do you automate semantic versioning using Git tags in GitHub Actions?**

**Answer:**
1. Use `git describe` or `git tag`:
   ```bash
   git tag v1.2.3
   git push origin v1.2.3
   ```
2. GitHub Action:
   ```yaml
   steps:
     - name: Get latest tag
       run: echo "VERSION=$(git describe --tags)" >> $GITHUB_ENV
   ```

**Tips & Best Practices:**
- Use `semantic-release` for automation.
- Tag only on stable commits.

**Example:**
```bash
git tag -a v2.0.0 -m "Release v2.0.0"
git push origin v2.0.0
```

---

### **Question 8: How do you clone only a specific folder from a large GitHub repo?**

**Answer:**
Use sparse checkout:
```bash
git clone --filter=blob:none --no-checkout https://github.com/org/repo.git
cd repo
git sparse-checkout init --cone
git sparse-checkout set path/to/folder
```

**Tips & Best Practices:**
- Use sparse checkout for monorepos.
- Avoid cloning entire repo if unnecessary.

**Example:**
```bash
git sparse-checkout set services/api
```

---

### **Question 9: How do you handle secrets in GitHub Actions securely?**

**Answer:**
1. Store secrets in GitHub:
   - Settings → Secrets → Actions
2. Access in workflow:
   ```yaml
   env:
     API_KEY: ${{ secrets.API_KEY }}
   ```

**Tips & Best Practices:**
- Never hardcode secrets.
- Rotate secrets periodically.

**Example:**
```yaml
steps:
  - name: Use secret
    run: echo $API_KEY
    env:
      API_KEY: ${{ secrets.API_KEY }}
```

---

### **Question 10: How do you create a release workflow that builds and uploads artifacts?**

**Answer:**
1. Use `actions/upload-artifact`:
   ```yaml
   on:
     push:
       tags:
         - 'v*'
   jobs:
     release:
       steps:
         - uses: actions/checkout@v3
         - run: make build
         - uses: actions/upload-artifact@v3
           with:
             name: binary
             path: ./build/output
   ```

**Tips & Best Practices:**
- Trigger only on tags.
- Use `actions/create-release` for GitHub releases.

**Example:**
```yaml
- uses: actions/create-release@v1
  with:
    tag_name: ${{ github.ref }}
    release_name: Release ${{ github.ref }}
```

---

### **Question 11: How do you implement a Git branching strategy for a microservices architecture?**

**Answer:**
1. Use a trunk-based model with service-specific branches:
   - `main` → stable
   - `service-a/feature-x`
   - `service-b/hotfix-y`
2. Merge via pull requests with CI checks.

**Tips & Best Practices:**
- Keep branches short-lived.
- Use GitHub labels and CODEOWNERS for service ownership.

**Example:**
```bash
git checkout -b service-a/feature-login
git push origin service-a/feature-login
```

---

### **Question 12: How do you trigger a GitHub Action only when a specific file or folder changes?**

**Answer:**
Use `paths` filter:
```yaml
on:
  push:
    paths:
      - 'infra/**'
      - '.github/workflows/deploy.yml'
```

**Tips & Best Practices:**
- Use `paths-ignore` to skip unnecessary builds.
- Combine with `if:` conditions for fine control.

**Example:**
```yaml
jobs:
  deploy:
    if: github.event_name == 'push'
    steps:
      - run: ./deploy.sh
```

---

### **Question 13: How do you revert a merge commit that introduced bugs?**

**Answer:**
1. Use `git revert` with `-m`:
   ```bash
   git revert -m 1 <merge-commit-hash>
   ```
   `-m 1` means keep parent 1 (usually main).

**Tips & Best Practices:**
- Avoid `reset` on shared branches.
- Document revert reason in commit message.

**Example:**
```bash
git revert -m 1 abc1234
git push origin main
```

---

### **Question 14: How do you handle GitHub Actions failing due to missing dependencies?**

**Answer:**
1. Add setup steps:
   ```yaml
   - name: Install dependencies
     run: |
       sudo apt-get update
       sudo apt-get install -y libxml2
   ```

**Tips & Best Practices:**
- Use containerized runners for consistency.
- Cache dependencies when possible.

**Example:**
```yaml
- uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
```

---

### **Question 15: How do you prevent force pushes to protected branches?**

**Answer:**
1. Enable branch protection:
   - GitHub → Settings → Branches → Protect `main`
2. Disable force push:
   - Uncheck “Allow force pushes”.

**Tips & Best Practices:**
- Require PR reviews and status checks.
- Use CODEOWNERS for approval gates.

**Example:**
```yaml
required_status_checks:
  strict: true
  contexts:
    - build
    - test
```

---

### **Question 16: How do you cherry-pick a commit from one branch to another?**

**Answer:**
1. Checkout target branch:
   ```bash
   git checkout main
   ```
2. Cherry-pick:
   ```bash
   git cherry-pick <commit-hash>
   ```

**Tips & Best Practices:**
- Resolve conflicts carefully.
- Avoid cherry-picking merge commits.

**Example:**
```bash
git cherry-pick abc1234
git push origin main
```

---

### **Question 17: How do you automate PR labeling based on file changes?**

**Answer:**
Use `actions/labeler`:
```yaml
name: Label PR
on:
  pull_request:
    types: [opened, synchronize]
jobs:
  label:
    steps:
      - uses: actions/labeler@v4
```

**Tips & Best Practices:**
- Define rules in `.github/labeler.yml`.
- Use labels to trigger workflows.

**Example:**
```yaml
frontend:
  - 'web/**'
backend:
  - 'api/**'
```

---

### **Question 18: How do you debug a detached HEAD state in GitHub Actions?**

**Answer:**
1. Use `actions/checkout` with `ref`:
   ```yaml
   - uses: actions/checkout@v3
     with:
       ref: ${{ github.head_ref }}
   ```

**Tips & Best Practices:**
- Avoid using `HEAD` directly.
- Use `github.ref` or `github.head_ref` for clarity.

**Example:**
```yaml
run: git log --oneline -n 5
```

---

### **Question 19: How do you create a GitHub Action that runs only on PRs to `main`?**

**Answer:**
Use `pull_request` with `branches`:
```yaml
on:
  pull_request:
    branches:
      - main
```

**Tips & Best Practices:**
- Use `types:` to filter events like `opened`, `closed`.
- Combine with `if:` for granular control.

**Example:**
```yaml
jobs:
  test:
    if: github.event.pull_request.base.ref == 'main'
```

---

### **Question 20: How do you handle large binary files in Git without bloating the repo?**

**Answer:**
1. Use Git LFS:
   ```bash
   git lfs install
   git lfs track "*.zip"
   git add .gitattributes
   ```

**Tips & Best Practices:**
- Avoid committing binaries directly.
- Use release assets or artifact storage.

**Example:**
```bash
git add largefile.zip
git commit -m "Add binary via LFS"
git push origin main
```

---

### **Question 21: How do you trigger a GitHub Action only when a tag is pushed?**

**Answer:**
Use the `push` event with `tags` filter:
```yaml
on:
  push:
    tags:
      - 'v*'
```

**Tips & Best Practices:**
- Use semantic versioning (`v1.2.3`).
- Combine with release creation steps.

**Example:**
```yaml
jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Tag triggered: ${{ github.ref }}"
```

---

### **Question 22: How do you prevent accidental commits of sensitive files (e.g., `.env`, `config.json`)?**

**Answer:**
1. Add to `.gitignore`:
   ```bash
   echo ".env" >> .gitignore
   ```
2. Use pre-commit hooks:
   ```bash
   # .git/hooks/pre-commit
   grep -q '.env' staged_files && exit 1
   ```

**Tips & Best Practices:**
- Use `git-secrets` or `talisman` for scanning.
- Review `.gitignore` regularly.

**Example:**
```bash
git rm --cached .env
```

---

### **Question 23: How do you set up a GitHub Action to run tests in parallel across multiple OSes?**

**Answer:**
Use matrix strategy:
```yaml
jobs:
  test:
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v3
      - run: npm test
```

**Tips & Best Practices:**
- Use matrix for cross-platform validation.
- Cache dependencies per OS.

**Example:**
```yaml
- uses: actions/setup-node@v3
  with:
    node-version: '18'
```

---

### **Question 24: How do you handle GitHub Actions failing due to rate limits or API throttling?**

**Answer:**
1. Use retry logic:
   ```yaml
   - name: Retry API call
     run: |
       for i in {1..5}; do curl ... && break || sleep 10; done
   ```

2. Use `jobs.<job_id>.timeout-minutes` to limit job duration.

**Tips & Best Practices:**
- Use authenticated API calls.
- Monitor usage via GitHub Insights.

**Example:**
```yaml
timeout-minutes: 15
```

---

### **Question 25: How do you create a Git alias for frequent commands?**

**Answer:**
1. Add alias:
   ```bash
   git config --global alias.co checkout
   git config --global alias.st status
   ```

**Tips & Best Practices:**
- Store aliases in `.gitconfig`.
- Use descriptive names for scripts.

**Example:**
```bash
git co feature-branch
git st
```

---

### **Question 26: How do you automate PR creation from CLI for a new feature branch?**

**Answer:**
Use GitHub CLI:
```bash
gh pr create --base main --head feature-xyz --title "Add login" --body "Implements login flow"
```

**Tips & Best Practices:**
- Use `gh` for scripting workflows.
- Combine with CI status checks.

**Example:**
```bash
gh pr view --web
```

---

### **Question 27: How do you enforce code review approvals before merging to `main`?**

**Answer:**
1. Enable branch protection:
   - Require pull request reviews.
   - Require status checks.

**Tips & Best Practices:**
- Use CODEOWNERS for auto-assigning reviewers.
- Block merge until approvals.

**Example:**
```yaml
required_approving_review_count: 2
```

---

### **Question 28: How do you debug a GitHub Action that works locally but fails in CI?**

**Answer:**
1. Add verbose logging:
   ```yaml
   - run: set -x
   ```
2. Use `debug` secrets:
   ```yaml
   secrets: GITHUB_ACTIONS_RUNNER_DEBUG=true
   ```

**Tips & Best Practices:**
- Use `act` to run workflows locally.
- Compare environment variables.

**Example:**
```bash
echo $GITHUB_WORKSPACE
```

---

### **Question 29: How do you automate dependency updates using GitHub Actions?**

**Answer:**
Use `dependabot.yml`:
```yaml
version: 2
updates:
  - package-ecosystem: npm
    directory: "/"
    schedule:
      interval: weekly
```

**Tips & Best Practices:**
- Enable auto-merge with labels.
- Review changelogs before merging.

**Example:**
```yaml
labels:
  - dependencies
  - auto-merge
```

---

### **Question 30: How do you generate a changelog automatically from Git commits?**

**Answer:**
Use `github-changelog-generator` or `conventional-changelog`:
```bash
npx conventional-changelog -p angular -i CHANGELOG.md -s
```

**Tips & Best Practices:**
- Use semantic commit messages.
- Automate in release workflow.

**Example:**
```yaml
- run: npx conventional-changelog -p angular -i CHANGELOG.md -s
```
---

### **Question 31: How do you clean up stale branches both locally and remotely?**

**Answer:**
1. Delete local branches:
   ```bash
   git branch --merged main | grep -v '\*' | xargs git branch -d
   ```
2. Delete remote branches:
   ```bash
   git fetch -p
   git push origin --delete <branch-name>
   ```

**Tips & Best Practices:**
- Use `git branch -r` to list remote branches.
- Automate cleanup with scripts or GitHub Actions.

**Example:**
```bash
git branch -r | grep 'origin/' | sed 's/origin\///' | xargs -n 1 git push origin --delete
```

---

### **Question 32: How do you manage Git submodules in a multi-repo setup?**

**Answer:**
1. Add submodule:
   ```bash
   git submodule add https://github.com/org/lib.git libs/lib
   ```
2. Initialize and update:
   ```bash
   git submodule update --init --recursive
   ```

**Tips & Best Practices:**
- Pin submodules to specific commits.
- Avoid frequent updates unless necessary.

**Example:**
```bash
git submodule foreach git pull origin main
```

---

### **Question 33: How do you migrate a repo from Bitbucket to GitHub with full history?**

**Answer:**
1. Clone from Bitbucket:
   ```bash
   git clone --mirror https://bitbucket.org/org/repo.git
   ```
2. Push to GitHub:
   ```bash
   cd repo.git
   git push --mirror https://github.com/org/repo.git
   ```

**Tips & Best Practices:**
- Use `--mirror` to preserve branches and tags.
- Audit `.git/config` before pushing.

**Example:**
```bash
git remote set-url origin https://github.com/org/repo.git
```

---

### **Question 34: How do you enforce signed commits in GitHub?**

**Answer:**
1. Enable in GitHub repo settings:
   - Require signed commits.
2. Sign locally:
   ```bash
   git commit -S -m "Signed commit"
   ```

**Tips & Best Practices:**
- Use GPG or SSH signing.
- Document setup in onboarding guides.

**Example:**
```bash
git config --global user.signingkey <GPG_KEY_ID>
```

---

### **Question 35: How do you split a monorepo into multiple service-specific repos?**

**Answer:**
1. Use `git filter-repo`:
   ```bash
   git filter-repo --path services/api --force
   ```
2. Push to new remote:
   ```bash
   git remote add origin https://github.com/org/api.git
   git push -u origin main
   ```

**Tips & Best Practices:**
- Preserve commit history.
- Tag before splitting.

**Example:**
```bash
git tag pre-split-api
```

---

### **Question 36: How do you create a GitHub Action that builds and deploys only on approved PRs?**

**Answer:**
Use `pull_request_review` trigger:
```yaml
on:
  pull_request_review:
    types: [submitted]
jobs:
  deploy:
    if: github.event.review.state == 'approved'
    steps:
      - run: ./deploy.sh
```

**Tips & Best Practices:**
- Combine with branch protection.
- Use `environment` for deployment gates.

**Example:**
```yaml
environment:
  name: production
  url: https://prod.example.com
```

---

### **Question 37: How do you handle Git history rewriting for sensitive data removal?**

**Answer:**
1. Use `git filter-repo`:
   ```bash
   git filter-repo --replace-text sensitive.txt
   ```
2. Force push:
   ```bash
   git push --force --all
   ```

**Tips & Best Practices:**
- Inform collaborators before rewriting history.
- Rotate credentials immediately.

**Example:**
```bash
echo 'password123==>REMOVED' > sensitive.txt
```

---

### **Question 38: How do you automate release notes generation in GitHub Actions?**

**Answer:**
Use `release-drafter`:
```yaml
name: Draft Release
on:
  push:
    branches: [main]
jobs:
  draft:
    steps:
      - uses: release-drafter/release-drafter@v5
```

**Tips & Best Practices:**
- Use labels to categorize changes.
- Customize `.github/release-drafter.yml`.

**Example:**
```yaml
categories:
  - title: 🚀 Features
    labels:
      - feature
```

---

### **Question 39: How do you set up a GitHub Action to deploy only when tests pass?**

**Answer:**
Use `needs:` keyword:
```yaml
jobs:
  test:
    steps:
      - run: npm test

  deploy:
    needs: test
    steps:
      - run: ./deploy.sh
```

**Tips & Best Practices:**
- Use `continue-on-error: false` to enforce failure.
- Separate build/test/deploy stages.

**Example:**
```yaml
jobs:
  test:
    continue-on-error: false
```

---

### **Question 40: How do you handle GitHub Actions secrets rotation securely?**

**Answer:**
1. Rotate secrets manually in GitHub UI.
2. Use external secret managers:
   - HashiCorp Vault
   - AWS Secrets Manager

**Tips & Best Practices:**
- Audit secret usage in workflows.
- Avoid echoing secrets in logs.

**Example:**
```yaml
env:
  DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
```

---
