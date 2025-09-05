---
name: /job-summarize.md
allowed-tools: Bash(git status:*), Bash(git log:*), Bash(git diff:*), Bash(git branch:*)
argument-hint: [message]
description: Create a summary for today, the time range user specified or the commits, the summary are made base on the git commit message
---

---

## Context

- Current git status: !`git status`
- Current git diff (staged and unstaged changes): !`git diff HEAD`
- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -10`

---

---

## Task

1. read commit messages if the $Arguments is a commit hash you should read the commit or commits, if the $Arguments is a time range you should find commits within the time range $Arguments, if $Arguments is empty you should fetch the commit within today
2. base on the commit messages create a summary for the jobs that user has done\
3. break the summary into below format

```
- Component or Endpoint or Feature name
  1. what job has been done
  2. if multiple jobs have been done, you should also list it

- Another Component or Endpoint or Feature
  1. job done for another component
  2. anotehr job done for another component

```

---
