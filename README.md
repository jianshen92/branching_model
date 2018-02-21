# Branching Model
How to Git 101 - A successful git branching model

Reference : http://nvie.com/posts/a-successful-git-branching-model/

## Create a local copy of the project
**Option 1 : Good Old Git Clone**

**Option 2 : Fork**

## The Main Branches
#### Master
* `origin/master` should be the main branch where the source code of HEAD always reflects a production-ready state.
* No developers should directly make a commit to `master`
* Every time a commit is made on master should trigger a CI/CD build, in our case, Jenkins, 
to automatically deploy our software to the production server
#### Develop
* `origin/develop` should be the main branch where the source code of HEAD always reflects a state with the latest delivered 
development changes for the next release.
* Every commit in `develop` should trigger a CI/CD build to deploy the system on the development server
* When the source code in the `develop` branch reaches a stable point and is ready to be released, 
all of the changes should be merged back into `master` and then tagged with a release number.

## Supporting Branches
#### Feature

* Feature branches are used to develop new features for the upcoming or a distant future release.
* Branching Practice
    * Must branch off from - `develop`
    * Must merge back into - `develop`
* Branch naming convention 
    * Anything except `master`, `develop`, or `hotfix-*`
    * Naming should be VERBOSE enough to describe the task/feature of the feature branch. 
    i.e : `logic-block-barring`, `gantry-ui-skeleton`, `api-connector`
* Once the development for the branch is completed, instead of merging directly, submit a **Pull Request** (PR).
* Avoid compressing too many tasks/features into a feature branch. 
If a branch takes too long to complete might result incompatibility during merging. 
Ideally a branch should sync with a Jira task, which will be discussed further.

#### Hotfix
* Hotfix branches are used when a critical bug in a production version must be resolved immediately.
* Branching Practice
    * May branch off from - `master`    
    * Must merge back into - `develop` and `master`
* Branch naming convention - `hotfix-*`

#### Release (We are omitting this)
* Release branches support preparation of a new production release. 
It serves as a middle layer between `develop` and `master`. 
Usually in `release`, only bugfixes and last minute changes are made.
* I have decided to drop this branch for our Wooppy's project for a few reason:
    * Simplify project branches and speed up development.
    * CI/CD workflow with pull requests and code review would means the source codes in `develop` is rather robust.


## Pull Requests
Pull requests let you tell others about changes you've pushed to a GitHub repository. 
Once a pull request is sent, interested parties can review the set of changes, discuss potential modifications, 
and even push follow-up commits if necessary. [Reference](https://yangsu.github.io/pull-request-tutorial/)

* Once you have completed your development in your feature branch, submit a pull request to merge your code into `develop`
* Any Pull Request will trigger a CI/CD(Jenkins) build.
* To approve a Pull Request, there are several condition to be met, ideally :
    * Project Build/Compile has to be successful (Jenkins) (Build Java/Gradle)
    * Pass All Unit Test (Jenkins) (Spock)
    * Pass Linting (Jenkins) (Some Linting Library)
    * Pass Minimum Coverage Requirement. 80% for old repository, 100% for new repository. (Jenkins) (Some Coverage Library)
    * Code Review. Pass minimum peer approval requirement. Github has a hard limit of at least 1 reviewer. (Github)
* Once all conditions are met, the code AUTHOR should be the one who perform the merge.

## Other Practices
#### Commit Messages
#### Code Review
#### Unit Test  |  Coverage
#### Jira Integration Convention
