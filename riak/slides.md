!SLIDE
# Git and Riak 

!SLIDE bullets

# Core Langauge
* Riak is written in Erlang
* Git is written in C

!SLIDE bullets
# Content Type Agnostic

* Riak values can be anything
* Git _Blobs_ can be anything

!SLIDE bullets
# Riak: API

* [HTTP](https://wiki.basho.com/display/RIAK/REST+API)
* [Protocol Buffers](https://wiki.basho.com/display/RIAK/PBC+API)

!SLIDE bullets
# Git: API

* Extensive set of console commands.
* [libgit2](http://github.com/libgit2)

!SLIDE center
# Riak: Web Scale

[![image of riak cluster ring](riak-ring.png)](https://wiki.basho.com/pages/viewpage.action?pageId=1245320)

!SLIDE
# Git: OSS Project Scale

!SLIDE center
# Riak: Replication

![riak data distribution](riak-data-distribution.png)

!SLIDE center
# Git: Lone Hacker

![image of lone hacker](spider-hacking.jpeg)

!SLIDE
# Git: Making Friends

![image of lone hacker](spider-hacking.jpeg)
![image of lone hacker](spider-hacking.jpeg)

!SLIDE commandline
# Riak: Getting a Value

    $ curl http://127.0.0.1:8091/riak/my-bucket/my-key
    ...

!SLIDE commandline incremental
# Git: Getting a Value

    $ git rev-parse master
    3e99e3e55610404b987b0482f8f844eddff98f7b

    $ git cat-file -p 3e99e3e55610404b987b0482f8f844eddff98f7b
    tree ba3f6333228e8f76ed375bd1e9f70635608ef78a
    parent 4c2257a01dfe432b0087c203874ab4be6019f20e
    author rick <technoweenie@gmail.com> 1284420213 -0700
    committer rick <technoweenie@gmail.com> 1284420213 -070

    $ git cat-file -p ba3f6333228e8f76ed375bd1e9f70635608ef78a
    100644 blob 6a003e5ee88ae4a5b03d34529c2ee69b364b12a1	AboutStacks.pdf
    100644 blob 4fa287fe65d2fbd08ae48391495eead0d5430ff7	Home.md
    040000 tree d69a8aaf94576b718cb5ca8e6fcdf3418e1e12bb	images
    100644 blob 5e40c0877058c504203932e5136051cf3cd3519b	rock-out.md

    $ git cat-file -p 5e40c0877058c504203932e5136051cf3cd3519b
    boom

!SLIDE commandline
# Riak: Storing a Value

    $ curl http://127.0.0.1:8091/riak/my-bucket/my-key \
        -H "Content-Type: application/json" -d '{...}'

!SLIDE commandline
# Git: Storing a Value

    $ git hash-object -w my-key.json
    1f7a7a472abf3dd9643fd615f6da379c4acb3e3a

!SLIDE center
![](graph-blob.png)

!SLIDE commandline incremental
# Git: Storing a Value

    $ git update-index --add --cacheinfo 100644 \
      1f7a7a472abf3dd9643fd615f6da379c4acb3e3a my-key.json

    $ git write-tree
    d8329fc1cc938780ffdd9f94e0d364e0ea74f579

!SLIDE center
![](graph-tree.png)

!SLIDE commandline incremental
# Git: Storing a Value

    $ echo 'storing some json' | git commit-tree d8329f
    fdf4fc3344e67ab068f836878b6c4951e3b15f3d

!SLIDE center
![](graph-commit.png)

!SLIDE
# Object Linking

* Git objects naturally link to themselves.
* Commit => Parent Commits, Author, Committer, Tree
* Tree => Blobs, Sub Trees

!SLIDE
# Object Linking

* Riak objects can link to any other object
* Link Walking

!SLIDE commandline
# Git already has search!

    $ git grep Net::HTTP
    lib/faraday/adapter/net_http.rb: Net::HTTP::Proxy(proxy[:uri].host, proxy[:uri].port, proxy[:user], proxy[:password])
    lib/faraday/adapter/net_http.rb: Net::HTTP

!SLIDE
# Riak Search looks pretty sweet

!SLIDE
# Why should you use Git?

* Your objects are text-based documents.
* You care about tracking revisions, history.
* Distributed replication is important.
* You're a Git host :)

!SLIDE
# Why not?

* You don't care about revisions or history.
* Lots of dynamic queries and indexes needed.

!SLIDE
# Git is not a general purpose, web scale, key value store

!SLIDE

Wow, how does GitHub survive?

Lots of Memcache
Lots of BIG file servers

!SLIDE
Git to Riak: "Let's be friends"
