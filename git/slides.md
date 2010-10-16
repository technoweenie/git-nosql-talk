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

!SLIDE commandline incremental
# Writing Objects

    $ echo '{"data":[1,2,3]}' | git hash-object -w --stdin
    bb12da17bca409723fd745c9afc209662bcbd4d9


!SLIDE commandline incremental
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

!SLIDE commandline incremental
# Trees

    $ git update-index --add --cacheinfo 100644 \
      1f7a7a472abf3dd9643fd615f6da379c4acb3e3a my-key.json

    $ git write-tree
    d8329fc1cc938780ffdd9f94e0d364e0ea74f579

!SLIDE center
![](graph-tree.png)

!SLIDE commandline incremental
# Commits

    $ echo 'storing some json' | git commit-tree d8329f
    fdf4fc3344e67ab068f836878b6c4951e3b15f3d

!SLIDE center
![](graph-commit.png)

!SLIDE commandline incremental
# Refs

    $ git checkout -b my-new-branch

    $ cat .git/refs/heads/master
    74d193238c5141f1bbf28541457137a82bc95edf

    $ cat .git/refs/heads/my-new-branch
    74d193238c5141f1bbf28541457137a82bc95edf

!SLIDE center
![simplified git graph](graph-complete.png)