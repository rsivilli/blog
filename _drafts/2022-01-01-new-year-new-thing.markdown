---
title:  "New Year, New Thing"
date:   2022-01-01 09:13:56 -0700
categories: smallprojects jekyll
excerpt: 
toc: true
type: posts
description: 
---

# Some kind of summary blurb (does meta data support?)
# Starting the new year
Welcome to 2022 and to the B<sup>3</sup> blog. (actually, I guess it would be B<sup>4</sup> as the Building, Brewing and Betterment Blog). For this first post, I figured a sort of 'hello world' would be a good starting point as the point of this blog is, in part for me to try new things and document the process for those that might follow and learn from my mistakes. 

# Background and Info

## What you will have by the end of this:
- local docker image that runs and serves your blog as you develop
- an initial skeleton for your blog
- a github pages hosted instance of your blog

## What you may not have by the end:
- an in-depth understanding of jekyll. Sorry, this blog is a capture of a collection of things, one of which is my exploration into new things and one of those things happens to be this framework. 
- things like integrations with google analytics, ads, etc. Planned for a later post
- A high performance hosting. There will be a future post to cover migrating to a cloud service provider and talking about the performance improvements. Github pages is free for all public repos and is a good way to cut your teeth. 

## Why a blog?



# Let's Start Building!
## Initial Setup
Start by creating your directory:

{% highlight console %}
mkdir myblog
{% endhighlight %}

and move into this directory and initialize as a repo:

{% highlight console %}
cd myblog && git init
{% endhighlight %}

We'll go ahead and create a new branch `gh-pages` and switch to it now
{% highlight console %}
git checkout -b gh-pages
{% endhighlight console %}

Finally, create a new blog skeleton using the jekyll docker image:

{% highlight console %}
docker run --rm --volume $PWD:/srv/jekyll jekyll/jekyll:3.8 jekyll new .
{% endhighlight %}

The fact that we're doing this with docker may be worth a comment. This is, in part to minimize setup and configuration time and allows you to hit the ground running to focus on your blog(assuming that you have docker installed already, if not, go [here](https://www.docker.com/get-started)). For larger teams, using docker images as pre-configured and standalone tools helps to minimize the number of unique dev environments for dependencies and deployments. You may still have cross-os problems, but this just reduces one source of noise. If you are interested in a local install, it is completely doable by following the instructions [here](https://jekyllrb.com/docs/).

For those of you not familiar with docker, or could use the refresher, the breakdown of the command is as follows
- `--rm` is the clean up flag. It ensures the container is destroyed after running.
- `--volume $PWD:/srv/jekyll` is mapping your current execution directory (`~/myblog` if your following this verbatim) to the directory used as the WORKDIR in the image. $PWD is set in most linux environments, but may need to be set if running elsewhere
- `jekyll/jekyll:3.8` image that is used. Very important to note that the version `3.8` is being used instead of `latest`. At time of writing, `latest` did not work out of the box. Check [here](https://github.com/envygeeks/jekyll-docker/blob/master/README.md) for the latest info. 
- `jekyll new .` is actually what is going to the running container and tells jekyll to create a new project at the current directory. Again, this has been mapped to `~/myblog`.


Now at this point, you have a functioning skeleton that can be used for development. You can run a container to interpret changes and serve them locally with the following:

{% highlight console %}
docker run --rm -p [::1]:4000:4000 --volume $PWD:/srv/jekyll jekyll/jekyll:3.8 jekyll serve
{% endhighlight %}

This maps the listening port of jekyll and makes it accessible to `localhost:4000`. Just navigate to your browser and you'll see the current state of your blog! Changes to files in `~/myblog` will be reflected as they are saved. 

It may be worth stating that changes to `Gemfile`, which is a list of your dependencies, and `_config.yml` will require terminating the current container and running the `serve` command again. 

As an aside, the docker image that we're using as opposed to a local install. When jekyll is called on the container, it is actually doing a bunch of things behind the scenes to include insuring that bundler (ruby dependency manger that operates off the Gemfile) is being run to update dependencies. Depending on how you end up running locally you may need to run `bundle exec jekyll build` and serve with `bundle exec jekyll serve` to update your dependencies. 

## Picking a Theme
Frankly, you could take what you have now and start working on hosting. This may be a good enough start for some and would let you start writing posts out the gate. 

That said, I didn't really like the way some of the default layouts were organized and there are some really neat looking themes out [there](https://jekyllthemes.io/). At the time of writing this blog, the one that perk my interest is [Minimal Mistakes](https://jekyllthemes.io/theme/minimal-mistakes). It has a lot of what I was looking for as defaults, but offers a lot of customization if I ever end up going down that road. It is also free :-)

To start, we're going to update our `Gemfile` to look like this:
{% highlight console %}
source "https://rubygems.org"

group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.12"
  
  # adds the theme
  gem "minimal-mistakes-jekyll", "~> 4.24"
  
  # these help us get through some bundling issues
  gem "webrick", "~> 1.7"
  gem "jekyll-include-cache", "~> 0.2.1"
end

# Windows and JRuby does not include zoneinfo files, so bundle the tzinfo-data gem
# and associated library. This comes from the initial laydown
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", "~> 1.2"
  gem "tzinfo-data"
end

# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.1.1", :platforms => [:mingw, :x64_mingw, :mswin]

{% endhighlight console %}

Run the following to update bundled commands

{% highlight console %}
bundle
{% endhighlight %}

and update the `theme` and `plugins` in `_config.yml` to

{% highlight console %}
theme: mmistakes/minimal-mistakes
{% endhighlight %}

and 

{% highlight console %}
plugins:
  - jekyll-feed
  - jekyll-include-cache
{% endhighlight %}

Reminder: these are two attributes in the same file. 



That's it as far as the actual theme change goes. There are some opinionated approaches to directory naming that is a little different than what comes with the Jekyll lay down, so be sure to take a look at Minimal Mistakes [Structure](https://mmistakes.github.io/minimal-mistakes/docs/structure/).

Take a look around at the other themes that are out there an find one that works best for you. Each will have its own variations, but the community is fairly active and supportive if you end up running into anything weird. 

## Hosting on Github Pages

Alright! Now you have the skeleton of a blog and you've spent a little bit of time making it more of a reflection of you. Awesome! 

One problem though, you're in the age old scenario of it only working on your computer. You still need to get that blog out to the world through some kind of hosting. 

Now there are a number of options open to you, some of which will be discussed on a later post (i.e. migrating to aws), but for the sake of this, we're going to talk about hosting via [github pages](https://pages.github.com/). Github pages are neat because, as long as you're site doesn't have huge amounts of traffic and you're ok with the source code of your blog being publicly hosted - its free! That makes it perfect for anyone just getting started.

I'm going to assume some familiarity with github in general, but if you find yourself getting stuck, this process is generally well [documented](https://pages.github.com/). There were a couple of gotchas that I ran into because I was using a theme that required some extra config, but otherwise this is fairly straight forward. You're already one step of the way there by working off the branch `gh-pages` in your local repo. Github pages defaults to that branch for hosting purposes. 

Go ahead and create a new repo on github. For the purposes of this example, let's stick to `myblog` and assume this will be a personal repo for someone with the username `gituser`

Add the new repo as a remote to your current local repo with
{% highlight console %}
git remote add origin https://github.com/gituser/myblog
{% endhighlight %}

Now before we push our files, let's make some changes so things will work as they are deployed out there. 

Update your `Gemfile` to use github pages and support remote themes:

{% highlight console %}
source "https://rubygems.org"

# new
gem "github-pages", "~> 219", group: :jekyll_plugins

# If you have any plugins, put them here!
group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.12"
  gem "minimal-mistakes-jekyll", "~> 4.24"
  # new
  gem "jekyll-remote-theme"
  gem "webrick", "~> 1.7"
  gem "jekyll-include-cache", "~> 0.2.1"
end

# Windows and JRuby does not include zoneinfo files, so bundle the tzinfo-data gem
# and associated library.
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", "~> 1.2"
  gem "tzinfo-data"
end

# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.1.1", :platforms => [:mingw, :x64_mingw, :mswin]
{% endhighlight %}


Also update `_config.yml` to enable the remote theme plugin, remove `theme` and replace it with `remote_theme`:

{% highlight console %}

remote_theme: mmistakes/minimal-mistakes

plugins:
  - jekyll-feed
  - jekyll-remote-theme
  - jekyll-include-cache
{% endhighlight %}



Make sure all changes are currently staged and committed:
{% highlight console %}
git add .
git commit -m "initial commit"
git push
{% endhighlight %}

Go back to the project on github and head to the `Settings` tab. `Pages` is its own option, second from the bottom - go there and make sure that the correct branch (`gh-pages`) and source path (`/(root)`)is selected. 

That's it! There will be a github actions workflow that builds and deploys the page and you should be able to visit at https://gituser.github.io/myblog (assuming the example values above). 

Your first deployment will likely run into a few mistypings or config issues. Keep an eye on the workflows (under `Actions` of the github project) to see what it is reporting. 

With any luck, you'll be up and running in no time! I sincerely hope you found this informative and useful. 

Welcome again to 2022, may it be better than the last few! 






















# Additional Reading and References
- Information on jekyll docker image [link](https://github.com/envygeeks/jekyll-docker/blob/master/README.md)
- Jekyll site for further investigation [link](https://jekyllrb.com/)
- 



