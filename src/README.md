# Forked from sveltejs/kit

The trend of using monorepos makes a lot of things easier to manage. However, when you want to fork a single package inside a monorepo, you'll have to chose one of two options:

- Fork the entire monorepo (meaning you get all those extra boilerplate you don't really care about)
- Manually copying the package files into a new git repo (meaning you'll loose all git history and have a lot of work to do when there's a new version of your base package)

The good news: There's a solution for this! And it's actually built in to git.

## `git subtree`

One of the lesser-known (and vaguely documented) features of git is `subtree`. It's intended for this purpose, working as a great alternative to the criticized `submodules`.
There are very few resources about using this in practice, so here's a guide for this specific use case.

## Set up

In this example we want to fork `adapter-static` out of the `sveltejs/kit` monorepo.

### Setting up the monorepo

1. Start out by cloning the repo: `git clone git@github.com:sveltejs/kit.git /sveltekit`.
2. `cd sveltekit`
3. Check out the branch/version tag you want to fork from: `git checkout version-3`
4. Create a new branch representing just the package we want (react-scripts): `git subtree split -P packages/adapter-static -b svelte-version-3`

### Add to your repo

1. Change into your repository: `cd phantom-adapter`
2. Add the monorepo fork as a new remote: `git remote add adapter-static-source ../sveltekit`
3. Add react-scripts to your project (in the directory you specify): `git subtree add -P src/phantom-adapter adapter-static-source svelte-version-3 --squash`

### Updating the package

1. Create a new branch representing the new version (see step 4 of Setting up the monorepo), e.g. `git checkout main && git subtree split -P packages/adapter-static -b svelte-adapter-static-main`.
2. In your own repo, use `git subtree pull` to merge the changes: `git subtree pull -P src/phantom-adapter adapter-static-source svelte-adapter-static-main --squash`

## Using `--squash` or not?

When we add `--squash` to the command (as suggested), it means that all the commits done to the original package will be squashed into a single commit when merging into your own repo.
This means that your `git log` will only show whatever commits you've made in the repo, a squash commit showing what commits where squashed into one + a merge commit whenever you merge.
