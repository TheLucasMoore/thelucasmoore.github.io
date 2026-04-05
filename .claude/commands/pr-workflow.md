This repo has branch protection on `main` — all changes must go through a PR. Follow these steps for every change:

1. Create a feature branch: `git checkout -b <descriptive-branch-name>`
2. Make the changes and commit: `git add <files> && git commit -m "<message>"`
3. Push and create a PR: `git push origin <branch> && gh pr create --title "<title>" --body "<description>"`
4. Merge and delete the branch: `gh pr merge --merge --delete-branch`
5. Pull main: `git checkout main && git pull`
