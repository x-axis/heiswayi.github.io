---
layout: post
title: GitHub - Delete Commits History
description: Git commands to clear all of commits history in a repository.
keywords: git commands, delete commits history, clear repository commits, reset repository
---

Here are the git commands to clean up the messy, ugly commits history in a GitHub repository. This is like a repository reset.

### First Method

Deleting the `.git` folder may cause problems in our git repository. If we want to delete all of our commits history, but keep the code in its current state, try this:

01. Check out to a temporary branch.

```
git checkout --orphan TEMP_BRANCH
```

02. Add all the files.

```
git add -A
```

03. Commit the changes.

```
git commit -am "Initial commit"
```

04. Delete the old branch.

```
git branch -D master
```

05. Rename the temporary branch to **master**.

```
git branch -m master
```

06. Finally, force update to our repository.

```
git push -f origin master
```

This will not keep our old commits history around. **But if this doesn't work, try the next method below.**

### Second Method

01. Clone the project, e.g. `myproject` is my project repository.

```
git clone https://github/heiswayi/myproject.git
```

02. Since all of the commits history are in the `.git` folder, we have to remove it.

```
cd myproject
```

And delete the `.git` folder:

```
git rm -rf .git
```

03. Now, re-initialize the repository.

```
git init
git remote add origin https://github.com/heiswayi/myproject.git
git remote -v
```

04. Add all the files and commit the changes.

```
git add --all
git commit -am "Initial commit"
```

05. Force push update to the **master** branch of our project repository.

```
git push -f origin master
```

> NOTE: You might need to provide the credentials for your account.
