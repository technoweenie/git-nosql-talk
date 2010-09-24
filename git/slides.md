!SLIDE
# What is Git?

!SLIDE bullets

# Git for Source Control

* Each repository contains the full history.
* It's fast and efficient.

!SLIDE 

![gitx](gitx.png)

!SLIDE bullets
# Git for Collaboration

* Supports parallel branches for development.
* Distribute changes with other remote repositories.

!SLIDE

[![git is a distributed source control tool](pullrequest.png)](http://github.com/sinatra/sinatra/pull/56)

!SLIDE

![stupid content tracker](man_git.png)

!SLIDE bullets
# Git as a Key/Value store!

* Key = SHA
* Value = Commit, Directory Tree, File, etc

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

!SLIDE commandline incremental
# Git References

    $ git rev-parse master
    39e1924cbcf9a59a174d6f13606472a33e4a3f0f

    $ git cat-file -p 39e1924cbcf9a59a174d6f13606472a33e4a3f0f
    tree e130afff21b6b084e3f39be0888c614965e3eb07
    parent 038aa66f94c2ce5a244d465935e91cb58d723724
    author rick <technoweenie@gmail.com> 1284509029 -0700
    committer rick <technoweenie@gmail.com> 1284509044 -0700

    my commit message

!SLIDE center

![simplified git graph](graph-complete.png)