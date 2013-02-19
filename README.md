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
a revision control system, surely they don't mean I can actually loose history?

    $ git push -f
    Total 0 (delta 0), reused 0 (delta 0)
    To git@github.com:regebro/die-git-die.git
    + 1a16272...dde3237 master -> master (forced update)

OK, so it didn't actually push anything. So why did I need to do this?