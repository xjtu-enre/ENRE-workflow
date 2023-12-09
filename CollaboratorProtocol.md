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
        <td>Follow scenario YY, but do all things in the forked repo</td>
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

This kind of commits are requested to not be publicly available, so it goes to a private repo (downstream). However, to maintain the proper relationship with the main ENRE repo (upstream), you should fork the main ENRE repo (but not create a new repo and push all files once, which will lose all previous commit history).

* **FORK** the main ENRE repo, and push into the new repo.

> Continue reading [how to sync](#syncing-between-upstream-and-downstream)

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
        <td>Forked repo</td>
        <td>Only the main branch of the main repo should be visible to the downstream, you can click the <code>Sync fork</code> button in GitHub</td>
        <td>Only the main branch of the forked repo should be visible to the upstream, you can create a PR to the upstream in GitHub</td>
    </tr>
</table>

> If there are conflicts while merging, it is the receiver side who is responsible for resolving them.
