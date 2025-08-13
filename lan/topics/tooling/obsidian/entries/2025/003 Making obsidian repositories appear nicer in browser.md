---
status: todo
---

# 1 Objective

As is, repositories will not load image,s embedded links nor internal links. So it makes the obsidian view diverge a lot from the web view. Since we provide URL links to notes, it would be good for the views to be more compatible.

# 2 Journal

## 2.1 Outstanding issues

- [ ] Internal links do not currently work. They are arguably harder to view now because they correctly get obscured under a title name, but are simply not clickable.
- [ ] `[[#^1]]` internal links of this form do not work either.
- [ ] `---\n#Header` lines break the exporter.

---

2025-08-10 Wk 32 Sun - 16:13

Using [gh zoni/obsidian-export](https://github.com/zoni/obsidian-export) we are able to support internal links and images a little more. 

Since this requires full export and change of the markdowns, we created a `webview` branch to host the export. It changes all the files there. The usage is just `obsidian-export from/obsidian/path to/exported/obsidian/path`.

In our git clone convention, we added a `branches/` folder to the git host username/org. So for example `~/src/cloned/gh/delta-domain-rnd/branches/delta-trace@webview`. This is a folder where `delta-domain-rnd/delta-trace` is cloned, but will primarily be used as the location on the `webview` branch. We export to this. 

This can be treated like a detached branch, and we can force push to it not to fill git history with syncing commits. Since it's a computed branch, there shouldn't be loss of information this way.

So, let's say I want to create this for `dism-exe/dism-exe-notes`:

For the first time,

```sh
mkdir -p ~/src/cloned/gh/dism-exe/branches/
cd ~/src/cloned/gh/dism-exe/branches/
git clone git@github.com:dism-exe/dism-exe-notes.git
mv dism-exe-notes dism-exe-notes@webview
cd dism-exe-notes@webview
git checkout -b webview
rm -rf *
obsidian-export ~/src/cloned/gh/dism-exe/dism-exe-notes/ ~/src/cloned/gh/dism-exe/branches/dism-exe-notes@webview
git add .
git commit -m "webview sync"
git push origin webview
```

For subsequent times,

```sh
obsidian-export ~/src/cloned/gh/dism-exe/dism-exe-notes/ ~/src/cloned/gh/dism-exe/branches/dism-exe-notes@webview
git add .
git commit --amend .
git push origin webview --force
```

Here is the force push sync automation:

```bash
#!/bin/bash

force_push() {
		base_location=$1
		repo_name=$2

		pushd "$base_location/branches/$repo_name@webview" || exit 1
		git add . || exit 1
		git commit --amend . -m "webview sync" || exit 1
		git push origin webview --force || exit 1
		popd || exit 1
}

force_push ~/src/cloned/gh/dism-exe dism-exe-notes
```

Although this would gradually get out of sync with main, and that's not really ideal. A computed branch should simply be main + computed commit, each time. This way there's no spamming sync commits, and also not being  behind the main branch.

This version of the script will always be latest with the pulling branch, and always have a tailing `webview sync` commit:

```sh
#!/bin/bash

force_push() {
		base_location=$1
		repo_name=$2
		pull_branch=$3

		pushd "$base_location/branches/$repo_name@webview" || exit 1
	    git checkout $pull_branch || exit 1
	    git branch -D webview || exit 1
	    git pull origin $pull_branch || exit 1
	    git checkout -b webview || exit 1
	    obsidian-export "$base_location/$repo_name" "$base_location/branches/$repo_name@webview" || exit 1
		git add . || exit 1
		git commit -m "webview sync" || exit 1
		git push origin webview --force || exit 1
		popd || exit 1
}

force_push ~/src/cloned/gh/dism-exe dism-exe-notes main || exit 1
```

^ebe248

This will always create a new remote `web_view` branch with the same content as `pull_branch` but with an extra commit "webview sync". 

# 3 Tasks

# 4 Issues

# 5 HowTos

# 6 Investigations

# 7 Ideas

# 8 Side Notes
## 8.1 LaTeX double dollar sign viewing discrepancies

These will not render in the browser:

```
$$
y_i \equiv \frac{e^{x_i}}{\sum^n_{j=1}{e^{x_j}}}
$$
^softmax-eq
```

```
$$

{equation}

$$
```

![[Pasted image 20250811035706.png]]

# 9 External Links

# 10 References