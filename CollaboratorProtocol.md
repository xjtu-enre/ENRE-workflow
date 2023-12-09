# Collaborator Protocol

This document defines how internal / outside collaborators works together based on Git & GitHub workflow.

## TL;DR

While making contributions to ENRE, choose an applicable solution that meets your specific requirements:

<table>
    <tr>
        <th colspan="2">Is the contribution ...</th>
        <th colspan="2">Where should the commit be pushed into</th>
        <th rowspan="2">How to</th>
        <th rowspan="6">As long as the the commit is not pushed into the main branch of the main repo, then you are also responsible for <a href="#syncing-between-upstream-and-downstream">regularly syncing between upstream and downstream.</a></th>
    </tr>
    <tr>
        <th>Opensource-able</th>
        <th>A custom patch</th>
        <th>Repo</th>
        <th>Branch</th>
    </tr>
    <tr>
        <td rowspan="2">Yes</td>
        <td>Yes</td>
        <td rowspan="2">The main ENRE repo</td>
        <td>A new branch</td>
        <td><a href="#scenario-yy-an-open-but-customized-commit">Scenario YY</a></td>
    </tr>
    <tr>
        <td>No</td>
        <td>Main</td>
        <td><a href="#scenario-yn-an-open-and-common-commit">Scenario YN</a></td>
    </tr>
    <tr>
        <td rowspan="2">No</td>
        <td>Yes</td>
        <td rowspan="2">A duplicated repo</td>
        <td>A new branch</td>
        <td>Follow scenario YY, but do all things in the duplicated repo</td>
    </tr>
    <tr>
        <td>No</td>
        <td>Main</td>
        <td><a href="#scenario-nn-an-private-and-common-commit">Scenario NN</a></td>
    </tr>
</table>

> A custom patch is a commit that you would not like it to be also presented in the main branch.

## Scenario YN: An open and common commit

This kind of commits should be merged with the main ENRE repo ultimately, so you can follow the regular git workflow:

* Directly push to the main branch;

* Or create a new branch, push into the new branch, (after approved) merge it to the main branch.

## Scenario YY: An open but customized commit

This kind of commits should not be merged with the main branch, given this commit serves a customized requirement that is not applicable for all users. However, it is still an open-source one, so it should go into the main ENRE repo.

* Create a new branch in the main ENRE repo, push the commit into the new branch.

> Continue reading [how to sync](#syncing-between-upstream-and-downstream)

## Scenario NN: An private and common commit

This kind of commits are requested to not be publicly available, so it goes to a private repo (downstream). However, to inherit the previous commit history, you should NOT create a new repo and push all files once (which will lose all previous commit history), but using the GitHub duplicate mechanism. Below is the step-by-step guide.

> GitHub restrict the visibility of a fork of a public repo to be public, so **fork is NOT appropriate in the scenario**.

* Using the [**Import a repo**](https://github.com/new/import) page in GitHub, insert the main ENRE repo's url into `Your old repository's clone URL` input field, change the `Owner` to `xjtu-enre` organization, and give it a unique `Repository name`;

* Click the `Begin import` button, and wait GitHub to complete the import;

* Once the duplicated repo is successfully created, you can clone it to local and work on it by

```bash
$ git clone https://github.com/xjtu-enre/private-repo.git
$ cd private-repo
$ private-repo> make some changes
$ private-repo> git commit
$ private-repo> git push origin main
```

### Syncing from the upstream

> Direction: Upstream -> Downstream

To receive new commits that are pushed to the main ENRE repo, follow the guide below.

* (Once) add the main ENRE repo to private's `remote` in your local environment by

```bash
$ cd private-repo
$ private-repo> git remote add public https://github.com/xjtu-enre/ENRE-xx.git
```

* (Periodically) run commands to pull public commits and push them to the private repo

```bash
$ private-repo> git pull public main
$ private-repo> git push origin main
```

> The first `pull` command pull public commits into your local repo, and the second `push` command push your local new commits to GitHub. Both the commands should be executed so that commits in GitHub's public repo can go into GitHub's private repo.

### Contributing to the upstream

> Direction: Upstream <- Downstream

To push private commits back to the main ENRE repo, you will need a fork to serve as the intermediate.

* Using the GitHub web interface to create a fork of the main ENRE repo, naming and owner don't matter;

* then

```bash
$ git clone https://github.com/yourname/the-fork.git
$ cd the-fork
$ the-fork> git remote add private https://github.com/xjtu-enre/private-repo.git
$ the-fork> git checkout -b pr-name
$ the-fork> git pull private main
$ the-fork> git push origin pr-name
```

This pushes commits of the private repo's main branch to the forked repo;

* On forked repo's webpage, you can create a pr to the main ENRE repo to contribute your commits.

* You can then delete the forked repo, and the next time you want to contribute to the main ENRE repo, you will need to create a new fork and perform the above steps again.

> The solution is credit to https://stackoverflow.com/a/30352360/13878671

## Syncing between upstream and downstream

This section discuss the method for syncing commits between upstream and downstream, so that both downstream can receive new features from upstream and upstream can get contributions like common bug fixes from downstream.

<table>
    <tr>
        <th rowspan="2">Upstream</th>
        <th rowspan="2">Downstream</th>
        <th colspan="2">Commit direction</th>
    </tr>
    <tr>
        <th>Up -> Down</th>
        <th>Up <- Down</th>
    </tr>
    <tr>
        <td>Main repo, main branch</td>
        <td>Main repo, new branch</td>
        <td>Create a PR in GitHub by clicking <code>x commit behind</code></td>
        <td>Create a PR in GitHub by clicking <code>x commit ahead</code> or the <code>Contribute</code> button
    </tr>
    <tr>
        <td>Main repo</td>
        <td>Private repo</td>
        <td colspan="2">See <a href="#syncing-from-the-upstream">above</a></td>
    </tr>
</table>

> If there are conflicts while merging, it is the receiver side who is responsible for resolving them.
