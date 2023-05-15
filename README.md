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
      <td>Accepts  from Working and Hotfixes</td>
    </tr>
    <tr>
      <td>Working</td>
      <td>devel</td>
      <td>Accepts merges from Features/Issues and Hotfixes</td>
    </tr>
    <tr>
      <td>Features/Issues</td>
      <td>topic-*</td>
      <td>Always branch off HEAD of Working</td>
    </tr>
    <tr>
      <td>Hotfix</td>
      <td>hf-*</td>
      <td>Always branch off Stable</td>
    </tr>
  </tbody>
</table>

`* = <name/desc>`

`topic = feature/minor`

## Evergreen Branches

Evergreen branches are branches that are created on creation of the repository. These branches are also protected, meaning you cannot simply push to them, you have to make a pull request to get a supporting branches commits into them. There are two main workflows to keep in mind when thinking about evergreen branches:

### Stable Workflow

The stable workflow emphasizes stable code in production.

Under a stable workflow there will always be two evergreen branches:

* `master`
* `devel`

The primary branch should be considered `origin/devel` and will be the branch where the source code of `HEAD` always reflects a state with the latest completed development changes for the next deployment cycle. As a developer, you will be branching and merging from `origin/devel` or an local `devel` that is updated with the latest changes from `origin/devel`. 

Consider `origin/master` to always represent the latest code deployed to production. During day to day development, `master` will not be interacted with. 

When the source code in the devel branch is ready to be deployed, all of the changes will be merged into master and be given a release number following the semantic versioning standard.

### Fast Workflow

When a repository uses the fast workflow, the Stable and Working instances are the same. This usually happens when a branch is extremely new and stability is not a priority. In this case there is only one evergreen branch and it should be called `master`. If you are working on a repository with the fast workflow in place then the Stable and Working instances and their respective branches should be considered the same in this document.

## Supporting Branches

Supporting branches are used to aid parallel development between team members and to assist in quickly fixing live production problems. Unlike the evergreen branches, these branches always have a limited life time, since they will be removed eventually.

The different types of branches we may use are:

* Feature branches
* Minor branches
* Hotfix branches
* Devops branches

Each of these branches have a specific purpose and are bound to strict rules as to which branches may be their originating branch and which branches must be their merge targets. Each branch and its usage is explained below.

### Feature Branches

Feature branches are used when developing a new feature or enhancement and will tend to be related to either a story or an epic on jira, meaning it has the potential of a development lifespan longer than a single deployment cycle. This makes it easier to colaborate and push to the feature branch without slowing down completed features and issues from being merged from `devel` into `master`. No matter when the feature branch will be finished, it will always be merged back into the `devel` branch.

During the lifespan of the feature development, the lead should watch the `devel` branch to see if there have been commits since the feature was branched. Any and all changes to `devel` should be merged into the feature before merging back to `devel`; this can be done at various times during the project or at the end, but time to handle merge conflicts should be accounted for.

Branching Rules:
* Must branch from: `devel`
* Must merge back into: `devel` or `snapshot` if applicable
* Branch naming convention: `feature-<short feature name/description>` ex: `feature-emsoap`

#### Working with a feature branch

If the branch does not exist yet (check with the Lead), create the branch locally and then push to origin. A feature branch should always be 'publicly' available. That is, development should never exist in just one developer's local branch.

```
$ git fetch                                           // updates all remote changes
$ git pull origin HEAD                                // applies remote changes to local copy of devel
$ git checkout -b feature-<name/desc> devel         // creates a local branch for the new feature
$ git push origin feature-<name/desc>                 // makes the new feature remotely available
```

Periodically, changes made to `devel` (if any) should be merged back into your feature branch by the lead of said feature.

```
$ git merge devel                                  // merges changes from devel into feature branch
```

When development on the feature is complete a merge request should be created. The assignee should be the person who was assigned the feature ie. the team lead, or single dev and the reviewer should be your lead or your assigned reviewer. 

### Minor Branches

Minor branches differ from feature branches only semantically. Minor branches will be created when there is something small such as a bug or small change on the live site that should be fixed/added and merged into the next deployment and will tend to be related to either a task on jira. For that reason, a minor branch typically will not last longer than one deployment cycle. No matter when the minor branch will be finished, it will always be merged back into `devel`.

Although likelihood will be less, during the lifespan of the minor branches development, the lead should watch the `devel` branch to see if there have been commits since the the minor branch was branched. Any and all changes to `devel` should be merged into the minor change before merging back to `devel`; this can be done at various times during the project or at the end, but time to handle merge conflicts should be accounted for.

Branching Rules:
* Must branch from: `devel`
* Must merge back into: `devel`
* Branch naming convention: `minor-<name/desc>`

#### Working with a minor branch

If the branch does not exist yet (check with the Lead), create the branch locally and then push to origin. A minor branch should always be 'publicly' available. That is, development should never exist in just one developer's local branch.

```
$ git checkout devel                              // ensure you are currently on the devel branch
$ git fetch                                         // updates all remote changes
$ git pull origin HEAD                              // applies remote changes to local copy of devel
$ git checkout -b minor-<name/desc> devel         // creates a local branch for the new minor branch
$ git push origin minor-<name/desc>                 // makes the new bug remotely available
```

Periodically, changes made to `devel` (if any) should be merged back into your minor branch by the lead of said minor change.

```
$ git merge devel                                 // merges changes from devel into minor branch
```

When development on the feature is complete a merge request should be created. The assignee should be the person who was assigned the feature ie. the team lead, or single dev and the reviewer should be your lead or your assigned reviewer. 

### Hotfix Branches

A hotfix branch comes from the need to act immediately upon an undesired state of a live production version. Additionally, because of the urgency, a hotfix is not required to be be pushed during a scheduled deployment. Due to these requirements, a hotfix branch is always branched from the `master` branch. This is done for two reasons:

* Development on the `devel` branch can continue while the hotfix is being addressed.
* The master branch represents what is in production. At the point in time where a hotfix is needed, there could have been multiple commits to `devel` which would then no longer represent production.

Branching Rules:
* Must branch from: `master`
* Must merge back into: `devel` and `master`
* Branch naming convention: `hf-<name/desc>`

#### Working with a hotfix branch

If the branch does not exist yet (check with the Lead), create the branch locally and then push to origin. A hotfix branch should always be 'publicly' available. That is, development should never exist in just one developer's local branch.

```
$ git checkout master                                      // ensure you are currently on the devel branch
$ git fetch                                              // updates all remote changes
$ git pull origin HEAD                                   // applies remote changes to local copy of master
$ git checkout -b hf-<name/desc> master                    // creates a local branch for the new hotfix
$ git push origin hf-<name/desc>                         // makes the new hotfix remotely available
```

When development on the hotfix is complete two merge requests should be created, one into `devel` as to not loose the changes, and the other into `master`. The assignee should be the person who was assigned the hotfix ie. the team lead, or single dev and the reviewer should be your lead or your assigned reviewer. 

## Snapshot Branch

The snapshot branch exists for the sole purpose of having end users test out new features before they are ready to be merged into `devel`. The only branches that can merge into `snapshot` are feature branches (not minor). `snapshot` is connected to a server and any changes merged into it will be present on a live testing site. This allows the end user to test out features that are not yet staged so that the devs can receive feedback on their feature. When the testing period is over the snapshot branch should be deleted by a devops engineer or the project lead to keep the repo clean.

### Working with a snapshot branch

The snapshot branch should only be used if the lead dev of a feature branch has been given the go ahead. That lead dev should then merge the changes of the feature branch being tested via a merge request (which does not have to be reviewed) into the snapshot branch on a nightly basis. The testing period of the feature ends once it is merged into `devel` and the feature branch has been deleted.

## Workflow Diagram

![worflow diagram](https://github.com/BeyondMD/gitflow/blob/main/img/final-gitflow.png)

## Other Material
* [digitaljhelms's branching rules](https://gist.github.com/digitaljhelms/4287848) (what this is based on)
* [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/)
