# A Collection of useful git hooks.

### How to install:
Copy the desired hook script into your repo at `<your_repo>/.git/hooks`.

git will then execute the script accordingly with the action specified by the name of the script. For more info on git hooks, see: https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks

This repo currently includes a `pre-commit` hook to do static code analysis on Go source files.