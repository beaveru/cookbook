# Git Remotes

In Git, a remote is a reference to a repository that exists on another server or system.
Remotes allow you to access and interact with a copy of your repository that is stored elsewhere, making it possible to collaborate with others, share your work, and maintain multiple copies of your repository for backup and disaster recovery purposes.
When you add a remote to your local repository, Git creates a reference to the remote repository, enabling you to push changes from your local repository to the remote one, pull changes from the remote to your local one, or fetch changes from the remote without updating your local copy.
This enables distributed development and helps maintain a centralized version of your project's history, making it easier to track changes, manage conflicts, and ensure that everyone has access to the most up-to-date code.

## Commands

- `git remote`
  - show remotes name for selected repository
- `git remote -v`
  - show names and connection string for a remote
- `git remote add <remote-name> <remote-url>`
- `git remote rm <remote-name>`
