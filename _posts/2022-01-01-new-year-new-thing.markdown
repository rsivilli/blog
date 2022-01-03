---
title:  "New Year, New Thing"
date:   2022-01-01 09:13:56 -0700
categories: smallprojects jekyll
excerpt: 
toc: true
---

# Some kind of summary blurb (does meta data support?)
# Starting the new year
Welcome to 2022 and to the B<sup>3</sup> blog. (actually, I guess it would be B<sup>4</sup> as the Building, Brewing and Brooding Blog). For this first post, I figured a sort of 'hello world' would be a good starting point as the point of this blog is, in part for me to try new things and document the process

# Background and Info

## What you will have by the end of this:
- local docker image that runs and serves your blog as you develop
- an initial skeleton for your blog
- a github pages hosted instance of your blog

## What you may not have by the end:
- an in-depth understanding of jekyll. Sorry, this blog is a capture of a collection of things, one of which is my exploration into new things and one of those things happens to be this framework. 
- things like integrations with google analytics, ads, etc. Planned for a later post
- A high performance hosting. There will be a future post to cover migrating to a cloud service provider and talking about the performance improvements. Github pages is free for all public repos and is a good way to cut your teeth. 

## What is this 2010? Why a blog?

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
{% highlight console %}

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

## Hosting on github pages














# Additional Reading and References
- Information on jekyll docker image [link](https://github.com/envygeeks/jekyll-docker/blob/master/README.md)
- Jekyll site for further investigation [link](https://jekyllrb.com/)
- 



