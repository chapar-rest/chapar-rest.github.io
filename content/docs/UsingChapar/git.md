---
title: "Git"
weight: 111
summary: "Commit, push, pull, branch and merge a space's requests, collections and environments from the Git page"
---

A space kept in a Git repository can be shared with a team the way code is: commit your changes, push them, and pull your teammates'. The **Git** page in the navigation bar does all of this without leaving Chapar.

Chapar uses the `git` installed on your computer. If it can't find one, the page says so and tells you how to install it for your system, with a **Check again** button.

## Set up a repository

If the space isn't in a repository yet, the Git page offers **Set up Git**, which runs `git init` in the space's folder. Local state, such as [secret values](../environments#secret-values) and [cookies](../cookies), lives in the space's `.state` folder, which git ignores, so it never ends up in a commit.

A space inside a larger repository (for example one kept next to your service's code) works too. The page shows a **Shared repository** badge and lists only the space's own files.

## Changes

The **Changes** tab lists every added, changed, renamed and deleted request, collection, environment, test case and proto file, by its name rather than by its file. Click one to see its diff; open it in a tab from there.

- Tick the changes that go into the next commit, or use **Select all** / **Select none**.
- Write a **Commit message** and click **Commit**. The first time, Chapar asks for the name and email git records with your commits, and sets them for this repository only.
- **Discard** puts a change back the way the last commit has it.

Tabs with unsaved changes aren't in the commit until you save them; the page reminds you when there are some.

## History

The **History** tab lists the commits with their author, date and message. Click one to see the items it changed and their diffs. **Revert** makes a new commit that undoes it.

## Remotes: fetch, pull and push

The header shows the current branch and how it compares with its remote branch (*Up to date*, *2 ahead*, *1 behind*). From there:

- **Connect remote** adds a remote to a repository that has none. **Change remote…** in the **More** menu changes its URL.
- **Pull** and **Push** bring in and send commits. **Fetch** (in **More**) checks the remote without changing your files.
- For an **HTTPS** remote, Chapar asks once for your user name and an **access token** (such as a GitHub personal access token). It saves the token in your system's secret store and uses it for every repository on that server. **Forget saved token** removes it. SSH remotes use your usual SSH keys.

## Branches and merging

Click the branch name to switch to another branch. The branch menu next to it has **New branch…** and **Merge a branch into …**.

When both sides changed the same item, Chapar doesn't show you conflict markers in a YAML file. It shows the conflicting item field by field, so you pick your version or theirs for each field (or **All from** one side), then **Mark resolved**. When every conflict is resolved, **Finish merge** commits it; **Abort** goes back to how things were before the merge.

{{< callout type="info" >}}
Pull, switching branches, merging, reverting and discarding rewrite files on disk, so Chapar asks you to save or close tabs with unsaved changes first.
{{< /callout >}}

## Clone a space

To get a space a teammate pushed, open **Spaces** and click **Clone**. Enter the repository URL (and, for HTTPS, your user name and token), and Chapar clones it as a new space.

Secret values aren't in the repository. After cloning, a secret with no value on this machine is marked **unset**: it isn't filled into requests, and the environment editor lists it so you can set it. See [Secret values](../environments#secret-values).
