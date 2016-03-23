---
layout: post
title: "Git Push Select Commit to Production"
date:   2015-04-10 18:27:00
meta: "A hot fix pushed to production will be needed. Sometime."
categories: blog
tags: misc
---
The need will arise to push a hot fix into production.  It just will.  When keeping code changes and commits small and isolated (and merging often), the available options greatly increase. If the hot fix is isolated in a single commit, then updating is easy.  Easier said than done, but that is what I strive for.

When the need arose recently, leveraging `git` to push an isolated commit of the necessary changes did the trick nicely.

    $ git checkout production
	$ git cherry-pick COMMIT_SHA1
	$ git checkout master
	$ git merge production

Basically, use the `cherry-pick` command to commit the change from master into production (while in the production branch).  Then merge back into master to keep things connected.
