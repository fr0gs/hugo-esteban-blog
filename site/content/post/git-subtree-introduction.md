+++
author = "Esteban"
categories = ["git"]
date = 0001-01-01T00:00:00Z
description = "Small introduction for the git subtreee capabilities."
draft = false
image = "/images/2017/08/git_logo.png"
slug = "git-subtree-introduction"
tags = ["git"]
title = "Git subtree introduction"

+++


[**NOTE**: I recovered this post from my [old wordpress blog](https://deixapaso.wordpress.com/)]

We often find ourselves developing projects that depend on other vendor’s libraries or even on our own produced external software components.

Git subtree provides a way to incorporate that external project into another one (normally bigger) by copying it inside the parent one and making it share the parent’s commit history from that moment on.

This is known as system-based approach in development, where you architect your design by taking the different interconnected projects as a whole. That strategy involves tagging, merging and pushing the whole repository constantly. One commit history to rule them all.

How does it work? Imagine we have two projects: the big one (the-backend), and the small one (the-frontend). Former is the main project, being constantly changed and under heavy commit routine. The latter is the mobile web application that consumes the backend’s API. Stable, and only being changed every backend’s major release.

It is therefore interesting to manage both projects independently, with sepparate commit histories but maintaining cohesion of the project itself. One way to do it is well, with git subtrees.

NOTE: this is an extremely simple use case of git subtree just to get the sense of it, any corrections are more than welcome.

```sh
the-backend/ (parent project)
   file1
   file2
 
the-frontend/ (child project)
   item1
   item2
```

Both the-backend/ and the-frontend/; are independent projects. They can be in the same server or in remote servers, only references will change.

```sh
λ mkdir the-backend/
λ touch the-backend/file1 the-backend/file2
 
λ mkdir the-frontend/
λ touch the-frontend/item1 the-frontend/item2
```

We want to add the-frontend/ as a dependence to the-backend/ project but letting it stand as an independent repository.

```sh
λ cd the-backend/
λ git init
λ git add .
λ git commit -a -m "Two first files added to the-backend parent project "
 
λ cd ../the-frontend/
λ git init
λ git add .
λ git commit -a -m "Two first files added to the-frontend child project"
```

Now with both repositories initialized, we add the the-frontend/ repo as a remote repo to the-backend/ project.

```sh
λ (In the-backend/ folder)
λ git remote add frontend-subtree ../the-frontend/
λ git subtree add --prefix=frontend frontend-subtree master
Creates a subtree of the-frontend/ project inside the-backend/ project under the prefix specified (prefix obligatory)
```


Now if we `git log` inside the-backend/ we see a commit message something like: “Add ‘the-frontend/’ from commit <commit>”.
Say that now we want to add some changes in the-frontend/ project outside the-backend/ one.

```sh
λ (In the-frontend/ folder, not the-backend/frontend/)
λ touch item3
λ git add item3 && git commit item3 -m "Item3 added inside the-frontend/ outside project."
λ git log
* c557ce6 - (HEAD, master) Item3 added inside the-frontend/ outside project. (4 seconds ago) <Esteban>
* 45074d3 - Two first files added to the-frontend child project (4 days ago) <Esteban>
```

However this changes are only visible in the-frontend/ project, while in the-backend/frontend/..

```sh
λ ls the-backend/frontend
item1 item2
 
λ git log
* e009438 - (HEAD, master) Add 'the-frontend/' from commit '45074d397c99079acd20cb24e9d8b8830afcf802' (4 days ago) <Esteban>
|\
| * 45074d3 - (frontend-subtree/master) Two first files added to the-frontend child project (4 days ago) <Esteban>
* 3ea1a67 - Two first files added to the-backend parent project (4 days ago) <Esteban>
```

If we want those changes visible in the-backend/frontend/ subtree folder, we have to git pull subtree:

In the-backend/ root folder, above frontend/, otherwise we get a message like 'You need to run this command from the toplevel of the working tree.')

```sh
λ git subtree pull --prefix=frontend/ frontend-subtree master
λ git log
 
* 7dd7677 - (HEAD, master) Merge commit from the parent the-backend/ pulling changes from the the-frontend/ subtree (28 seconds ago) <Esteban>
|\
| * c557ce6 - (frontend-subtree/master) Item3 added inside the-frontend/ outside project. (6 minutes ago) <Esteban>
* | e009438 - Add 'the-frontend/' from commit '45074d397c99079acd20cb24e9d8b8830afcf802' (4 days ago) <Esteban>
|\ \
| |/
| * 45074d3 - Two first files added to the-frontend/ child project (4 days ago) <Esteban>
* 3ea1a67 - Two first files added to the-backend/ parent project (4 days ago) <Esteban>
```

See also how we got into the-backend/ parent folder the local commit from the-frontend/ repository. This may be also avoided adding the –squash option in the git subtree pull command. That option will compress all local commits in one single commit for the subtree pull.

Nonetheless, we might aswell do it inversely. If someone working from the parent the-backend/ folder makes a change to the frontend/ added subtree, and we want to see those changes reflected in the outsider the-frontend/ repository:

(In the-backed/frontend)

```sh
λ touch item4_from_parent
λ git add && git commit item4_from_parent -m "Item4 added from the parent to frontend/ subtree folder"
λ git log
* 4a50c88 - (HEAD, master) Item4 added from the parent to frontend/ subtree folder (9 seconds ago)
( . . . )
λ git subtree push --prefix=frontend/ frontend-subtree new-branch-from-master
```


This will create a new branch in the-frontend/ project with those changes. It is extremely cumbersome to create a new branch in the frontend external project every time a change is made in the subtree from the parent, but git subtree does not allow to overwrite the master branch by default due to potential inconsistent state restrictions.

One alternative to git subtree is git submodules,  but that is topic for another article.

Links:

https://stackoverflow.com/questions/31769820/differences-between-git-submodule-and-subtree
https://stackoverflow.com/questions/769786/vendor-branches-in-git/769941#769941
https://git-scm.com/book/en/v1/Git-Tools-Subtree-Merging
https://developer.atlassian.com/blog/2015/05/the-power-of-git-subtree/

