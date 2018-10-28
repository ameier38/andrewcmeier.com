---
layout: post
title:  How to Jekyll
permalink: how-to-jekyll
date:   2018-10-27 11:37:39 -0400
categories: 
  - jekyll
  - windows
  - blog
  - markdown
comments: true
---

This post will walk through the steps necessary to build
a blog using GitHub Pages and Jekyll on Windows.

## Environment set up
Checkout the [Windows Development Environment post]({{ site.baseurl }}{% post_url 2018-08-16-win-dev %})
for a guide on setting up a Windows development environment. The section below covers
only the items necessary for this tutorial.

1. Install [Scoop](https://scoop.sh). Scoop is a command-line installer for Windows

   1. Open PowerShell.

   2. Set the execution policy.
      ```
      > set-executionpolicy remotesigned -s currentuser
      ```
   3. Install scoop.
      ```
      > iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
      ```
   4. Add extras and versions buckets.
      ```
      > scoop add bucket extras
      > scoop add bucket versions
      ```

2. Install [Git](https://git-scm.com/). Git is a version-control system 
for tracking changes in computer files.

   ```
   > scoop install git
   ```

3. Install [Ruby](https://www.ruby-lang.org/en/). Ruby is a dynamic, 
open source programming language with a focus on simplicity and productivity.

   ```
   > scoop install ruby
   ```

4. Install [MSYS2](https://www.msys2.org/). MSYS2 is a software distro 
and building platform for Windows.

   ```
   > scoop install msys2
   > msys2
   ```
   > A console will open. Once it finishes running
   commands just close the console.

5. Install the MSYS2 and MINGW development toolchain.

   ```
   > ridk install 3
   ```

6. Install [Jekyll](https://jekyllrb.com/)
   ```
   > gem install bundler jekyll
   ```

## Create your site repository

1. Create a new repository for your site.
   ```
   > jekyll new my-site
   ```
   > This bootstraps a new Jekyll site in the directory `my-site`.

2. Initialize Git. 
   ```
   > cd my-site
   > git init
   ```

3. Create a new empty repository on GitHub. Navigate to 
`github.com/{username}?tab=repositories` and click the 'New' button.
   ![create-new-repository](/assets/images/how-to-jekyll/create-new-repository.png){:class="shadow"}

4. Copy the repository link.
   ![clone-repository](/assets/images/how-to-jekyll/clone-repository.png){:class="shadow"}

5. Add the repository link as a remote.
   ```
   > git remote add origin {copied repo url}
   ```

6. Add files to track.
   ```
   > git add .
   ```

7. Commit changes.
   ```
   > git commit -m 'initial commit'
   ```

8. Push to GitHub.
   ```
   > git push origin master
   ```

## Host your site on GitHub pages

[GitHub Pages](https://pages.github.com/) allows you to host
your web site from a GitHub repository.

1. Go to the Settings tab for your repository.
   ![github-select-page-rule](/assets/images/how-to-jekyll/github-select-settings.png){:class="shadow"}

2. Scroll down to the GitHub Pages section and set the source to the `master` branch.
   ![github-pages](/assets/images/how-to-jekyll/github-pages.png){:class="shadow"}

3. Add 'github-pages' gem to your Gemfile.
```ruby
group :jekyll_plugins do
  gem "github-pages"
end
```

## Using a custom domain name

1. Purchase a domain name. I recommend [Namecheap](https://namecheap.com) but
you can use plenty of other domain name registrars. I bought `andrewcmeier.com` 
for ~$30 and it renews at ~$20 a year.

2. Create a free [Cloudflare](https://cloudflare.com) account. Cloudflare is used
as the DNS provider so you can route traffic to your site securely using SSL.

3. Add a site to your Cloudflare account.
   ![cloudflare-add-site](/assets/images/how-to-jekyll/cloudflare-add-site.png){:class="shadow"}

4. Get the Cloudflare nameservers for your site.
   ![cloudflare-nameservers](/assets/images/how-to-jekyll/cloudflare-nameservers.png){:class="shadow"}

5. Add the Cloudflare nameservers to Namecheap (or whatever registrar you use).
   ![namecheap-custom-dns](/assets/images/how-to-jekyll/namecheap-custom-dns.png){:class="shadow"}

6. Add CNAME records to Cloudflare. You will add one apex record
(`andrewcmeier.com` in my case) and another `wwww` record (`www.andrewcmeier.com` in my case).
Both of the values will be `{username}.github.io`. So for my site, the value is
`ameier38.github.io`.
   ![cloudflare-cname](/assets/images/how-to-jekyll/cloudflare-cname.png){:class="shadow"}

7. Add a CNAME file in your repository. This is used to register the domain
name with GitHub pages and point to the correct repository.
   ```
   > cd my-site
   > echo "{your domain}.com" > CNAME
   > git add CNAME
   > git commit -m 'added CNAME'
   ```

## Create a post

To create a post, we need to add a new markdown file into the `_posts` directory.
Each post is a standard Markdown file which uses the 
[kramdown syntax](https://kramdown.gettalong.org/syntax.html). Each post should
have a 'Front Matter' section at the top which contains the Jekyll options
formatted in YAML. Below is the 'Front Matter' for this post.
```yaml
---
layout: post
title:  How to Jekyll
permalink: how-to-jekyll
date:   2018-10-27 11:37:39 -0400
categories: 
  - jekyll
  - windows
  - blog
  - markdown
comments: true
---
```
> Learn more about 'Front Matter' on the [Jekyll website](https://jekyllrb.com/docs/front-matter/).

> Check out [this tutorial](https://poanchen.github.io/blog/2017/07/27/how-to-add-disqus-to-your-jekyll-site)
to find out more about adding [Disqus](https://disqus.com/) to your blog.

Then the rest of the file is your blog post content.

## Check your site

Before pushing your changes to GitHub, you should check your site locally. Run
the Jekyll site locally using the following command from the root of your repository.
```
> jekyll serve
```

Then navigate to your site at `http://localhost:4000`.

## Deploy your site

Once the site looks good, then we can deploy our site by pushing
the latest changes to GitHub.
```
> git push origin master 
```

Then navigate to your custom domain name and check that it was
deployed successfully.

## Summary
In this post we covered:
- [Setting up our Jekyll environment on Windows](#environment-set-up)
- [Creating our site repository](#create-your-site-repository)
- [Hosting the site on GitHub pages](#host-your-site-on-github-pages)
- [Using a custom domain name with Cloudflare](#using-a-custom-domain-name)
- [Creating a post](#create-a-post)
- [Running a local server](#check-your-site)
- [Deploy site to GitHub](#deploy-your-site)

All the code for this site is located on [my GitHub repo](https://github.com/ameier38/andrewcmeier.com)
if you want to see more details. Let me know if you have any questions or find any issues in
the comments below. Thanks!

## Additional Resources
- [Jekyll Homepage](https://jekyllrb.com/)
- [Setting up GitHub Pages with Jekyll](http://www.stephaniehicks.com/githubPages_tutorial/pages/githubpages-jekyll.html)
- [Secure and fast GitHub Pages with CloudFlare](https://blog.cloudflare.com/secure-and-fast-github-pages-with-cloudflare/)

{% include disqus.html %}
