# Branching Model
How to Git 101 - A successful git branching model

Reference : http://nvie.com/posts/a-successful-git-branching-model/

## Create a local copy of the project
**Option 1 : Good Old Git Clone**
#### Pro
* Easy to setup. Just copy the repository's HTTPS and do a `git clone <https:xxx.git>` in your preferred directory

#### Cons
Because we are all collaborators and have push access, these things might happen:
* Pollute the project's repository with dangling branches and meaningless commits.
* Somebody might fuck up something.

**Option 2 : Fork**
#### Pro 
* All pushes from team members are in form of Pull Request. Therefore, commits can be squashed, 
number of branches in the main repository can be controlled, thence we have a less polluted project repository
* Anyone can do experiments in their forked repo. If the experiment fails we can simply discard the forked repository.

#### Cons 
* Slightly more tedious to setup.

1. Fork the repository
2. Add upstream. `git remote add upstream <original-repo-HTTPS>`
3. Prevent accidental push. `git remote set-url --push upstream no_push`


## The Main Branches
#### Master
* `origin/master` should be the main branch where the source code of HEAD always reflects a production-ready state.
* No developers should directly make a commit to `master`
* Every time a commit is made on master, it should trigger a CI/CD build, in our case, Jenkins,
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
Reference : https://github.com/erlang/otp/wiki/writing-good-commit-messages, https://gist.github.com/robertpainsi/b632364184e70900af4ab688decf6f53

#####Example commit message :
> ```
> Short (50 chars or less) summary of changes
> 
> More detailed explanatory text, if necessary.  Wrap it to about 72
> characters or so.  In some contexts, the first line is treated as the
> subject of an email and the rest of the text as the body.  The blank
> line separating the summary from the body is critical (unless you omit
> the body entirely); tools like rebase can get confused if you run the
> two together.
> 
> Further paragraphs come after blank lines.
> 
>   - Bullet points are okay, too
> 
>   - Typically a hyphen or asterisk is used for the bullet, preceded by a
>     single space, with blank lines in between, but conventions vary here
> ```

##### DO
* Write the summary line and description of what you have done in the imperative mode, that is as if you were commanding someone. 
Start the line with "Fix", "Add", "Change" instead of "Fixed", "Added", "Changed".
* Always leave the second line blank.
* Line break the commit message.

##### DON'T
* Don't end the summary line with a period - it's a title and titles don't end with a period.
    
#### Code Review
There are plenty of articles and guideline in the internet that describe what to pay attention during a code review. 
Just google "[language] code review checklist" .
Here's [one](https://tedvinke.wordpress.com/2015/03/15/basic-groovy-and-grails-code-review-guidelines/) for Groovy and Grails project.

TLDR (There are too many to list out, below is my summary of some important ones): 
* Readability : Make sure code is well commented and easy to understand, i.e: Avoid using variable name such as `temp`.
* Maintainability : Refactor repeated usage of functions or objects
* Scalability : Is the feature that you are building easily deployable with other features (currently and future)?
* Security : Location/Encryption of sensitve data, sanitization of user inputs.
* Code Conventions : If a linter is properly set up it should handle this part.

Feel free to add more to the list if you think it is important. We are all new to this :)

**Why Code Review?**

To prevent code debt accumulating in a project. 
A badly written code can be extremely difficult to fix without severely halting the development progress.

If we do code review properly, the Future You will be extremely grateful for what Current You have done.
#### Unit Test  |  Coverage
Let's have 80% coverage for all old repositories, and aim for 100% for the all repositories.

#### Jira Integration | Wooppy's Smart Convention
Since we are using Jira for our project management, we MAY integrate our commits and branches to Jira Issues.

##### Commits

> ```
> <ISSUE_KEY> Short (50 chars or less) summary of changes
> 
> Your comments
> ```

Example of `ISSUE_KEY` : `RED-30`, `MVP-69`

**PS :** If your branch is already tracked according to the issue, `ISSUE_KEY` is not needed in the commit message for it to be tracked.

##### Branch
<ISSUE_KEY>-[short_description_of_feature]

Example:`RED-30-logic-block`

##### Workflow Transition
I think we should also drop this since we are using Github instead of Bitbucket. Integration might be a pain in the ass.

Drag and drop cards is more fun than writting comments anyway! 

Reference : https://confluence.atlassian.com/fisheye/using-smart-commits-298976812.html
____
## This Project
This is a template project that follows the branching practice described in this article.