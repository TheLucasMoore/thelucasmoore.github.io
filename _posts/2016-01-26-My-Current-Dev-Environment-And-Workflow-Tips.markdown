---
layout: post
title: My Current Dev Environment and Workflow
author: Lucas Moore
---

I enter full-time-bootcamp-mode in less than two weeks. I'm learning everything I can about optimizing my work flow, strengthening specific skills and I'm reading as much about web development as possible. I am building a workshop to practice my new craft in. Here's what it looks like right now. 

### My Development Environment

In this screenshot you can see this very article as I was writing it. Only the text outline is in the image, but the development environment I'm using is what's important. 

![workflow](/assets/workflow.png)

I work with *Firefox*, *Sublime Text 3* and *Terminal* each as a full screen app. I swipe between them with a three fingers. 
This arrangement is the quickest way to code that I've found. I code in Sublime in the center and can swipe left to see changes in the browser or swipe right to access Terminal.

### Workflow Magic with Package Control

I found [this amazing article](https://scotch.io/bar-talk/best-of-sublime-text-3-features-plugins-and-settings) about optimizing [Sublime Text](http://www.sublimetext.com/3) to do such magical things with *Package Control*. I giggled a few times as the reality of Package Control washed over me. It's full of so many amazing shortcuts!

[After installing Package Control](https://packagecontrol.io/installation), each repetitive task that had been part of my development process now has an quicker and more elegant solution. And in way fewer keystrokes. Those saved seconds add up into minutes and hours across a day of work. 

I installed the Package Control script in Sublime text by copying and pasting the code and adding it into the console with <code>Tab + ~</code>. I installed the following packages by hitting <code>Command + Shift + P</code>to open the Command Palette, and typing <code>Install</code>

![package control](/assets/package_control.png)

Then press <code>Enter</code> and search the following packages and install them by hitting enter again.

![install](/assets/install.png)

### Advanced New File ###

# Time Saved: 7 seconds a save, which is about 15 minutes a day if I save files 150 times.

![advanced](/assets/advanced.png)

When making a new file in Sublime, the default requires us to hit <code>Command + S</code> to save the file, to name it, then to save it. It took a few seconds each time I repeated the task, which was often. With *AdvancedNewFile* installed, a menu pops up instantly in the status bar to name and save the new file just created. 

### Git for Sublime

# Time Saved: 30 seconds a commit, which is about 10 minutes a day if I commit to Github 20 times a day. 

Throught the same shortcut process above, I can now use Git right from Sublime Text, to stage, commit and push all my files to Github. 

<code>Command + Shift + P</code>
Type "Quick Commit" to stage all the files and write your commit message. 
<code>Command + Shift + P</code>
Type "Push" to push those changes to Github's remote servers.

### Emmet

# Time Saved: It's hard to estimate, but I'll roughly guess it'll save me oodles of time. 

Emmet is a HTML text-expander. I type a snippet in SublimeText and press <code>Tab</code> to fill in the expanded and formatted code snippet. 

It allows logical commands like writing "link" <code>+ Tab</code> to autocomplete to 

{% highlight HTML %}
<link rel="stylesheet" href="">
{% endhighlight %}

It also has ability to make nested syblings by typing <code>div>ul>li</code> I can generate the code:

{% highlight HTML %}
<div>
	<ul>
		<li>
			
		</li>
	</ul>
</div>
{% endhighlight %}

Those are my favorite three packages for Package Control. I'd appreciate any other work flow tips or your critiques of mine. You can reach out to me on [Twitter](https://twitter.com/thelucasmoore) or my email in the footer below. 

# Prepare the Preparations

The last things I'm doing before the bootcamp starts in polishing up my [Javascript skills on Codecademy](https://www.codecademy.com/en/tracks/javascript) and saving the most useful Web Development articles I find and tagging them carefully in [Evernote](https://evernote.com), for later reference. 


