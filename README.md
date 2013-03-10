die-git-die
===========

OK, so I've created a new repo on github, and I've cloned it:

    $ git clone  git@github.com:regebro/die-git-die.git

I add my code to the repo and commit and push:

    $ git commit -a -m"Commit."
    $ git push

Now I want to add some github pages for this repo. This
is done by the weird practice of creating a special branch
called gh-pages and then deleting everything on that branch.

Yes, really. Kinda scary. But it shouldn't be a problem,
should it? Because we are using a code revision system,
so even if something gets messed up, it's always possible
to go back to an earlier revision and do it from there.

    $ git clone git@github.com:regebro/die-git-die.git die-git-die.pages
    $ cd die-git-die.pages
    $ git checkout --orphan gh-pages
    $ git rm -rf .

So we create some pages to have there. It's HTML.

    $ echo "<html><body><h1>Die git, die\!</h1></body></html>" > index.html
    $ git add .
    $ git commit -a -m "First pages commit"
    $ git push origin gh-pages

While doing up these pages, we find some errors in the original branch 
and fix them, which we push and commit.

    $ cd ../die-git-die
    $ git commit -a -m"Commit"
    $ git push 

And let's then clean up the HTML in the pages a bit:

    $ cd ../die-git-die
    $ vi index.html
    $ git commit -a -m"Commit"
    $ git push 

    ! [rejected]        master -> master (non-fast-forward)
    error: failed to push some refs to 'git@github.com:regebro/die-git-die.git'
    To prevent you from losing history, non-fast-forward updates were rejected
    Merge the remote changes (e.g. 'git pull') before pushing again.  See the
    'Note about fast-forwards' section of 'git push --help' for details.

Oi! What? Why? There's no conflicts, there can't be any conflicts,
the two branches doesn't even have one single file in common. Why didn't it 
want to push? But, eh, OK, I'll pull.

    $ git pull

    You asked me to pull without telling me which branch you
    want to merge with, and 'branch.gh-pages.merge' in
    your configuration file does not tell me, either. Please
    specify which branch you want to use on the command line and
    try again (e.g. 'git pull <repository> <refspec>').

What!? Doesn't it know I'm on the gh-pages branch?

    $ git checkout gh-pages
    Already on 'gh-pages'

Yes, it does. So why does it ask me with which branch I want to merge!?
That makes no sense. But OK, I'll tell it:

    $ git pull gh-pages
    fatal: 'gh-pages' does not appear to be a git repository
    fatal: The remote end hung up unexpectedly

Aha, OK. 

    $ git pull git@github.com:regebro/die-git-die.git gh-pages
    From github.com:regebro/die-git-die
     * branch            gh-pages   -> FETCH_HEAD
    Already up-to-date.

Huh. This is extra strange, since on another repo I have, it does 
know what to pull from automatically. The error message tells me to
fix my config file, and I notice that the other repos config file
does include those changes, so I'll do that here as well. No idea why
it worked automatically once.

    [branch "gh-pages"]
    	remote = origin
    	merge = refs/heads/gh-pages
    	rebase = true

OK, now git pull works. But it didn't merge anything, so... I'll try to push again:

    $ git push 

    ! [rejected]        master -> master (non-fast-forward)
    error: failed to push some refs to 'git@github.com:regebro/die-git-die.git'
    To prevent you from losing history, non-fast-forward updates were rejected
    Merge the remote changes (e.g. 'git pull') before pushing again.  See the
    'Note about fast-forwards' section of 'git push --help' for details.

No? OK, so I'll force it then. It tells me I can lose history, but this is 
a revision control system, surely they don't mean I can actually lose history?

    $ git push -f
    Counting objects: 6, done.
    Delta compression using up to 2 threads.
    Compressing objects: 100% (4/4), done.
    Writing objects: 100% (6/6), 626 bytes, done.
    Total 6 (delta 0), reused 6 (delta 0)

OOPS! Everything you did on the master branch since you made the hg-pages branch now dissapeared!
Gone! *Poof*!

You can get it back by going over to the other clone and pushing it again, because it
still exists locally. But that's not obvious. But don't do a pull there first!

    $ cd ../die-git-die
    $ git push

And if you pull in that clone first, well, then you are in deeper trouble, 
but you can still get it back because it's still exists
in the local history, although I'm unsure how.

If you delete that local clone though, it's gone forever.


So what is the reason for this? What did I do wrong? Well, the problem seems to be that git by 
default will push "matching" branches, ie all branches that exist both locally and remotely.
However, git will not *pull* matching branches. That means that all your branches except your
current branch will not get updated when you pull. And when you push, your local git will then,
in a fit of amazing stupidity, try to push *your current state* of these other branches onto
your origin.

This is so mindboggingly stupidly inconsistent that it blows my minds. Still. 
Two weeks after I learned this. 

Git 2.0 will change the default to current, it seems, which solves the problem at least partly.
Meanwhile you can add the following addition in your .gitconfig to set a more sane default.

    [push]
    	default = current

I can however not find any way to set the same thing for pull, so what it pulls for default is 
still somewhat unclear to me.

Of course, a less good but at least sane behavior would have been if pull also pulled matching
branches by default. But it doesn't. And it seem like there is no way to make it do so either,
which also boggles the mind. Pull has an "--all" parameter, which seems to make no discernable
difference.

It is in any case completely obvious that pull and push, by default should pull and push the same
branches. All other behavior is just confusing and will cause problems.
