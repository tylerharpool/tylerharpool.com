+++
title = "Deploying Zola to Netlify"
date = 2021-06-08
+++

I've decided to build my website using [Zola](https://www.getzola.org/). Zola  is a [static site generator](https://www.cloudflare.com/learning/performance/static-site-generator/) written in [Rust](https://www.rust-lang.org/). My first foray into SSG was [Gatsby](https://www.gatsbyjs.com/). It came out about the sametime [GraphQL](https://en.wikipedia.org/wiki/GraphQL) did. Before Gatsby, GraphQL was this data fetching layer written by [Facebook](https://en.wikipedia.org/wiki/Facebook) for solving problems like overfetching and growing pains of version api's. Nothing like solving problems a blog would never have, but nevertheless I bought in and started building. Gatsby was using GraphQL in novel new ways and that was really exciting. We could query our site metadata or site configurations and create fast websites that were pre-built templates in a few minutes. It was shiny and new and most importantly it wasn't [Wordpress](https://en.wikipedia.org/wiki/WordPress). At some point, we all have to come to the conclusion that Gatsby is beautiful, but a bit [over the top](https://twitter.com/tesseralis/status/1401294359568343045?s=20) for a blog. I want something fast and simple. I'm learning Rust so Zola was the natural next step and fit.

## Deploying with Netlify
My old Gatsby site was deployed using [Netlify](https://www.netlify.com), so I wanted to do the same for my new website built with [Zola](https://www.getzola.org/). Zola comes with [instructions](https://www.getzola.org/documentation/deployment/netlify/) already for Netlify. You can also deploy using various other services you should check out [here](https://www.getzola.org/documentation/deployment/overview/).  

* [Netlify](https://www.getzola.org/documentation/deployment/netlify/)
* [GitHub Pages](https://www.getzola.org/documentation/deployment/github-pages/)
* [GitLab Pages](https://www.getzola.org/documentation/deployment/gitlab-pages/)
* [Vercel](https://www.getzola.org/documentation/deployment/vercel/)
* [Cloudflare Pages](https://www.getzola.org/documentation/deployment/cloudflare-pages/) 

I found the instructions on Zola to be a little off. They make a reference to an image selection that as far as I can see doesn't exist. This screenshot shows what worked for this site.
![14eba09adfae97ab2e02b53f1a5ee900.png](/_resources/ebd807d073a643fabde6be92d18e1e2f.png)

We can also create a netlify.toml file in our root directory that Netlify will use when we push changes to our code.

```
[build]
# This assumes that the Zola site is in a docs folder. If it isn't, you don't need
# to have a `base` variable but you do need the `publish` and `command` variables.
#base    = "docs"
publish = "public"
command = "zola build"

[build.environment]
# Set the version name that you want to use and Netlify will automatically use it.
ZOLA_VERSION = "0.13.0"

# The magic for deploying previews of branches.
# We need to override the base url with whatever url Netlify assigns to our
# preview site.  We do this using the Netlify environment variable
# `$DEPLOY_PRIME_URL`.

[context.deploy-preview]
command = "zola build --base-url $DEPLOY_PRIME_URL"
```

## My Zola Netlify build fails
I used the [anpu theme](https://github.com/zbrox/anpu-zola-theme) for Zola. Unfortunately, as a result Netlify complained that it couldn't checkout the submodule. I needed to add a .gitmodule file to fix the build.  
![c871a52d029a817dbd015046c5c8d4d7.png](/_resources/32fe30d599974f3b994f6a3ba13b8bc5.png)

To fix it we just need to add a .gitmodules file to our root.

```
[submodule "themes/anpu"] 
  path = themes/anpu
  url = https://github.com/zbrox/anpu-zola-theme.git
```

![29f41e08360a69d51281df163721461a.png](/_resources/5921026b72124a2f9f4fd3b06d5c27a8.png)
![6474826c6b97ecb791bb3b09cd6c77d7.png](/_resources/3268d8c1490c43e19d1ba6d084e71f5f.png)

Build success!