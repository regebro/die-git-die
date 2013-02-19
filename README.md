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

    git clone git@github.com:regebro/die-git-die.git die-git-die.pages
    cd die-git-die.pages
    git checkout --orphan gh-pages
    git rm -rf .

So we create some pages to have there. It's HTML.

    echo "<html><body><h1>Die git, die\!</h1></body></html>" > index.html

While doing up these pages, we find some errors in the original branch 
and fix them, which we push and commit.

    cd ../die-git-die
    git commit -a -m"Commit"
    git push 

OK, now we are ready to commit and push the changes in the pages branch:

    $ git add .
    $ git commit -a -m "First pages commit"
    $ git push origin gh-pages

