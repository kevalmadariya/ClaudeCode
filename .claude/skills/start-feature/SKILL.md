---
name: start-feature
description: Start a new feature branch from a clean main branch and rename the session.
argument-hint: [feature_name]
disable-model-invocation: true
allowed-tools: Bash(git)
---

# Start a feature branch and rename session

```bash
!
FEATURE="$0"

# 1. Require a feature name
if [ -z "$FEATURE" ]; then
  echo "ERROR: Please provide a feature name. Usage: /start-feature <feature_name>"
  exit 1
fi

# 2. Verify we are on main (or master)
CURRENT_BRANCH=$(git rev-parse --abbrev-ref HEAD)
if [ "$CURRENT_BRANCH" != "main" ] && [ "$CURRENT_BRANCH" != "master" ]; then
  echo "ERROR: You must be on 'main' (or master) to start a feature. Current branch: $CURRENT_BRANCH"
  exit 1
fi

# 3. Verify clean working tree
if [ -n "$(git status --porcelain)" ]; then
  echo "ERROR: Working tree is not clean. Please commit or stash changes before starting a new feature."
  exit 1
fi

echo "OK: On $CURRENT_BRANCH with a clean tree."

# 4. Create or switch to the feature branch
if git show-ref --verify --quiet "refs/heads/$FEATURE"; then
  echo "Branch '$FEATURE' already exists, switching to it..."
  git checkout "$FEATURE"
else
  echo "Creating and switching to new branch '$FEATURE'..."
  git checkout -b "$FEATURE"
fi

# 5. Rename the Claude Code session
PROJECT=$(basename "$(git rev-parse --show-toplevel)")
PROJECT_LOWER=$(echo "$PROJECT" | tr '[:upper:]' '[:lower:]')

# Output the rename command exactly so Claude Code executes it
echo "/rename $PROJECT_LOWER/$FEATURE"