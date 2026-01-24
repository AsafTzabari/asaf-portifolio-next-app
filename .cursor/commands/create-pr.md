# Open a PR

- Check that I'm in a branch other than `main`. If not, bail and explain.
- Check the diff between my branch and the main branch of the repo
- If there's unstaged or staged work that hasn't been committed, commit all the relevant code first
  (Use `gh` in case it's installed)
- Check if a PR already exists for this branch by running: `gh pr view --json url`
- If a PR already exists:
  - Display the existing PR URL
  - Check if there are local commits that haven't been pushed: `git log origin/<branch-name>..HEAD`
  - If there are unpushed commits, push them: `git push`
  - Inform the user that the existing PR has been updated with the new changes
  - Always paste the link to the PR in your response so I can click it easily
  - Exit (do not create a new PR)
- If no PR exists:
  - Write up a quick PR description in the following format

<feature_area>: <Title> (80 characters or less)

<TLDR> (no more than 2 sentences)

<Description>
- 1~3 bullet points explaining what's changing

- Create the PR using: `gh pr create --base main --title "<title>" --body "<description>"`
- Always paste the link to the PR in your response so I can click it easily

- Prepend GIT_EDITOR=true to all git commands you run, so you can avoid getting blocked as you execute commands
