# Branching

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
      <td>main</td>
      <td>Accepts merges from Staging and Hotfixes</td>
    </tr>
    <tr>
      <td>Staging</td>
      <td>staging</td>
      <td>Accepts merges from Features/Issues and Hotfixes</td>
    </tr>
    <tr>
      <td>Features/Issues</td>
      <td>topic-*</td>
      <td>Always branch off HEAD of Staging</td>
    </tr>
    <tr>
      <td>Hotfix</td>
      <td>hf-*</td>
      <td>Always branch off Stable</td>
    </tr>
    <tr>
      <td>Snapshot</td>
      <td>snapshot</td>
      <td>Accepts merges from features that are being tested by end users for feedback.</td>
  </tbody>
</table>

## Main Branches

The main repository will always hold three evergreen branches:

* `main`
* `staging`

The main branch should be considered `origin/staging` and will be the main branch where the source code of `HEAD` always reflects a state with the latest delivered development changes for the next release. As a developer, you will be branching and merging from `staging`.

Consider `origin/main` to always represent the latest code deployed to production. During day to day development, the `main` branch will not be interacted with.

When the source code in the `staging` branch is ready to be deployed, all of the changes will be merged into `main` and be given a release number with the semantic versioning standard.

## Supporting Branches

Supporting branches are used to aid parallel development between team members, ease tracking of features, and to assist in quickly fixing live production problems. Unlike the main branches, these branches always have a limited life time, since they will be removed eventually.

The different types of branches we may use are:

* Feature branches
* Minor branches
* Hotfix branches

Each of these branches have a specific purpose and are bound to strict rules as to which branches may be their originating branch and which branches must be their merge targets. Each branch and its usage is explained below.

### Feature Branches

Feature branches are used when developing a new feature or enhancement and will tend to be related to either a story or an epic on jira, meaning it has the potential of a development lifespan longer than a single deployment cycle. This makes it easier to colaborate and push to the feature branch without slowing down completed features and issues from being merged from `staging` into `main`. No matter when the feature branch will be finished, it will always be merged back into the `staging` branch.

During the lifespan of the feature development, the lead should watch the `staging` branch to see if there have been commits since the feature was branched. Any and all changes to `staging` should be merged into the feature before merging back to `staging`; this can be done at various times during the project or at the end, but time to handle merge conflicts should be accounted for.

Branching Rules:
* Must branch from: `staging`
* Must merge back into: `staging` or `snapshot` if applicable
* Branch naming convention: `feature-<short feature name/description>` ex: `feature-emsoap`

#### Working with a feature branch

If the branch does not exist yet (check with the Lead), create the branch locally and then push to origin. A feature branch should always be 'publicly' available. That is, development should never exist in just one developer's local branch.

```
$ git fetch                                           // updates all remote changes
$ git pull origin HEAD                                // applies remote changes to local copy of staging
$ git checkout -b feature-<name/desc> staging         // creates a local branch for the new feature
$ git push origin feature-<name/desc>                 // makes the new feature remotely available
```

Periodically, changes made to `staging` (if any) should be merged back into your feature branch by the lead of said feature.

```
$ git merge staging                                  // merges changes from staging into feature branch
```

When development on the feature is complete a merge request should be created. The assignee should be the person who was assigned the feature ie. the team lead, or single dev and the reviewer should be your lead or your assigned reviewer. 

### Minor Branches

Minor branches differ from feature branches only semantically. Minor branches will be created when there is something small such as a bug or small change on the live site that should be fixed/added and merged into the next deployment and will tend to be related to either a task on jira. For that reason, a minor branch typically will not last longer than one deployment cycle. No matter when the minor branch will be finished, it will always be merged back into `staging`.

Although likelihood will be less, during the lifespan of the minor branches development, the lead should watch the `staging` branch to see if there have been commits since the the minor branch was branched. Any and all changes to `staging` should be merged into the minor change before merging back to `staging`; this can be done at various times during the project or at the end, but time to handle merge conflicts should be accounted for.

Branching Rules:
* Must branch from: `staging`
* Must merge back into: `staging`
* Branch naming convention: `minor-<name/desc>`

#### Working with a minor branch

If the branch does not exist yet (check with the Lead), create the branch locally and then push to origin. A minor branch should always be 'publicly' available. That is, development should never exist in just one developer's local branch.

```
$ git checkout staging                              // ensure you are currently on the staging branch
$ git fetch                                         // updates all remote changes
$ git pull origin HEAD                              // applies remote changes to local copy of staging
$ git checkout -b minor-<name/desc> staging         // creates a local branch for the new minor branch
$ git push origin minor-<name/desc>                 // makes the new bug remotely available
```

Periodically, changes made to `staging` (if any) should be merged back into your minor branch by the lead of said minor change.

```
$ git merge staging                                 // merges changes from staging into minor branch
```

When development on the feature is complete a merge request should be created. The assignee should be the person who was assigned the feature ie. the team lead, or single dev and the reviewer should be your lead or your assigned reviewer. 

### Hotfix Branches

A hotfix branch comes from the need to act immediately upon an undesired state of a live production version. Additionally, because of the urgency, a hotfix is not required to be be pushed during a scheduled deployment. Due to these requirements, a hotfix branch is always branched from the `main` branch. This is done for two reasons:

* Development on the `staging` branch can continue while the hotfix is being addressed.
* The main branch represents what is in production. At the point in time where a hotfix is needed, there could have been multiple commits to `staging` which would then no longer represent production.

Branching Rules:
* Must branch from: `main`
* Must merge back into: `staging` and `main`
* Branch naming convention: `hf-<name/desc>`

#### Working with a hotfix branch

If the branch does not exist yet (check with the Lead), create the branch locally and then push to origin. A hotfix branch should always be 'publicly' available. That is, development should never exist in just one developer's local branch.

```
$ git checkout main                                      // ensure you are currently on the staging branch
$ git fetch                                              // updates all remote changes
$ git pull origin HEAD                                   // applies remote changes to local copy of main
$ git checkout -b hf-<name/desc> main                    // creates a local branch for the new hotfix
$ git push origin hf-<name/desc>                         // makes the new hotfix remotely available
```

When development on the hotfix is complete two merge request should be created, one into `staging` as to not loose the changes, and the other into `main`. The assignee should be the person who was assigned the hotfix ie. the team lead, or single dev and the reviewer should be your lead or your assigned reviewer. 

### Snapshot Branch

The snapshot branch exists for the sole purpose of having end users test out new features before they are ready to be merged into `staging`. `snapshot` is connected to a server and any changes merged into it will be present on the on a live testing site. This allows the end user to test out features that are not staged yet so that the devs can receive feedback on their feature. When the testing period is over the snapshot branch should be deleted by a devops engineer or the project lead.

#### Working with a snapshot branch

The snapshot branch should only be used if the lead dev of a feature is given the go ahead. That lead dev should then merge the changes of the feature branch being tested via a merge request (which does not have to be reviewed) into the snapshot branch on a nightly basis. The testing period of the feature ends once it is merged into `staging` and the feature branch has been deleted.

## Other Material
* [digitaljhelms's brnahcing rules](https://gist.github.com/digitaljhelms/4287848)(what this is based on)
* [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/)
