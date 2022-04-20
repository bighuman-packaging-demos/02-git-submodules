# 02-git-submodules

> _NB:_ This pattern is very common in languages without a package manager, like C and C++.

This is an example of how copy-pasting code into a repo would work. Consider this scenario: a package `dependency` we would like `dependent` to depend on exists at a `git` remote. We can add a `git` submodule pointed at `dependency`'s remote, then possibly set up a workspace to inform `npm` of the dependency tree.

```
~
├── dependency
│   ├── index.js
│   ├── package-lock.json
│   └── package.json
├── dependent
│   ├── index.js
│   ├── package-lock.json
│   ├── package.json
│   └── packages
│       └── dependency@ffffff # <- this directory is a git submodule
└── .gitmodules
```

```sh
$ cd ~/dependent
$ git submodule add "${DEPENDENCY_GIT_REMOTE_URL}" packages/dependency
```

Once the submodule is added, `~/dependent/packages/dependency` will be pinned to a specific commit of `dependency`'s remote (commit hash `ffffff` in the above tree view). To receive updates, the maintainers of `dependent` must update the submodule. For example:

```sh
$ cd ~/dependent
$ git submodule update --recursive --remote
```

Additionally, when cloning `dependent`, contributors must use `git clone`'s `--recurse-submodules` flag, or else the submodule will not be cloned.

```sh
$ git clone --recurse-submodules "${DEPENDENT_GIT_REMOTE_URL}" ~/dependent
```
## Client handoff

Git submodules work by way of two things:

- A file called `.gitmodules` at the repository root
- `.git` directories for each submodule

This makes it extremely easy to complete client handoff &mdash; when that time comes, the `.gitmodules` file and the `.git` directory in each submodule can easily be removed, and there will be no trace of `dependency` ever being a submodule.
