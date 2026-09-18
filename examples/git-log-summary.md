#!/usr/bin/env -S agy-run --model gemini-3.8-flash-high --dangerously-skip-permissions

# GOAL

The goal is to **create release notes** by analyzing the last 2 month worth of commits in the current Git repository.

Follow these steps, one after the other:
* Read commits and diffs of the current repository to understand and explain in details the changes.
* Reorder commits so that commits for the same feature are grouped together. Each commit or groups of commits should be explained thoroughly as a single entity, and as if you were writing a blog post explaining the changes.

# INSTRUCTIONS

* Execute `git log -p --no-merges` using `run_command` to also get the diff for each commit, and to ignore merge commits.
* Don't run any other tools.
* Don't create Python programs to do the job.

# OUTPUT

* Organize the release notes by category (Features, Fixes, etc.)
* Use bold for the title of each commit.
* Mention the dates of each change in the format `YYYY-MM-DD`, in parentheses after the title.

# EXAMPLE

Here's an example of the output:

```
# RELEASE NOTES
## Features
### Dark Mode
* **Add support for dark mode** (2024-01-01) — Now users can switch to dark mode in the settings...
* **Fix issue with the dark mode** (2024-01-02) — The dark mode was not working...
## Fixes
### Accessibility
* **Fix issue with the accessibility** (2024-01-02) — The reader mode has been improved...
## Refactors
* **Refactor the code** (2024-01-03) — The code was not following the best practices...
## Documentation
* **Update the documentation** (2024-01-04) — The documentation was not up to date...
## Security
* **Fix security issue** (2024-01-05) — The code was not following the best practices...
## Performance
* **Improve performance** (2024-01-06) — The code was not following the best practices...
## Other
* **Enabled Dependabot** (2024-01-07) — Now we have Dependabot to keep our dependencies up to date...
```
