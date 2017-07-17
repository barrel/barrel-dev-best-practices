### Barrel Development Best Practices

# Git Best Practices
- [Repo Requirements](#repo-requirements)
- [What to Commit](#what-to-commit)
- [Commit Message Conventions](#commit-message-conventions)
- [Commit Frequency](#commit-frequency)
- [Working with Others](#working-with-others) and Workflows
- [Working on an Existing App](#working-on-an-existing-app)
- [Github Issues](#issues)
- [Git Quick Reference](#quick-reference)

**Note:** Barrel mostly uses *GitLab* for the truth repository, but occasionally we will use *GitHub* and/or *BitBucket* repositories as well. For new developers, please request access immediately.

## Repo Requirements
1. The repository name should follow the "slugified" convention such that the name is **all lowercase**, **uses hyphens** to separate words instead of spaces or casing, and **omits articles** (a/an/the).
2. The repo should contain a description, which should at minimum describe the type of project, WordPress, static HTML, Shopify. Ideally this also contains a short description of the site itself.
3. All repositories require a `README.md` at the root of the project as well as in any other places such as theme or plugin directories where specific documentation for development is required. A `CONTRIBUTING.md` file should be provided to assist contributing developers on the project with any project-specific contribution guidelines.
4. For sites that use Pantheon multidev environments, there is a [11 character](https://pantheon.io/docs/multidev/#create-a-new-multidev-environment) branch name limitation as well as some protected branches names. 
5. When possible, enable both the JIRA and HipChat integrations (internal development only).

## What to Commit
**The Issue:** The biggest cause for conflict is compressed, compiled, transpiled, or compressed code. Reviewers often aren't as intimate with code, so recognizing and resolving conflicts becomes a pain-point.

**The Solution:** First _ensure that all compressed and processed code is committed on a dedicated commit_. Do not push a commit that contains both source code and processed code; instead, try to contain all processed code in a single commit just before pushing code to review (environment) or merge request (branch). This allows reviewers to rebase (if necessary, when possible) without relying on the developer.

## Commit Message Conventions
1. Commit header (first line) should succinctly describe changes in high-level changes. Think: Fix, Add, Remove, Change.
2. Commit body (subsequent lines) should be imperative present tense, and break out into more low-level details.

**Verbose (bad):** avoid overly verbose message headers without references to ticketing or specs.

> **Site down message on error handler. This commit requires a migration for the time_remaining column to add
> to the essays table**

**Ambiguous (bad):** avoid ambiguous messaging that offers little insight to the actual changes or features.

> **Auto save & save on timeout**
> * Update code for timeout

**Succint (good):** be sure to include references to tickets or specifications while including more granular details as bullet points.

> **Fix BR-137 and BR-57**
>
> * Add auto-save functionality when connection to client times out
> * Add method to auto-save client details
> * Commit requires a migration for the time_remaining column to add to the essays table

**Detailed (good):** without references, do not forget to add more detailed bullet points.

> **Add auto-save functionality and site down error handler**
>
> * Add auto-save functionality when connection to client times out
> * Add method to auto-save client details
> * Commit requires a migration for the time_remaining column to add to the essays table

---
**Links for reference:**
- See github [article](https://help.github.com/articles/closing-issues-via-commit-messages/#keywords-for-closing-issues) on closing issues in commit messages.
- See jira [article](https://confluence.atlassian.com/display/Cloud/Processing+JIRA+issues+with+commit+messages#ProcessingJIRAissueswithcommitmessages-Thecommands) on processing jira tickets with commit messages. Note at the very least, you can easily attach commits to a jira ticket by including the ticket reference in your commit message.
- See gitlab [article](https://about.gitlab.com/2016/03/08/gitlab-tutorial-its-all-connected/) on referencing commits, merge requests, or issues. 

## Commit Frequency

- Avoid working all day and then adding an "EOD Commit". Instead, commit every time you have added a meaningful chunk of code. This way, if you need to revert back to an older commit, there are frequent, logical timestamps for you to revert to.
- Give commits a meaningful name (ex. git commit -m "Add BX Slider to homepage").
- **Try to make more commits in your branch.**
The more you commit, the easier it is to rebase or merge or cherry-pick individual commits. You might consider separating your models, controllers, migrations in separate smaller commits.
- **Make sure your branch is limited to one feature.** 
It's ok if the branch has only one commit for even the smallest feature, but it makes it easier if we need to have someone else pickup the work for that specific issue. In this case, ensure the features being committed to your branch match the branch's stated purpose.
- **Name your feature branch.**
Another benefit of naming your branch according to feature is that everyone will know what your branch contains. If your feature covers a range of files and other features, make sure your branch reflects that by indicating "various-edits" or "bundle-fixes" or, if it spans several fixes for a general issue, use something like "fix-timing-issues" instead of "bobs-tickets" where someone must review each commit and the files changed to get a better idea of what's added, removed, changed, or fixed. If you are bundling edits, this is best achieved by creating a branch from a common ancestor and merging all the relevant feature branches you wish to commit to the bundled branch.

## Working with Others

Assume your code will be inherited by another person. You should **never** work directly on the "master" branch. It's best practice to make "feature branches", which can be merged into "master" as you complete features.  

### Workflows

Most new projects should follow either a **centralized** or **feature branch** workflow while most ongoing projects will follow **gitflow**. See [this](https://www.atlassian.com/git/tutorials/comparing-workflows) Atlassian article on comparing various workflows. Note that most workflows will need a lead or primary developer to manage the incoming code to ensure successful merges and integration.

#### Process

In addition to pure git workflows, developers that work with Barrel must follow process guidelines outlined as follows:

1. As mentioned in [Commit Frequency](#commit-frequency) section, any branches, including work-in-progress or development-only code, should be pushed to Barrel's repo (the truth repository) **daily** with an accompanying **Merge Request** (MR) and assigned to the lead developer. 
2. If the feature or branch is still in progress, prepend "WIP: " to the title of the merge request, and outline what's been addressed and what's remaining in the body of the MR message body.
 > **WIP: Add carousel feature**
 > 
 > - Adds carousel module for desktop
 > - Working on responsive styles
3. If a branch on an existing MR becomes outdated, it must be rebased on `develop` or `master` depending on whether it is a hotfix or feature to be added. Processing commits that include style or javascript minification, compression, or post-processing should be skipped in these cases as noted in the [What to Commit](#what-to-commit) section.
4. Close the MR and delete any branches that do not get merged because a new branch was created in its place. Branches will be automatically deleted after a successful merge request.
5. Resolve any discussions or issues raised directly in the git web interface (Gitlab, Github, and Bitbucket all have this feature) of a merge request to indicate the note was understood and/or fully executed.

### Gitflow

We currently recommend and use the [gitflow-avh](https://github.com/petervanderdoes/gitflow-avh) collection of extenstions to make gitflow easier. With that said, this does not prohibit developers from following gitflow using pure git commands. Also when using gitflow, the two most important things to handle at the end of a release or hotfix are updating the Changelog and incrementing versioning.

#### Tips for working with [Feature] Branches

- Avoid using really long-winded branch names; instead use the most descriptive words, all lowercase, and joined by hyphens. If you're using long branch names, it becomes a pain to review.
- When merging a feature into Master, **add a pull request on github** rather than doing a simple merge. This helps to document the integration and lets other devs comment on the merge. You can perform localized test merges before issuing a pull request to ensure a smooth merge.
- Always fast forward your branch and/or rebase from the most recent merge point before adding a pull request so that you can resolve conflicts before submitting the PR.
- Delete feature branches after they have been merged into "master".

#### Avoid erroneous or duplicate commits when merging
By refraining from cyclical or redundant merges, you can ensure you don't reintroduce bugs that were previously removed or wipe out concurrent changes from other developers. 
- Avoid merging the same branches: `Merge remote-tracking branch 'origin/fix-tickets' into fix-tickets`
- Avoid merging master into your branch: `Merge branch 'master' into fix-tickets`
  - Instead try to rebase your branch onto master.
  - Alternatively, try creating a new branch and cherry-pick your good commits.
- Initially perform `git fetch --all` to ensure all local branches are synced with the remote. 
- If you see any inconsistencies, you can review, rebase, refactor commits into a new branch by cherry-picking, or get clarification from the project maintainer.

## Working on an Existing App

Once an app is live, it's a good idea to add a "staging" branch to your repo, and change your workflow to something like:

1. Create a feature branch, add code.  
2. Merge your feature into "staging"  
3. Push the "staging" code to the staging server for testing.  
4. Confirm that everything works.  
5. Merge your feature into "master".  
6. Push "master" code to the production server and delete feature branch.  

This way, if you are in the middle of testing a feature and something more important comes up (a bug on production), you can fix the issue without having to remove your feature code from "master" (because only production-ready code is ever merged to 
"master").

## Issues

Github Issues are a great way to keep track of bugs and feature requests. Issues can also be linked to Commits and Pull Requests, which leaves a nice paper trail for anybody trying to track progress on the project.

To link an issue, simply use the special [issue syntax](https://help.github.com/articles/closing-issues-via-commit-messages) within a commit message. When your commit is merged into the "master" branch, the issue will close automatically.

For Example, given the issue `#21 - Usernames should link to the user's profile`, you could add the following commit:

```
git add .
git commit -m "fixes #21, usernames now link to user's profile"
```

Once merged into "master", the keyword _"fixes #21"_ lets Github know to close issue #21 and add a reference to this commit.

## Quick Reference

**Check status of branch where you're at:**
```
git status
```

**Retrieve/update all remote tracking branches:**
```
git fetch --all
```

**Push to branch 'master' on remote 'origin':**
```
git push origin master
```

**Pull from branch 'master' on remote 'origin':**
```
git pull origin master
```

**Stage/add all untracked files:**
```
git add --all
```
Or, to add just the files in the current directory:
```
git add .
```

**Commit all staged/added files:**
```
git commit -a
```
To add a message, add the `-m` flag:
```
# commit all changes, with message
git commit -am "<message>"
# commit staged changes, with message
git commit -m "<message>"
```

**Checkout an existing branch 'staging':**
```
git checkout staging
```

**Create a new branch `test`:**
```
git branch test
```
Or, create a new branch `test` *and* switch to the new branch:
```
git checkout -b test
```

**Delete branch 'test':**
```
git branch -D test
```

**Add a remote 'origin':**
```
git remote add origin https://github.com/barrel/barrel-dev-best-practices.git
```

**List all remotes verbosely:**
```
git remote -v
```

**View past commits, with hash and messages:**
```
git log
```
You can optionally pass a number of commits. In this example, 10:
```
git log -10
```

**Retrieve commit hash of current commit, last commit, five commits prior:**
```
git rev-parse HEAD
git rev-parse HEAD~
git rev-parse HEAD~5
```

**You forgot to change or commit a file in the last commit:**
```
# add changed file
git add path/to/file.js
# or
git add .

# ammend last commit
git commit --amend
# this will open vim for you to add a commit message
# enter your message, then type
:wq
# to save and commit the changes

# alternatively, you can leave you last commit message unchanged
git commit --amend --no-edit
```
