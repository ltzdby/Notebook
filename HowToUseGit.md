# How to use Git
## Before beginning

1. (On Windows) Set up a new user environmental variable "HOME" that directs to the directory that you want to store your repositories in Advanced System Settings.

    (On Linux) Check the configuration file /etc/gitconfig.

2. Execute the commands below:
    $ git init

3. Execute the commands below:

        $ git config --global credential.helper wincred
        $ git config --global user.name "<Your Name>"
        $ git config --global user.email "<email@example.com>"

Three Levels of Configuration Files:

* Global config file:

(On Linux) `/etc/gitconfig`, contains values for every user on the system and all their repositories. If you pass the option `--system` to `git config`, it reads and writes from this file specifically.

(On Windows XP) `C:\Documents and Settings\All Users\Application Data\Git\config`

(On Windows Vista and newer) `C:\ProgramData\Git\config` (This config file can only be changed by `git config -f <file>` as an administrator.)

* User config file:

(On Linux) `~/.gitconfig` or `~/.config/git/config`, specific to your user. You can make Git read and write to this file specifically by passing the `--global` option.

(On Windows) User variable file is in the User Environmental Variable 'HOME' directory (`$HOME\.gitconfig`,`C:\Users\$USER` for most people).

* Repository config file:

`config` file in the Git directory (`.git/config`) of whatever repository you're currently using: Specific to that single repository.

PS: Each level overrides values in the previous level, so values in `.git/config` trump those in `/etc/gitconfig`.

## Basic operations
    $ git init // Create a new repository.
    $ git add <file> // Add a file in the Working Directory to the stage.
    $ git commit -m "<a message here>" // Commit all the operations of files in the stage to repository.
    $ git clone <repository path> // Clone a repository to the local directory.
    $ git status // Show the status of the repository.

## Undoing things
    $ git diff <file> // Show the differences between the file in the Working Directory and the commitment in the repository.
    $ git log [--pretty=oneline] // Show commitment logs from the latest to the earliest. Parameter "--pretty=oneline" means that show one commitment in only one line.
    $ git reset --hard <HEAD[^/~n]/commit_id> // "HEAD" points to the current branch pointer, "HEAD^" points to the commitment before the commitment that the branch pointer that "HEAD" pointer points to points to, "HEAD^^" points to the commitment before the "HEAD^" pointer points to, and so on. "HEAD~n" points to the nth commitment before the commitment that the branch pointer that "HEAD" pointer points to points to. You can also type the commit_id of that commitment directly.
    $ git reflog // If you forgot the commit_id of that version, use this command to view all the commands you have executed.
    $ git checkout -- <file> // Clear the Working Directory. If there's some operations of that file in the stage, the file in the stage will replace the file in the Working Directory. If there's not any operation of that file in the stage, the file in the latest commitment will replace the file in the Working Directory.
    $ git reset HEAD <file> // Clear the stage. Note: Now the stage is clear but the Working Directory will remain the status before.
    $ git rm <file> // Remove a file. You should commit then.
    $ git filter-branch -f --tree-filter 'rm -rf <file>' HEAD // Delete all histories of a file in the repository. You should execute "git push origin [branch] --force" then.

## Remote repository and branches
    $ git push origin <branch name> // Push the latest commitment of a branch to the remote repository.
    $ git branch <branch name> // Create a new branch.
    $ git checkout <branch name> // Switch to a branch. Note: "Switch" means change the pointer "HEAD".
    $ git checkout -b <branch name> // Create and switch to a new branch.
    $ git branch // View all the branches. A "*" will be displayed before the name of the current branch.
    $ git merge <branch name> // Merge a branch into the current branch.
    $ git branch -d <branch name> // Delete a branch.
    $ git branch -D <branch name> // Force to delete a branch.
    $ git log --graph // Show the graph of branches.
    $ git merge --no-ff -m "<a message here>" <branch name> // Merge a branch into the current branch without using "Fast forward" mode, which will create a commitment after that and when delete the original branch logs will remain existed.
    $ git stash // Store the current Working Directory for further use.
    $ git stash list // Show a list of all stored stashes.
    $ git stash <apply/drop/pop> [stash number(like stash@{0})] // Parameter "apply" means recover that stash to the Working Directory without deleting that stash, "drop" means delete that stash, "pop" means recover that stash to the Working Directory and then delete it.
    $ git remote [-v] // Show the status of the remote repository. Parameter "-v" means show details.
    $ git checkout -b <branch name> origin/<branch name> // Clone a branch which is not the "master" branch to the local repository.
    $ git branch --set-upstream-to=origin/<branch name> // Set up a link between the current local branch and a remote branch.
    $ git pull // Get the latest commitment of the current branch in the remote repository and merge it into the local branch.

## Tags
    $ git tag // View all tags of the current branch.
    $ git show <tag name> // Show details of the commitment which has the tag "<tag name>".
    $ git tag <tag content> [commit_id] // Create a tag for a commitment. Parameter "commit_id" is optional. If left empty, this command will create a tag for the latest commitment.
    $ git tag -a <tag name> -m "<a message>" [commit_id] // Create a tag with some messages.
    $ git tag -d <tag name> // Delete a tag.
    $ git push origin <tag name> // Push a tag to the remote repository.
    $ git push origin --tags // Push all tags that have not been pushed to the remote repository.
    $ git push origin :refs/tags/<tag name> // Delete a tag in the remote repository. Note: You must delete the tag in the local repository first.

## Customize Git
    $ git add -f <file> // Force to add a file to the repository.
    $ git check-ignore -v <file> // Check ".gitignore" to find out what's wrong with ".gitignore" and where is the fault.
    $ git config --global alias.<alias> <'original command'> // Create an alias for a command.

## FAQ

* git: Your branch and 'origin/master' have diverged ...

    (Caution: You will lose all your commits since your last push)
    $ git fetch origin
    $ git reset --hard origin/master

## Something interesting:

    $ git config --global alias.last 'log -l'
    $ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
