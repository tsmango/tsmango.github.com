---
layout: post
title: "Transferring a Private Repository on Github"
alias: /post/731804559/transferring-a-private-repository-on-github/index.html
---

### Important Update (08-21-2011)

GitHub now has a native support for transferring the ownership of a repository to another user. Just click into your repository's admin area and scroll down to the Danger Zone&trade;.

<p class="image">
  <img src="http://thomasmango.com/images/2010/06/github-danger-zone.png"/>
</p>

### Original Post (06-24-2010)

I recently had to transfer a private git repository that was being hosted on Github. It turned out to be embarrassingly easy to transfer the repository while keeping the history intact, but I figured I'd document it in case others were wondering.

### What Didn't Really Work

The first thing I tried was a fork. I added the receiver's Github account as a collaborator to the private repository. Then, I forked the project on the receiver's Github account. Afterwards, I *removed* the receiver as a collaborator from the original project and *removed* the original account as a collaborator on the newly forked project (which is automatically added by Github).

More or less, this did the job. The newly forked project was it's own repository and neither repository owner could see the other repository. The only issue was that the new repository still said "forked from {original account}". The receiver wasn't happy about this and to be honest, it was a little crude being that they purchased the codebase.

### The Correct Way

The correct way to do this is basically just as easy.

* Create a new repository on Github under the receiver's account.

* Add someone with access to the original repository as a collaborator to the newly created repository owned by the receiver.

* Duplicate your project directory locally (as someone who has access to both repositories).

	* `cp ./projects/{project_name} ./projects/{project_name}_backup`

* Change into the project and remove the existing remote origin that points at the original repository.

	* `cd projects/{project_name}`

	* `git remote rm origin`

* Add the new remote origin (see Github's excellent [first run screen](http://cl.ly/487aed0f71d395350ca4)) that points to the newly created repository owned by the receiver.

	* `git remote add origin git@github.com:{receiver}/{repository_name}.git`

* Push to the new repository.

	* `git push origin master`

* Delete your local copy pointing to the new repository.

	* `cd ..`

	* `rm -rf ./projects/{project_name}`

	* `mv ./projects/{prject_name}_backup ./projects/{project_name}`

This isn't rocket science, but I figured it would help someone.