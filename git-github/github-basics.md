# Github Basics

## Working by yourself 

### Pushing to a repo
* Pushing commits to a repository: 

    ```
    $ git init 
    $ git add [files] 
    $ git commit -m [message]
    $ git remote add origin [urlForRepo]
    $ git push -u origin master
    ```
* once the repo is created and you have made your first push, you can then just use `$ git push`  
* it is common to use the shorthand `$ git add .` as this adds all changed files for staging to commit

### Issues
* Issues is used to track bugs or features 
* You can also use it to track anything you want
* To create an issue, navigate to the Issues tab and add a new issue
* You can use plain text or markdown when describing the issue
* Github provides [documentation](https://guides.github.com/features/mastering-markdown/) for using markdown for you
* You can also use [emojis](https://www.webfx.com/tools/emoji-cheat-sheet/) in your issues description
* To send a notification to a user to view a comment or description in Issues, use `@username` to tag them - this is an "@ mention"

### Creating a branch
* To see all branches for a repo use `$ git branch`
* Branching allows you to work on multiple versions of a code base at once, meaning you are free to experiment until you are ready to commit 
* To create a new branch of a code base use `$ git branch [branchName]`
* To switch into a different branch other than master use `$ git checkout [branchName]`
* To create a new branch and switch into the new branch all in one command use `$ git checkout -b [branchName]`
* commit and push all changes in your new branch to the branch you are working in, not the master branch with `$ git push origin [branchName]`

### Open a pull request
* Pull requests are used to propose changes to the project files 
* First you must have a branch with changes made to it that you want to merge with the master branch 
* Navigate to Pull requests and click to add a new pull request
* It is a good idea to leave a comment explaining the pull request and referencing the issue number for the issue your pull request is attempting to fix using  `#[issueNumber]`
* When the person/people managing the repository click "merge pull request", the branch you have committed with its changes are merged into the master branch

### Resolving conflicts 
* After opening a pull request you may receive feedback or engage in a discussion that leads to you changing your code before it is accepted
* You simply make the required changes to the code, commit those changes and then push the branch again
* However, you may run into a situation when collaborating with a team in which another collaborator makes changes that lead to your copy of the repo being out of sync with the master branch, resulting in a conflict 
* this is called a merge conflict 
* You will get an error message in your terminal telling you about a conflict between the remote repository and your local respository leading to your push being rejected
* To fix this run `$ git pull origin [branchName]`
* You will then get a merge conflict error. You go into the files that contain the conflicts and manually select which parts you want to keep and which you don't
* Then you add and commit again, then push again


## Working on a team
* It is common that you will not be using Github just by yourself, but instead will be collaborating on projects with others in a workplace, or with an open source orgnaization
* Github allows you to create organization profiles that facilitate collaboration among many different individual Github accounts
* When you have multiple people working together on multiple repositories, a Github Organization will most likely be necessary
* An Organization allows you to organize and manage group-owned repositories, and are a great way for a company to share it's open-source projects
* Organizations can give specific users access to certain repositories while withholding access to others
* A single user on Github can be a member of many organizations
* To create an organization, navigate to the top right of your profile and click the "+" button then click "New organization" and fill out the profile information
* You can then invite other users to join your organization
* One specific feature of Github Organizations is Teams

### Teams
* Teams make it easier to setup and organize access to repositories
* Teams allow you to share a set of permissions with groups of like people who collaborate together on the same project(s)
* make it easy to set up and organize access to repositories 
* Navigate to the Teams tab for your organization click "Create a new team"
* You can then add members to the team
* The Team can be visible or secret, but keeping it visible allows team mentions
* In an organization, repositories are owned by the organization and not individual users
* To give a team access to a repository click settings for the respository, click "collatorators and teams" in options
* If you want to give an individual access, add a collaborator; if you want to give a team access, add a team. You can do both
* Adding teams is good for adding functional groupings of people who share a skillset or set of objectives, but adding an individual collaborator may be done if you are giving a contractor access
* Once you have added a collaborator or team, you must select which permission level they have: Admin, Write, or Read

#### Open a pull request for your team to review
* When opening a pull request for a repo that you and your team work on, it is a good idea to follow any guidelines set out for pull requests, and to make your pull request clear on what you're changing and why you're doing it
* You can also do a team mention, which is similar to an @ mention for individual users, but a team mention notifies every person in your team. This allows you to let everyone know you have submitted a pull request so they can review your code before merging it
* If you want to make any additions to a pull request submitted by someone else, first you need to clone the repo on your local machine, but you will still be in the master branch. To checkout into the branch created by your colleague, use `$ git branch --remote` to get a listing of all the remote branches, then use `$ git checkout [remoteBranchName]` to go into that branch. You can then make any changes you wish to make, commit those changes, then push your commit(s) to the origin repo for the branch you are working on. Here is the whole process: 

    ```
    $ git clone [repoURL]
    $ git branch --remote 
    $ git checkout [remoteBranchName]
    $ git add .
    $ git commit -m [message]
    $ git push origin [remoteBranchName]
    ```
* Any commits you added and pushed will now be on the branch and visible in the pull request your colleague made

## Github Pages
* Github allows you to host one personal site and unlimited project sites 
* This is an ideal solution for a portfolio site as well as documentation sites for your projects
* To create and host a personal site on Github Pages, create a repository named `yourUserName.github.io`
* Push your files to the repo, and the site should be visible at `https://yourUserName.github.io`
* When creating a project site or hosting a simple client side app or website, the production build should be on the `gh-pages` branch, although these days Github will allow you to host your master branch

## Contributing to open-source
* Open source software is software that is open to be taken and used, as well as modified, by anyone because the license under which the software is published allows for it
* It is important to add an open source license to projects that you want to be open source
* To add a license file, click "add file" and add a file called `LICENSE.MD` and select the license type you want from the dropdown that appears
* MIT is most common, though there are less permissive licenses you can select
* To find an open source project, go to `github.com/explore` or click "explore" in the top nav bar and browse through different projects you think are interesting
* There are other ways to find open source projects: 
    * [Improve the Bus Factor](https://libraries.io/experiments/bus-factor)
    * [Finding ways to contribute to open source on Github](https://docs.github.com/en/github/getting-started-with-github/finding-ways-to-contribute-to-open-source-on-github)
    * [24 Pull Requests](https://24pullrequests.com/)

### Contributing to a project with an Issue
* You may run into a bug in a package that you use, and even though you don't know how to fix it necessarily, you *can* add an Issue on the package repo to make a bug report 
* First, check any existing issues for what you are about to report to make sure you don't duplicate an issue
* Then, click "New Issue" under the Issues tab
* Add a clear title
* In the body of the issue, describe the bug and give a clear set of instructions for reproducing the bug behaviour
* Give the details of your machine that you are using: operating system and version, since the bug might be related to your computer
* List the version of the programming language you are running locally on your machine
* Display the error output you get with markdown so that the error is readable

### Forking an open source project
* Most open source projects have a similar workflow. There is a person/group who maintain the project; people who contribute to the project with code fixes and bug reports; and people who simply make use of the open source software 
* Those who maintain the project are usually the only people who have direct push access to the code base; no one else can directly push up changes to the repo 
* Contributes are able to fork the repo, which clones the repo to their account and they become the owner of that fork
* The contributor can then make changes on the forked version of the repo, and then submit a pull request to the original repo which will be reviewed by a maintainer 
* If the pull request is found to be useful and compliant with any contributing standards, it may be merged with the original repo's master branch by the maintainer

### Contributing to a project with a pull request
* Fork the project, and then clone it to your local machine with `$ git clone [repoName]`
* Create and checkout into a new branch for the changes you are going to make with `$ git checkout -b [branchName]`
* Make your intended changes or additions 
* Add and commit, then push the branch up to the origin repo with `$ git push origin [branchName]`
* You can then open a pull request on your fork, but it will be requesting to merge your changes with the fork's original master branch
* This is how most open source contributions happen
* Some general guidelines: 
    * Add a unit test for your change, as this helps assure the maintainers that your change does exactly what you say it does
    * Include screen shots if the change is visual in nature 
    * Use the style of the project, not your own
    * Reference the Issue number with `#[IssueNumber]` to create a link to the issue your fix is in response to