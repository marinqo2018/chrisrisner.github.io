---
layout: post
title: "Changing Blog Platforms Again"
date: Sun Aug 23 2015 20:16:00
commentsOn: true
status: publish
type: post
published: true
categories: [Blogging]
excerpt: Surprise, I've changed blogging platforms again.
logoUrl: null
keywords: jekyll,blogs,blog framework
filepath: 2015-08-23-Changing-Blog-Platforms-Again.markdown
disqus_identifier: 2015-08-23-Changing-Blog-Platforms-Again
---
A little while back, I wrote about how I moved my blog on to [Azure Websites](http://chrisrisner.com/This-Site-Now-Powered-By-Windows-Azure-Websites!/).  
At the time, I was still running my website off of the custom blog engine I wrote a few years ago, [Veritas](http://chrisrisner.com/Blog-Upgrade-Number-One/).  
I've actually been running off of my own, home built, engine for longer than that but at that point decided to rewrite, clean up, and open source it.
Well, recently, I've been thinking about where to go with my website.  I want to keep blogging, but I don't want to deal with the effort that comes
with running it off of a blog engine that no one else is working on / maintaining and if anything breaks, I need to dive back into how it works
in order to fix it.  I haven't actually run into very many issues over the past few years but it's just not something I want to have to worry about.  

### WordPress

Now a year ago, I reached out to friends in the community to ask what they recommended I use for a new [blog](http://sailingmakai.com/).  I received all manner of suggestions for things like WordPress, SquareSoft, Ghost, and more.  After looking into many options, I had decided to use WordPress.  Whatever option I went with, I wanted to be able to host it on Azure.  Azure supports super [simple deployment of a WordPress web app in Azure](https://azure.microsoft.com/en-us/documentation/articles/web-sites-php-web-site-gallery/) as well as a way to deploy a very [scalable WordPress app](http://azure.microsoft.com/en-us/marketplace/partners/wordpress/scalablewordpress/) into Azure, but there were a couple of issues I had to deal with in order to use WordPress.  

The first is that WordPress is written in PHP.  This isn't, and wasn't, a huge show stopper.  My PHP skills aren't the most amazing right now, but I could work my way through any code changes I might want to make.  So, while I would prefer an ASP.NET solution because I have a lot more experience with programming that, I could deal with PHP.  The second issue is WordPress' reliance on MySQL.  Azure's [MySQL](https://azure.microsoft.com/en-us/documentation/articles/store-php-create-mysql-database/) solution relies upon [ClearDB](https://www.cleardb.com/).  This is fine and works well, but I don't like not having as much control over things and ClearDB is, in my mind, a little too pricey (note that since I set up that blog, ClearDB did create a new database tier named [Titan](https://azure.microsoft.com/blog/2015/06/04/new-titan-db-offering-from-cleardb/) which is priced better for the size you can use.  

At the time, I ended up setting up a [Virtual Machine running a MySQL server](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-mysql-windows-server-2008r2/) which I could use for my WordPress site.  This worked out very well and went very smoothly.  Azure supports a lot of great backup options for the disk used for the VM so I quickly had backups of the disk with my MySQL server and DBs on it.  Additionally, I installed a WordPress plugin which did daily backups of both the site files and the DB.  However, at the end of the day, I'm not a database administrator.  I'm also not someone that knows all about DevOps and can handle any issues that might arise when it comes to a Virtual Machine.  When you add that into the issues I already had with WordPress, I knew that using WordPress would work briefly but wasn't a long term solution.  More so, when I decided to move away from Veritas, I knew WordPress wasn't the right solution.

### Jekyll
      
One of the suggestions back when I originally asked, was [Jekyll](http://jekyllrb.com/).  Jekyll is a static site generator that makes it very simple to create a blog.  Essentially, it takes a collection of HTML, CSS, JS, Markdown, and other files, and converts them into a static site.  Static means that at the end of the day you have a dump of html content that can be served up without relying on a backend server for processing.  So there's no PHP or ASP.NET code behind.  There is no server side logic.  It's just HTML all the way down (ok, with CSS and JS files as well).  The advantages to this are many including that the site can be hosted from anywhere or anything that can serve up HTML content and that it's very quick because there isn't any server side processing (outside serving up those files).  

### GitHub Pages

One of the very cool things about Jekyll is that GitHub supports a very easy way to host a website from a repo.  Essentially, you check in the same files you'd run against Jekyll locally into your GitHub repo, and GitHub will automatically run Jekyll on it to produce the static website.  This can be done for both an org (using a <orgname>.github.io repo) as well as projects (by checking into a **gh-pages** repo).  This means that even if you don't have a hosting account somewhere or want to deploy into a cloud, you can host a website in GitHub pages.  Even more so, you can easily apply a custom domain to your GitHub pages website.  What this all means is that with very little work, someone can host a website, with a custom domain, with no database dependency, on GitHub.  If you know who [Phil Haack](http://haacked.com/about/) is, you may know about how he [moved his website to GitHub pages](http://haacked.com/archive/2013/12/02/dr-jekyll-and-mr-haack/) about a year and a half ago.  Reading through one of his most [recent posts about 404 pages and redirects](http://haacked.com/archive/2015/07/28/github-pages-redirect-handling/) with GitHub pages reminded me of that at what would be the most opportune time.  I had recently had some issues with my WordPress site and wanted to move that to something else, and had been considering the same thing or my personal site.  
	
### Moving to Jekyll

Moving to Jekyll consisted of four steps:
* Picking a theme
* Converting my current content to be "Jekyllized"
* Fixing any misc issues
* Uploading the site to GitHub and redirecting the DNS records

As it turned out, none of this took more than a few evenings time.  Picking a theme was arguably the hardest part and I ended up primarily using [dbtek's dbyll theme](https://github.com/dbtek/dbyll) combined with a few elements from the [greyshade](https://github.com/shashankmehta/greyshade) theme that Phil Haack used.  I did have to make a few additional modifications to support things like Google Ads and the menu structure I wanted and blog entry headers I wanted, but those were pretty easy things to do.  When Phil moved his blog from Subtext to Jekyll, he built a [small program](http://haacked.com/archive/2013/12/02/dr-jekyll-and-mr-haack/) which connected to his database, pulled out the content, used [Pandoc](http://johnmacfarlane.net/pandoc/) to convert the HTML content to markdown, and saved those files locally.  This generated a markdown file for each blog entry which Jekyll could then be run against to create the static site.  In my case, I couldn't use his exact program since my Veritas blog engine's databse design didn't match up with Subtexts.  So I wrote something simlar using Node.JS and the MSSQL module to connect to my DB.  I tested with Pandoc but I ran into an issue with the alignment of images not being respected.  It is possible to create markdown with **img** tags that have alignment, but it doesn't work with Pandoc (as far as I'm aware).  Fortunately, Jekyll works with markdown AND HTML.  So I didn't need to convert all of my entries to markdown but could sae them as HTML files.  So, instead of converting the files, I mearly saved each entry as an HTML file with the appropriate [Front Matter](http://jekyllrb.com/docs/frontmatter/) in each file.   

Once that was done, there were only a few minor issues to deal with.  Most things had to do with mixing the two themes I liked together.  One of the advantages of moving from my own engine to Jekyll (or anything else) was that instead of building out my own design that was responsive and supported mobile viewing, I would just use one someone else had already made.  Most of this worked fine though I did have to make a few minor modifications to ensure things looked the way I wanted.  Outside of that, the only other issue I had to deal with was that early on, the image and linked content I had was automatically put into a directory within my site (by Windows Live Writer).  I knew this would be an issue early on and had planned to move all of those files up to Azure Blog Storage where I started hosting all of my files a while back.  However, it ended up being much easier to just dump the files into the same directory path within the site.  I'm hoping to still move those files but this will work for now.

Lastly, I needed to push the files up.  This was as easy as adding all of the files, commiting them, and pushing them to GitHub.  After that, I needed to drop a **CNAME** file into to the root folder to point to **chrisrisner.com**.  Then with some simple DNS changes at my registrar (I added **A** records since my registrar doesn't support the **ALIAS** record that GitHub prefers and suggests).  Oddly, when I moved over my WordPress site, it took the full day for my requests to get to the GitHub site.  When I moved ChrisRisner.com over, it instantly started going to GitHub.  

### What's Next?

I just made this change and it has worked out well so far.  We'll see within a few weeks if things are still working out well.  I'm going to leave the previous, Veritas powered, site up and running on Azure just in case I decide to switch back over.  Provided I don't do that, I'm planning to actually deploy the same Jekyll powered site on Azure and then potentially using [Traffic Manager](http://azure.microsoft.com/en-us/services/traffic-manager/) to sacle across both Azure and GitHub, and maybe across some geographical regions as well.  