---
layout: post
title: How and Why I Made this Blog
excerpt_separator: <!--excerpt-->
comments: true
author: Lucas Moore
---

This website is all about what I learn as a Web Developer as I complete the [Learn.co Coding Bootcamp](http://learn.co). Here live my lessons, notes & reflections as I move towards a career as a Full-Stack Developer. 
<!--excerpt-->

I believe in learning by doing, so my first lesson is building this very blog, venturing away from the complicated simplicity of Wordpress. I still have my [personal blog](http://thelucasmoore.com) for all things personal, but am writing about Ruby on Rails development here.

### How I Made this Blog

![Jekyll Documentation](/assets/jekyll.png)

This blog is built with Jekyll and hosted on Github Pages. After reading through the documentation for [Jekyll][jk], [Github Pages][gt] and [Markdown][mk], I built this site in about five minutes and then started writing this post. Here's how:

[jk]: http://jekyllrb.com/docs/home/
[gt]: https://pages.github.com/
[mk]: http://daringfireball.net/projects/markdown/basics

# 1. Command Line Web Design

Modern web design uses the command line to move swiftly. One can construct complete sites, like this one, in a few lines of code. That's assuming your development environment is set up, which you can learn in [this great tutorial series][evanto] on Terminal for Web Design.

[evanto]: http://webdesign.tutsplus.com/articles/the-command-line-for-web-design-introduction--cms-23493

# 2. Make a New Jekyll Blog in Terminal

With the Jekyll gem installed, I typed the following into my terminal:

<code>$ jekyll new my_blog_name</code>

This makes a new Jekyll blog, all tidy and ready to go.

<code>$ cd my_blog_name</code>

This brings me into that newly created folder containing the Jekyll site. I also opened up this folder in [Sublime Text](http://www.sublimetext.com/2) to edit it.

<code>$ jekyll serve</code>

This runs the site on localhost, so I can see changes I make at localhost:4000 in my browser.

Lastly, I edited the <code>_config.yml</code> to my own information in Sublime.

# 3. Host it on Github

I also have this folder set up as a repository on [my Github](https://github.com/TheLucasMoore/thelucasmoore.github.io). Any repository that is set as *your-username*.github.io is automatically structured to work on Github Pages. When I update the blog from my computer and push the updates to Github, this website updates. Effortless. 

# 4. Write in [Markdown][mk]

![New Blog Post](/assets/posts.png)

Markdown is a beautifully simple way to write for the web. The syntax took me about 15 minutes to learn, since it's so intuitive. To make a new blog post, I just create a new file in the <code>_posts</code> folder that follows the <code>YEAR-MONTH-DAY-title.markdown</code> naming convention.

# 5. Push to a Static Web Page

Finally, any time I update this site, I push the changes to Github. Jekyll does all the work of compiling the file structure and converting the Markdown to HTML. There is no server database, simply an agile static page.
