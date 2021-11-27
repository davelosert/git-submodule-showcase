# git-submodule-showcase

A little showcase to show how to work with [git submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules).

This repository has a submodule under `submodules/git-submodule-showcase-submodule` that points to my repository with the same name at <https://github.com/davelosert/git-submodule-showcase-submodule>.

## Cloning the parent for the first time

On default cloning the repository will leave the child under `submodules/git-submodule-showcase-submodule` empty. To fill it, you will have to both `init` and `update` it like the following:

```shell
git clone https://github.com/davelosert/git-submodule-showcase.git
git submodule init
git submodule update
```

### Shorthand

However, there is also a shorthand cloning command that does both commands in one step:

```shell
git clone --recurse-submodules https://github.com/davelosert/git-submodule-showcase.git
```

## Getting Updates for the Submodules

Pulling in the newest changes for the parent repository itself can be done with `git pull`.

However, this will **not** pull the latest changes for the submodules. To achieve this, you have to run another command:

```shell
git submodule update --init --remote 
```

- The `--remote` flag is necessary due to the decentralized nature of git where you have a local copy of the repository. Omitting this flag would only look for local changes - but not contact the GitHub Server.
- The `--init` flag is required in case new submodules were added to the project so that they can be properly initialized. It will simply do nothing if there are no new submodules.

### Making changes stick

Now if you pulled changes from submodules into the parent repository with the command above, you will have to execute some more steps to actually make them 'stick'.

This is due to the fact that the parent repository itself always tracks an explicit commit-sha (the id of the commit) of the submodule in on it's own - so when changing this commit-sha, it will have to be committed within the parent repository like any other change.

You can try it out in this showcase:

1. First check the current version of the submodule by looking into the file [submodules/git-submodule-showcase-submodule/submodulefile.txt](submodules/git-submodule-showcase-submodule/submodulefile.txt). It will say `Revision 2.` in the end.

2. Now let's pull in some updates:

  ```shell
  git submodule update --init --remote 
  git status
  ```

  You will now see that git tells you there are untracked changes in the folder `submodules/git-submodule-showcase-submodule`. Opening [submodules/git-submodule-showcase-submodule/submodulefile.txt](submodules/git-submodule-showcase-submodule/submodulefile.txt) again you will now see it says `Revision 3`.

3. To make those changes stick, you will have to explicitly commit the folder of the submodule - this will adjust the parent's repository commit-sha of the submodule to the newest version:

  ```shell
  git add submodules/git-submodule-showcase-submodule
  git commit -m 'Brought submodule to revision 3'
  ```
  
4. What's left now is to push those changes back to the server so everybody else working in the project won't have to do step 3:

  ```shell
  git push
  ```
  
  *Please note: The push command will probably fail in this scenario as you do not have the permissions to push to my GitHub repository. In a real project where you have the proper access, it would work.*

## More information

There are more caveats when working with submodules, e.g. when you want to do commits in the submodule directly from within the parent repository. There are handled in depth in the official tutorial here: <https://git-scm.com/book/en/v2/Git-Tools-Submodules>
