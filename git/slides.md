!SLIDE
# What is Git?

!SLIDE center

# DVCS

### Dynamic Version Control System

!SLIDE 

![stupid content tracker](man_git.png)

!SLIDE

# Git is a NOSQL DB?

!SLIDE
# Git is a key/value store.

!SLIDE commandline
# Writing Objects

    $ echo '{"data":[1,2,3]}' | git hash-object -w --stdin
    bb12da17bca409723fd745c9afc209662bcbd4d9

!SLIDE commandline
# Reading Objects

    $ ls .git/objects/*
    .git/objects/bb:
    12da17bca409723fd745c9afc209662bcbd4d9

    $ git cat-file -p bb12da17bca409723fd745c9afc209662bcbd4d9
    {"data":[1,2,3]}

!SLIDE center
![](graph-blob.png)

!SLIDE
# Git is a graph database.

!SLIDE commandline
# Trees

    $ git update-index --add --cacheinfo 100644 \
      1f7a7a472abf3dd9643fd615f6da379c4acb3e3a my-key.json

    $ git write-tree
    d8329fc1cc938780ffdd9f94e0d364e0ea74f579

!SLIDE center
![](graph-tree.png)

!SLIDE commandline
# Commits

    $ echo 'storing some json' | git commit-tree d8329f
    fdf4fc3344e67ab068f836878b6c4951e3b15f3d

!SLIDE center
![](graph-commit.png)

!SLIDE commandline
# Refs

    $ git checkout -b my-new-branch

    $ cat .git/refs/heads/master
    74d193238c5141f1bbf28541457137a82bc95edf

    $ cat .git/refs/heads/my-new-branch
    74d193238c5141f1bbf28541457137a82bc95edf

!SLIDE commandline
# Tags

    $ git tag v1.0

    $ cat .git/refs/tags/v1.0
    74d193238c5141f1bbf28541457137a82bc95edf

!SLIDE center
![simplified git graph](graph-complete.png)

!SLIDE center
![](lightweight-tag-downloads.png)

!SLIDE commandline
# Annotated Tags

    $ git tag -a v1.0 -m "Version 1.0"
    # Stores Tagger / Tagged Date
    # Stores Message

!SLIDE center
![](annotated-tag-downloads.png)

!SLIDE center commandline
# [Gollum](http://github.com/github/gollum)

![](smeagol.jpeg)

    $ gem install grit
    $ gem install gollum

!SLIDE commandline
# Lightweight

    $ cd faraday.wiki
    $ gollum
    == Sinatra/1.0 has taken the stage on 4567 for development with backup from Thin
    >> Thin web server (v1.2.7 codename No Hup)
    >> Maximum connections set to 1024
    >> Listening on 0.0.0.0:4567, CTRL+C to stop

!SLIDE center
![](gollum-web.png)

!SLIDE
# History of Changes

    @@@ ruby
    class Gollum::Page
      def versions(options = {})
        # @path = "docs/home.md"
        # git log master -- docs/home.md
        @wiki.repo.log('master', @path)
      end
    end

!SLIDE center
![](gollum-history.png)

!SLIDE
# Diffs

    @@@ ruby
    class Gollum::Page
      def diff(start, finish)
        # @path = "docs/home.md"
        # git diff sha1 sha2 -- docs/home.md
        @wiki.repo.diff(start, finish, @path)
      end
    end

!SLIDE center
![](gollum-diff.png)

!SLIDE 
# Search With Grep
    @@@ ruby
    class Gollum::Wiki
      def search(query)
        # $ git grep -c FooController
        # app/controller/foo_controller.rb:1
        # test/functional/foo_controller_test.rb:1
        @repo.git.grep({:c => query}, 'master')
      end
    end

!SLIDE bullets
# Git Hooks

* Update caches
* Publish alternative formats (PDF, ATOM, JSON)
* Full-text indexing

!SLIDE bullets
# Git Challenges in Gollum

* Git/Grit limitations
* Scaling for GitHub.com

!SLIDE commandline incremental
# Latest Updates

    $ git diff-tree --name-only master~10 master
    Available-middleware.md
    For-committers.md
    Projects-using-faraday.md

    $ git diff-tree --name-only master~30 master
    fatal: ambiguous argument 'master~30': unknown revision or path not in the working tree.
    Use '--' to separate paths from revisions

!SLIDE
# GitRuby vs Native Git

    @@@ ruby
    # git ls-tree master
    puts grit.ls_tree({}, 'master')
    # 100644 blob SHA  home.md
    # 040000 tree SHA  images

    # git ls-tree master -r
    puts grit.ls_tree({:r => true}, 'master')
    # 100644 blob SHA  home.md
    # 100644 blob SHA  images/send-pull-req.png

!SLIDE
# GitRuby vs Native Git

    @@@ ruby
    # git ls-tree master -r -l
    puts grit.native(:ls_tree, 
      {:l => true, :r => true}, 'master')
    # 100644 blob SHA  2305 home.md
    # 100644 blob SHA 60638 images/send-pull-req.png

!SLIDE center
# A "big" Gollum Wiki
![](big-gollum-wiki.png)

!SLIDE center
# Gollum Wikis Won't Scale
![](wikipedia.png)

!SLIDE center
# Smoke is Grit in the Cloud
![](making-github-fast.png)

!SLIDE bullets
# Smoke is Grit in the Cloud

* Redis maps repos to file servers
* Smoke proxies to file servers
* Git is now a remote database

!SLIDE
# Local != Remote

    @@@ ruby
    sha     = @repo.git.rev_parse(
      {:verify => true}, 'master')
    commit  = @repo.commit(sha)
    tree    = commit.tree
    subtree = tree / 'lib'
    blob    = subtree / 'gollum.rb'
    puts blob.data

!SLIDE center
# Get Git a Wingman!
[![](go_team_venture.png)](http://www.adultswim.com/shows/the-venture-bros/index.html)

!SLIDE 
# Use Simple APIs

    @@@ ruby
    module Gollum
      class GitAccess
        # Public method for getting a tree by ref or sha
        def tree(ref)
          sha = ref_to_sha(ref)
          get_cache(:tree, sha) { tree!(sha) }
        end

        def tree!(sha)
          @repo.tree(sha)
        end
!SLIDE 
# Use Simple APIs

    @@@ ruby
    module Gollum
      class GitAccess

        def tree(ref)
          sha = ref_to_sha(ref)
          get_cache(:tree, sha) { tree!(sha) }
        end
        # Private method for accessing data through Grit
        def tree!(sha)
          @repo.tree(sha)
        end

!SLIDE
# Use Simple APIs
    @@@ ruby
    # Basic Implementation, no caching
    def get_cache(name, key)
      yield
    end

    def get_cache(name, key)
      cache_key = build_key(name, key)
      value     = @cache.get(cache_key)
      if value.nil? && block_given?
        set_cache(name, key, value = yield)
      end
      value
    end

!SLIDE
# Use Simple APIs
    @@@ ruby
    
    def get_cache(name, key)
      yield
    end
    # Simple Memcache version
    def get_cache(name, key)
      cache_key = build_key(name, key)
      value     = @cache.get(cache_key)
      if value.nil? && block_given?
        set_cache(name, key, value = yield)
      end
      value
    end

!SLIDE
# Memcache/Redis for caching

    @@@ ruby
    class Walker
      def tree(sha)
        cache("tree:#{net_id}:#{sha}") do
          raw_tree(sha)
        end
      end
    end

!SLIDE
# Redis for custom sorting

    @@@ ruby
    class GitHub::Gollum::Wiki
      def update_page_recency(name, commit)
        @redis.zadd("wiki:#{repo_id}:updated", 
          commit.committed_date.to_i, 
          name)
      end
    end

!SLIDE commandline incremental
# Redis for custom sorting

    $ redis-cli zrange wiki:1:updated 0 2
    1. contributors.md
    2. help.md
    3. home.md

    $ redis-cli zscore wiki:1:updated
    1280171806

!SLIDE bullets

# Other Possible Wingmen

* Riak (clustered key/value, simple links, search)
* CouchDB (similar replication, versioning)
* VertexDB (graph databases)

!SLIDE center

# BONUS ROUND: Madrox
[![](Madrox-multiple-man.jpeg)](http://en.wikipedia.org/wiki/Jamie_Madrox)
[http://github.com/technoweenie/madrox](http://github.com/technoweenie/madrox)

!SLIDE commandline
# Get Madrox

    $ git clone http://github.com/technoweenie/madrox.git

!SLIDE commandline
# Setup Madrox repo

    $ mkdir madrox-sample
    $ cd madrox-sample
    $ git init
    $ touch README
    $ git add README
    $ git commit -m "initial"
    [master (root-commit) b2bfffe] initial
     0 files changed, 0 insertions(+), 0 deletions(-)
     create mode 100644 README

!SLIDE commandline
# First Tweet

    $ ../madrox/bin/madrox technoweenie \
      --email=rick@github.com --msg="Hi"
    @technoweenie: Hi
    52c3d2dfd81e918d491d5c9895fca33a91427387
    [Wed Oct 20 08:47:34 +0200 2010] @technoweenie: Hi

!SLIDE commandline
# First @mention

    $ ../madrox/bin/madrox atmos --email=corey@github.com \
      --msg="@technoweenie: sup?"
    @atmos: @technoweenie: sup?
    139ca4082351caa5565886cf35b9394440152a5f
    [Wed Oct 20 08:49:13 +0200 2010] @atmos: @technoweenie: sup?

!SLIDE commandline
# First @reply

    $ ../madrox/bin/madrox technoweenie \
      --email=rick@github.com --msg="@atmos: nada"
    @technoweenie: @atmos: nada
    e5a5950c7ff743a636015f1419260f4e9490aef2
    [Wed Oct 20 08:50:05 +0200 2010] @technoweenie: @atmos: nada
    [Wed Oct 20 08:47:34 +0200 2010] @technoweenie: Hi

!SLIDE commandline
# Showing Messages: Madrox

    $ ../madrox/bin/madrox technoweenie
    [Wed Oct 20 08:50:05 +0200 2010] @technoweenie: @atmos: nada
    [Wed Oct 20 08:47:34 +0200 2010] @technoweenie: Hi

!SLIDE commandline
# Showing Messages: Git

    $ git log technoweenie
    commit e5a5950c7ff743a636015f1419260f4e9490aef2
    Author: technoweenie <rick@github.com>
    Date:   Tue Oct 19 23:50:05 2010 -0700

        @atmos: nada

    commit 52c3d2dfd81e918d491d5c9895fca33a91427387
    Author: technoweenie <rick@github.com>
    Date:   Tue Oct 19 23:47:34 2010 -0700

        Hi

    commit b2bfffe5bc9254f2bcaf464e164dd20f858d55a4
    Author: rick <technoweenie@gmail.com>
    Date:   Wed Oct 20 08:47:05 2010 +0200

        initial

!SLIDE commandline
# Merging Timelines

    $ git checkout -b timeline

    $ git merge technoweenie
    Updating b2bfffe..e5a5950
    Fast-forward
     0 files changed, 0 insertions(+), 0 deletions(-)
     delete mode 100644 README

    $ git merge atmos
    Merge made by recursive.

!SLIDE commandline
# Timeline in Madrox

    $ ../madrox/bin/madrox timeline
    [Wed Oct 20 08:50:05 +0200 2010] @technoweenie: @atmos: nada
    [Wed Oct 20 08:49:13 +0200 2010] @atmos: @technoweenie: sup?
    [Wed Oct 20 08:47:34 +0200 2010] @technoweenie: Hi

!SLIDE commandline
# Timeline in Git

    $ git log timeline
    commit c9b3729e8218d3aa76dfb94e382b73970b35d9b3
    Merge: e5a5950 139ca40
    Author: rick <technoweenie@gmail.com>
    Date:   Wed Oct 20 08:53:06 2010 +0200

        Merge branch 'atmos' into timeline

    commit e5a5950c7ff743a636015f1419260f4e9490aef2
    Author: technoweenie <rick@github.com>
    Date:   Tue Oct 19 23:50:05 2010 -0700

        @atmos: nada

    commit 139ca4082351caa5565886cf35b9394440152a5f
    Author: atmos <corey@github.com>
    Date:   Tue Oct 19 23:49:13 2010 -0700

        @technoweenie: sup?

    commit 52c3d2dfd81e918d491d5c9895fca33a91427387
    Author: technoweenie <rick@github.com>
    Date:   Tue Oct 19 23:47:34 2010 -0700

        Hi

    commit b2bfffe5bc9254f2bcaf464e164dd20f858d55a4
    Author: rick <technoweenie@gmail.com>
    Date:   Wed Oct 20 08:47:05 2010 +0200

        initial
