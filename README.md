# Gitflow

## Quick Legend

<table>
  <thead>
    <tr>
      <th>Instance</th>
      <th>Branch</th>
      <th>Description, Instructions, Notes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Stable</td>
      <td>master</td>
      <td>Accepts pull requests from devel and Hotfixes</td>
    </tr>
    <tr>
      <td>Working</td>
      <td>devel</td>
      <td>Accepts pull requests from Features/Issues and Hotfixes</td>
    </tr>
    <tr>
      <td>Features/Issues</td>
      <td>[topic]-*</td>
      <td>Always branch off HEAD of Working</td>
    </tr>
    <tr>
      <td>Hotfix</td>
      <td>hf-*</td>
      <td>Always branch off master</td>
    </tr>
  </tbody>
</table>

`* = <name/desc>`

`[topic] = feature/minor`

## Evergreen Branches

Evergreen branches are branches that are created on creation of the repository. These branches are also protected, meaning you cannot simply push to them, you have to make a pull request to get a supporting branches commits into them. There are two main workflows to keep in mind when thinking about evergreen branches:

### Stable Workflow

The stable workflow emphasizes stable code in production.

Under a stable workflow there will always be two evergreen branches:

* `master`
* `devel`

The primary branch should be considered `origin/devel` and will be the branch where the source code of `HEAD` always reflects a state with the latest completed development changes for the next deployment cycle. As a developer, you will be branching and merging from `origin/devel` or a local `devel` that is updated with the latest changes from `origin/devel`. 

Consider `origin/master` to always represent the latest code deployed to production. During day to day development, `master` will not be interacted with. 

When the source code in the devel branch is ready to be deployed, a pull request will be created into master and be given a release number following the semantic versioning standard.

### Fast Workflow

The fast workflow ephasizes getting commits into production as fast as possible. It is almost as if everything is a hotfix.

When a repository uses the fast workflow, the Stable and Working instances are the same. This usually happens when a repository is extremely new and stability, uptime, and versioning are not a priority. In this case there is only one evergreen branch and it should be called `master`. If you are working on a repository with the fast workflow in place then `master` and `devel` branches should be considered the same in this document. In otherwords anytime you see `devel` replace it with master.

## Supporting Branches

Supporting branches are used to aid parallel development between team members, ease tracking of features, and to assist in quickly fixing live production problems. Unlike the evergreen branches, these branches always have a limited life time, since they will be removed eventually.

The different types of branches we may use are:

* Feature branches
* Minor branches
* Hotfix branches
* Devops branches

Each of these branches have a specific purpose and are bound to strict rules as to which branches may be their originating branch and which branches it must create a pull request back into once completed. Each branch and its usage is explained below.

### Feature Branches

Feature branches are used when developing a new feature or enhancement and should be related to an epic in taiga, meaning it has the potential of a development lifespan longer than a single sprint. This makes it easier to colaborate and push to the feature branch without bogging down other branches with incomplete code and keeps `devel` and `master` clean. No matter when the feature branch will be finished, it will always be merged via a pull request back into the `devel` branch.

During the lifespan of the feature development, the lead should watch the `devel` branch to see if there have been commits since the feature was branched. Any and all changes to `devel` should be merged into the feature branch before creating a pull request back to `devel`; this can be done at various times during the project and always at the end of the branches lifespan, but time to handle merge conflicts should be accounted for.

#### Branching Rules:
* Must branch from: `devel`
* Must create pull request back into: `devel`
* Branch naming convention: `feature-short_feature_name` ex: `feature-document_viewer`

#### Working with a feature branch

If the branch does not exist yet, create the branch locally and then push to origin. A feature branch should always be 'publicly' available. That is, changes should never exist in just one developer's local branch.

```
$ git fetch                                 // updates all remote changes
$ git pull origin HEAD                      // applies remote changes to local copy of devel
$ git checkout -b feature-<name/desc> devel // creates a local branch for the new feature
$ git push origin feature-<name/desc>       // makes the new feature remotely available
```

Periodically, changes made to `devel` (if any) should be merged back into your feature branch by the lead of said feature.

```
$ git merge devel                           // merges changes from devel into feature branch
```

When development on the feature is complete a pull request should be created. The assignee should be the person who was assigned the feature ie. the team lead, or single dev and the reviewer should be your lead or your assigned reviewer. 

### Minor Branches

Minor branches differ from feature branches only semantically. Minor branches will be created when there is something small such as a bug or small change on the live site that should be fixed/added and merged into the next deployment and should be related to a story on taiga. For that reason, a minor branch will typically be completed in a single sprint. The minor branch will always be merged via a pull request back into the `devel` branch.


Although likelihood will be less, during the lifespan of the minor branches development, the lead should watch the `devel` branch to see if there have been commits since the the minor branch was branched. Any and all changes to `devel` should be merged into the minor branch before creating a pull request back to `devel`; this can be done at various times during the project and always happen at the end of the branches lifespan, but time to handle merge conflicts should be accounted for.

#### Branching Rules:
* Must branch from: `devel`
* Must create pull request back into: `devel`
* Branch naming convention: `minor-short_minor_name`

#### Working with a minor branch

If the branch does not exist yet, create the branch locally and then push to origin. A minor branch should always be 'publicly' available. That is, changes should never exist in just one developer's local branch.

```
$ git checkout devel                        // ensure you are currently on the devel branch
$ git fetch                                 // updates all remote changes
$ git pull origin HEAD                      // applies remote changes to local copy of devel
$ git checkout -b minor-<name/desc> devel   // creates a local branch for the new minor branch
$ git push origin minor-<name/desc>         // makes the new branch remotely available
```

Periodically, changes made to `devel` (if any) should be merged back into your minor branch by the lead of said minor branch.

```
$ git merge devel                           // merges changes from devel into minor branch
```

When development on the minor branch is complete a pull request should be created. The assignee should be the person who was assigned the feature ie. the team lead, or single dev and the reviewer should be your lead or your assigned reviewer. 

### Hotfix Branches

A hotfix branch comes from the need to act immediately upon an undesired state of a live production version. Additionally, because of the urgency, a hotfix is not required to be be pushed during a scheduled deployment. Due to these requirements, a hotfix branch is always branched from the `master` branch. This is done for two reasons:

* Development on the `devel` branch can continue while the hotfix is being addressed.
* The master branch represents what is in production. At the point in time where a hotfix is needed, there could have been multiple commits to `devel` which would then no longer represent production.

#### Branching Rules:
* Must branch from: `master`
* Must create pull request back into: `master` and `devel`
* Branch naming convention: `hf-short_hotfix_name`

#### Working with a hotfix branch

If the branch does not exist yet, create the branch locally and then push to origin. A hotfix branch should always be 'publicly' available. That is, development should never exist in just one developer's local branch.

```
$ git checkout master                       // ensure you are currently on the master branch
$ git fetch                                 // updates all remote changes
$ git pull origin HEAD                      // applies remote changes to local copy of master
$ git checkout -b hf-<name/desc> master     // creates a local branch for the new hotfix
$ git push origin hf-<name/desc>            // makes the new hotfix remotely available
```

When development on the hotfix is complete two pull requests should be created, one into `devel` as to not lose the changes, and the other into `master`. The assignee should be the person who was assigned the hotfix ie. the team lead, or single dev and the reviewer should be your lead or your assigned reviewer. 

## Pull Requests

Pull requests are useful for two main reasons. It allows a branch to be merged back into an evergreen branch, and allows for code review. There should always be at least two people involved in a pull request: a reviewer and an assignee. Everyone who has made a commit to the branch should be added to the assignees feild. The reviewer should be someone who is either assigned or someone who did not work on the supporting branch

### Resposibilities

* Reviewer: The reviewers only job is to approve the pull request. They should not be the one to actually merge the pull request. They should also leave comments on the pull request if they have any questions, comments, or concerns about the code they are reviewing
* Assignees: The assignees should reply to the reviewer if they have commented anything that warrents a response and make changes and push them to the branch when needed. Once the reviewer approves the pull request the lead dev of the branch should go ahead and merge the branch into necessary branches.

## Workflow Diagram

![worflow diagram](https://github.com/BeyondMD/gitflow/blob/master/img/final-gitflow.png)

## Other Material
* [digitaljhelms's branching rules](https://gist.github.com/digitaljhelms/4287848) (what this is based on)
* [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/)
