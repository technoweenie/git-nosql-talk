!SLIDE
# Git and Riak 

!SLIDE

* Riak is written in Erlang
* Git is written in C

!SLIDE
# Riak API

* HTTP, web machine
* Protocol Buffers

!SLIDE
# Git API

* Extensive set of console commands.
* libgit2

!SLIDE
# Content Type Agnostic

* Riak values can be anything
* Git _Blobs_ can be anything

!SLIDE
# Riak

[image of riak cluster ring]

scales to millions of keys, terabytes of data

!SLIDE
# Git

[image of lone hacker]

Designed for smaller repos with (relatively) little data.

!SLIDE commandline
# Riak: Getting a value of a key

Optimized for retrieving a value of a key.

$ curl http://riakserver.com/riak/my-bucket/my-key
...

!SLIDE commandline
# Git: Getting a value of a key

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

    $ git cat-file -p d69a8aaf94576b718cb5ca8e6fcdf3418e1e12bb
    100644 blob 6a003e5ee88ae4a5b03d34529c2ee69b364b12a1	AboutStacks.pdf
    100644 blob 4fa287fe65d2fbd08ae48391495eead0d5430ff7	Home.md
    040000 tree d69a8aaf94576b718cb5ca8e6fcdf3418e1e12bb	images
    100644 blob 5e40c0877058c504203932e5136051cf3cd3519b	rock-out.md

    $ git cat-file -p 5e40c0877058c504203932e5136051cf3cd3519b
    boom

!SLIDE
# Riak

Multiple nodes in a cluster can handle concurrent writes from multiple clients.

!SLIDE 
# Git

A repo handles one write at a time, from one user.

!SLIDE bullets
# Typical Git Commit

* Write file data => get a Blob SHA
* Collect Blob SHAs in a directory => get a Tree SHA
* Combine commit message and root Tree Sha => get a Commit SHA

!SLIDE
# Git conflict resolution

!SLIDE
# Riak conflict resolution

!SLIDE
# Object Linking

* Git objects naturally link to themselves.
* Commit => Parent commits, Author, Committer, Tree
* Tree => Blobs, Trees

!SLIDE
# Object Linking

* Riak objects can link to any other object
* Artist => Album => Track
* Link Walking

!SLIDE

Git already has search!

$ git grep casshern
Home.md:[[images/casshern.jpg]]
Home.md:[[casshern | casshern.jpg]]

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
