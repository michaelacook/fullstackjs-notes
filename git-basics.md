# Git Basics
- Note: This set of notes assumes basic familiarity with the command line
- Git is software that allows you to keep track of changes to a project over time
- Git is important to be comfortable with, since it is very widely used in software development across many organizations and sectors
- Git allows you to record changes to your project and reference those changes as needed
- Git works with Github to store your code versions online so that they can be accessed anywhere
- Github allows collaboration for teams working on a project together
- Git creates a hidden folder in your project called `.git` that stores all the versions of your code 

## `git init`
- To initialize git in your project, make sure you are in the root folder of your project and run `git init` in the terminal 
- This creates a new empty `.git` folder in your project 
- The command will print out the following message to your terminal: 

```
Initalized empty Git repository in /path/to/project/.git/
```
- To see your git folder in the terminal run `ls -a` 
- To deinitialize a git project (delete the .git folder) run the command `rm -rf .git` in the root folder of your project

## Git Workflow 
- A project that uses git has a cycle of 3 steps that constitute the workflow: 
  - 1. Making changes in the working directory (creating, editing, deleting and organizing files)
    - This is the step where you work on programming tasks 
  - 2. Staging changes 
    - This is the step where you list the changes you are making to the working directory 
  - 3. Committing (or saving) changes to the git repository 
    - This is the step where you tell git to save all the changes you have staged as a new version of your project in the git repository
- Git does not automatically save every change you make as a new version. You have to tell git which changes you want to have saved as a new version by staging those changes, and then committing them to the repository. More on this later

## `git status` 
- Running the `git status` command will tell you the state of the working directory and the staging area 
- This means it will tell you if any files have been modified, added, or deleted, and whether you have any changes currently staged for commit that have not yet been committed
- For instance, when I run `git status` while working on these notes, I get this printed out to the terminal: 

```
michael@michael-Inspiron-7790-AIO:~/Desktop/Projects/backend engineer$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        git-basics.md
        javascript-fundamentals-cheatsheet.md
        operating-systems.md

nothing added to commit but untracked files present (use "git add" to track)
```
- Let's break this down. Git is telling me that I have not made any commits yet, and that I have several untracked files, which means my git repo has no copy of those files yet. Git also tells me at the bottom that there are no changes staged for a commit 
- Notice also that in parentheses git gives me a hint that I can use `git add <file>` to add, or stage, a file to be committed 
- Also notice at the top of the message it says "On branch master". Don't be confused by this. Ignore it for now, branches will be covered in a future set of notes
- `git status` is useful when you've been making many changes and you lose track of what files you've added or made changes to and whether or not you have already staged files for a commit 
- Some text editors like VS Code hint to you which files have been changed but not commited by changing the colour of the file icon in the file navigator

## `git add` 
- After you have made some meaningful change to your working directory (added a file, modified a file, etc), you will want to stage that change or set of changes 
- Staging changes before committing is important (and git forces it on you--you can't go straight to committing!) because it allows you to ***unstage*** a file before committing if you realize you want to undo or modify something before commiting those changes to the repository as a new version
- The git repository in your project will have no knowledge of a file until it is added, or staged for the first time. Files that have never been staged are referred to by git as "untracked", meaning git is not keeping a record of changes to those files
- To add an individual file to the staging area, run `git add ./path/to/filename` in the root folder of your project
- A convenient shortcut to add *all* changes to the working directory to the staging area is to run `git add .`. This command will automatically stage every file that has been changed as well as new files and files deleted
- To remove a file from the staging area **if it is not currently being tracked** run `git rm --cached <file>` to unstage the file
- To unstage a file that is already being tracked, run `git reset HEAD <file>`
- Note: remember that when working in the terminal, you should always reference files with `./` prepended to the path for the file name unless your current working directory is the same as the file's location

## `git diff` 
- `git diff` prints out changes between commits, commit and the working directory, etc
- Documentation is included because this command can be used in multiple ways
- Running the `git diff <filename>` command will print to the terminal the differences between a staged file and any changes made after staging 
- [Atlassian `git diff`](https://www.atlassian.com/git/tutorials/saving-changes/git-diff#:~:text=git%20diff%20is%20a%20multi,%2C%20branches%2C%20files%20and%20more.&text=The%20git%20diff%20command%20is,state%20of%20a%20Git%20repo.)
- [git diff](https://git-scm.com/docs/git-diff)
- Simply running `git diff` without specifying a file will print out **all** differences, rather than for a single file

## `git commit`
- Committing changes that you have staged is the last step in the git project workflow 
- A commit permanently creates a new version of your project inside your project's git repository (`.git` folder)
- Changes to your working directory that you have staged **do not** become a new version in your repository until committed
- To make a commit, run the command `git commit -m [commit message]`
- Each commit must have an associated message describing the commit 
- A good commit message is: 
  - brief (50 characters or less)
  - written in the present tense 
  - must be in quotation marks

## `git log`
- To view a list of previous commits, you can run `git log`
- Viewing previous commits saved to your git repository is useful in a number of situations
- When you run the command, each commit will have a unique SHA, which is a 40 character identifier, as well as the author, date, and commit message

## Recap 
- git is the industry standard software for keeping track of source code versions and it is used by a huge number of software development teams across the IT sector 
- Initialize a git repository in your project with `git init`
- As you make changes to the files in your working directory, you stage them for commit with `git add <file>` or `git add .`
- You commit changes as a new version of your project with `git commit -m [message]`
- You can view past commits with `git log` 
- To view the current status of the working directory and staging area run `git status`
- You can view differences between staged files and new changes with `git diff`
- The basic workflow of a project involves changing the working directory, staging changes, and then committing those changes as a new version of the project
- [Git cheat sheet](https://education.github.com/git-cheat-sheet-education.pdf)

## Bonus 
- The VS Code docs have lots of great information! Check out this short video on git integrations with VS Code that will make using git easier. [Git version control in VS Code](https://code.visualstudio.com/docs/introvideos/versioncontrol)