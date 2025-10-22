---
id: task-306
title: Improve backlog init flow to ask for confirmation when git repo not found
status: To Do
assignee: []
created_date: '2025-10-22 05:09'
labels: []
dependencies: []
priority: medium
---

## Description

<!-- SECTION:DESCRIPTION:BEGIN -->
When running 'backlog init' in a directory that is not a git repository, the CLI should prompt the user to confirm they want to initialize backlog in the current folder. This improves user experience by preventing accidental initialization in unintended directories.
<!-- SECTION:DESCRIPTION:END -->

## Acceptance Criteria
<!-- AC:BEGIN -->
- [ ] #1 Add confirmation prompt when git repo is not detected during init
- [ ] #2 Prompt should clearly state that no git repo was found and ask for user confirmation
- [ ] #3 User can skip confirmation with a --force or --yes flag
- [ ] #4 Tests should verify the confirmation behavior
<!-- AC:END -->
