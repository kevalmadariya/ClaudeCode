---
name: end-feature
description: Stage all changes, commit with a message, and push the current feature branch (must not be main).
argument-hint: [commit_message]
disable-model-invocation: true
allowed-tools: Bash(git add, git commit, git checkout, git status, git rev-parse, git show-ref, git diff, git log, git push, git pull, git fetch, git clone, git init, git merge)
---

# End a feature branch and push changes

```bash
!
COMMIT_MESSAGE="$0"

# 1. Require a commit message
if [ -z "$COMMIT_MESSAGE" ]; then
  echo "ERROR: Please provide a commit message. Usage: /end-feature <commit_message>"
  exit 1
fi

# 2. Verify we are not on main or master
BRANCH=$(git rev-parse --abbrev-ref HEAD)

if [ "$BRANCH" = "main" ] || [ "$BRANCH" = "master" ]; then
  echo "ERROR: You are on '$BRANCH'. Switch to a feature branch first."
  exit 1
fi

echo "OK: on branch '$BRANCH'"

# 3. Stage all changes
echo "Staging all changes..."
git add .

# 4. Commit changes
echo "Creating commit..."
git commit -m "$COMMIT_MESSAGE"

# 5. Push branch
echo "Pushing branch to origin..."
git push -u origin HEAD

# 6. Final success message
echo "Pushed branch '$BRANCH' to origin. Feature ready for review."