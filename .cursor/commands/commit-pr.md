# Commit and Create PR

## Overview
Automatically commit all changes with an AI-suggested commit message based on the actual changes made, push to remote, and create a pull request to the main branch using GitHub CLI.

## Steps

### 1. Check Git Status and Analyze Changes

First, check the current git status to understand what has changed:
- Run `git status` to see modified, added, or deleted files
- Check if there are any changes to commit (both staged and unstaged)
- If there are no changes, inform the user: "No changes detected. Nothing to commit." and exit

Verify the current branch:
- Run `git branch --show-current` to get the current branch name
- If in detached HEAD state or not on a branch, inform the user they need to be on a branch and exit

### 2. Analyze Git Diffs

Analyze the actual changes to understand what was modified:
- Run `git diff` to see unstaged changes
- Run `git diff --staged` to see staged changes
- Analyze the diff output to identify:
  - **File types changed**: TypeScript/TSX files, CSS, config files, etc.
  - **Type of changes**: 
    - New features (new components, new functionality, new commands, new tools)
    - Bug fixes (error handling, corrections)
    - Refactoring (code restructuring, improvements)
    - Documentation (README, comments, docs)
    - Styling (CSS changes, Tailwind classes)
    - Configuration (package.json, config files)
    - Tests (test files added/modified)
  - **Primary purpose**: Determine if the main change is adding new functionality (feat) or just maintenance (chore)
  - **Feature indicators**: New commands, new tools, new capabilities, new components, new functionality
  - **Maintenance indicators**: Dependency updates without new features, config tweaks, formatting-only changes
  - **Scope**: Which part of the application was affected (e.g., homepage, navbar, components, commands)

### 3. Suggest Commit Message

Based on the analysis, suggest an appropriate commit message following the **Conventional Commits** format:

**Format**: `type(scope): description`

**Commit Types**:
- `feat`: New feature or functionality (including new commands, tools, or capabilities)
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, whitespace, etc.)
- `refactor`: Code refactoring without changing functionality
- `test`: Adding or updating tests
- `chore`: Maintenance tasks, dependencies updates (when not part of a new feature), build config

**Important**: If adding a new command, tool, or feature (even if it includes config changes), use `feat`. Only use `chore` for pure maintenance or dependency updates that don't add new functionality. The primary purpose determines the type, not the file types changed.

**Scope Examples**: `homepage`, `navbar`, `components`, `layout`, `config`, `commands`, etc.

**Description Guidelines**:
- Use imperative mood ("add" not "added", "fix" not "fixed")
- Be concise but descriptive
- **Focus on the purpose and outcome, not just the implementation details**
- **Describe what the change enables or achieves, not just what was added**
- **Prefer describing the benefit/value over technical details**
- First letter lowercase
- No period at the end

**Examples of good commit messages**:
- `feat(homepage): add hero section with call-to-action button`
- `feat(commands): add commit-pr cursor command for automated commits and PRs`
- `fix(navbar): resolve mobile menu closing issue`
- `refactor(components): extract reusable button component`
- `docs(readme): update installation instructions`
- `style(layout): improve spacing and typography`
- `chore(deps): update Next.js to latest version`

**Bad examples (too technical, less readable)**:
- ❌ `chore(commands): add commit-pr cursor command with interactive prompts` - Too technical, focuses on implementation
- ❌ `feat(api): add endpoint with authentication middleware` - Focus on implementation details
- ❌ `feat(components): add component using useState and useEffect hooks` - Technical details instead of purpose

**Good examples (clear purpose and outcome)**:
- ✅ `feat(commands): add commit-pr cursor command for automated commits and PRs` - Clear outcome
- ✅ `feat(api): add user authentication endpoint` - Clear purpose
- ✅ `feat(components): add user profile card with edit functionality` - Describes what it enables

After analyzing the changes, present the suggested commit message clearly to the user.

### 4. Interactive Prompt for Commit Message

Present the suggested commit message and ask for user confirmation:

**Prompt format**:
```
Suggested commit message:
[type(scope): description]

Use this commit message? (Y/n) or type a custom message:
```

**Handle user input**:
- If user types `Y`, `y`, `yes`, or presses Enter: Use the suggested message
- If user types `n` or `no`: Ask them to provide a new commit message
- If user types any other text: Use that text as the commit message
- Wait for user response before proceeding

### 5. Stage All Changes

Once the commit message is confirmed, **preview and verify files before staging**:

**Step 1: Preview files that will be staged**
- Run `git status --porcelain` to see a clean list of all modified, added, and deleted files
- Run `git status` to see detailed information about staged vs unstaged changes
- Review the list carefully to identify what will be committed

**Step 2: Check for sensitive files**
- **WARNING**: Before staging, verify that no sensitive files are being added:
  - Environment files: `.env`, `.env.local`, `.env.*`
  - API keys and credentials: files containing `key`, `secret`, `token`, `password`, `credential` in filename
  - Build artifacts: `dist/`, `build/`, `.next/`, `out/`, `node_modules/`
  - Temporary files: `*.log`, `*.tmp`, `.DS_Store`
  - Configuration with secrets: `config.json`, `secrets.json`, `credentials.json`
  - Certificate files: `*.pem`, `*.key`, `*.cert`
- If any sensitive files are detected, **STOP** and inform the user:
  - List the sensitive files found
  - Warn the user that these files should NOT be committed
  - Suggest adding them to `.gitignore` if needed
  - Ask the user to confirm if they want to proceed or exclude these files
  - Do NOT proceed with staging until user explicitly confirms

**Step 3: Confirm staging**
- Present the list of files that will be staged to the user
- Ask for confirmation: "Stage these files? (Y/n)"
- If user confirms (Y/yes/Enter):
  - Run `git add .` to stage all changes (both staged and unstaged)
  - Verify files are staged by running `git status` again
- If user declines (n/no):
  - Inform the user that staging was cancelled
  - Allow them to manually stage specific files if needed
  - Exit the command

**Important**: Never stage sensitive files without explicit user confirmation. Always show what will be staged before proceeding.

### 6. Commit Changes

Commit with the final message:
- Run `git commit -m "<final-commit-message>"`
- Verify the commit was successful
- If commit fails, inform the user and exit

### 7. Push to Remote

Push the current branch to the remote repository:
- Get the current branch name: `git branch --show-current`
- Run `git push -u origin <branch-name>`
- **If push fails:**
  - Present the error message to the user
  - Explain that PR creation (step 8) requires the branch to exist on remote
  - Ask the user if they want to retry: "Push failed. Retry? (Y/n)"
  - If user confirms (Y/yes/Enter):
    - Retry the push: `git push -u origin <branch-name>`
    - If retry succeeds, continue to step 8
    - If retry fails again, repeat the retry prompt (allow multiple retries)
  - If user declines (n/no):
    - Inform the user that PR creation cannot proceed without a successful push
    - Suggest they push manually: `git push -u origin <branch-name>`
    - Exit the command (do NOT proceed to step 8)
- **If push succeeds:**
  - Verify the branch is on remote
  - Continue to step 8 (Create Pull Request)

**Important**: PR creation (step 8) requires the branch to exist on remote. Do NOT proceed to step 8 if push failed. The branch must be successfully pushed before creating a PR.

### 8. Create Pull Request

**Prerequisite**: This step requires that step 7 (Push to Remote) completed successfully. The branch must exist on remote before creating a PR.

Create a pull request using GitHub CLI:
- Verify GitHub CLI is available: Run `gh --version` (if not available, inform user and exit)
- Check GitHub CLI authentication: Run `gh auth status` (if not authenticated, inform user and exit)
- Create PR with:
  ```
  gh pr create --base main --title "<commit-message>" --body "Automated PR from commit: <commit-message>"
  ```
- If PR creation fails:
  - Check if PR already exists for this branch
  - Inform the user about the error
  - Provide the PR URL if it was created successfully
- If successful, display the PR URL to the user

## Important Notes

- **Always check for changes first**: Never commit if there are no changes
- **Branch validation**: Ensure user is on a valid branch before proceeding
- **Error handling**: Handle each step gracefully and inform the user of any issues
- **User confirmation**: Always wait for user confirmation on the commit message
- **Conventional commits**: Follow the conventional commit format for consistency
- **GitHub CLI**: Verify GitHub CLI is installed and authenticated before creating PR
- **Base branch**: Always create PR targeting `main` branch

## Example Workflow

```
1. User types: /commit-pr

2. AI runs: git status
   Output: "Modified: app/page.tsx, app/components/Hero.tsx"

3. AI runs: git diff
   Analyzes: "Added new Hero component with title and CTA button"

4. AI suggests: "feat(homepage): add hero section component with call-to-action"

5. AI prompts: "Use this commit message? (Y/n) or type a custom message:"
   User: Y

6. AI runs: git add .
   AI runs: git commit -m "feat(homepage): add hero section component with call-to-action"

7. AI runs: git push -u origin add/commit-pr-command

8. AI runs: gh pr create --base main --title "feat(homepage): add hero section component with call-to-action" --body "Automated PR from commit: feat(homepage): add hero section component with call-to-action"

9. AI displays: "✅ PR created successfully: https://github.com/user/repo/pull/123"
```

