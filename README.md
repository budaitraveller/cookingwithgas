#Cooking With Gas - a smart contract blog

Site is here: https://budaitraveller.github.io/cookingwithgas/

Uses Artist Theme: A Free Theme for Jekyll Sites (https://github.com/ninapetrop/Artist-Theme) - MIT licensed

##Build

IN THE TOP DIRECTORY

bundle exec jekyll build

##Run

bundle exec jekyll serve --watch --baseurl=

Note: I am screwing with an alternate config file _config_dev.yml to make things easier.

##Push

Just push to gh-pages

Note that it usually takes a few minutes for the site to build when you push to github.

##Using categories

They don't really work the way you would like them to.  You have to add a category html file manually, and add the category to the nav menu manually.  Actually, looks like the whole category setup is pretty busted.  Need to fix it up.

##How to use URLS with Jekyll/gh-pages

Definitely read this - it's the best documentation on jekyll/gh-pages urls anywhere, bar none:

http://downtothewire.io/2015/08/15/configuring-jekyll-for-user-and-project-github-pages/

And here is the most useful documentation page for jekyll itself:

http://jekyllrb.com/docs/configuration/#serve-command-options



