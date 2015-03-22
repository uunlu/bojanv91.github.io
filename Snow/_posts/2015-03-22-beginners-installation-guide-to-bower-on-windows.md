---
layout: post
title: Beginners installation guide to Bower on Windows
---

[Bower](http://www.bower.io/) is a package manger for the web. It makes installing frameworks, libraries and assets easy and straightforward. Automatically resolves package dependencies to other packages. For example, if you install twitter-bootstrap it would automatically install jquery for you, because bootstrap is dependent on jquery. Additionally it gives you version control out of the box for your packages, thus easier installing of updates. So, here is step-by-step guide into installing Bower and prerequisites on Windows. <!--excerpt--> 

Bower installs all packages under a single folder called bower_components, and holds dependency information in a bower.json file (among other project information).

![bower initial folder structure](/images/2015-03-22-beginners-installation-guide-to-bower-on-windows/Screenshot_1.png)

Bower depends on NPM and GIT, so you have to install them first.

> Recommendation: Working with Bower is done via command line tool. So, I recommend installing more advanced command line utility in order to get happier with some neat features, as [Scott Hanselman blogged about Console2](http://www.hanselman.com/blog/Console2ABetterWindowsCommandPrompt.aspx).

# Installing Bower and it's prerequisites

1. Open CMD
2. Install Chocolatey

		@powershell -NoProfile -ExecutionPolicy unrestricted -Command "(iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))) >$null 2>&1" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin

3. Install GIT (or [http://git-scm.com/downloads](http://git-scm.com/downloads))

		choco install git.install 
wait until installation is finished  
		
		setx path "%path%;C:\Program Files (x86)\Git\bin\"

4. Install NodeJS (or [https://nodejs.org/download/](https://nodejs.org/download/))

		choco install nodejs.install
wait until installation is finished

		setx path "%path%;C:\Program Files\nodejs\"
		setx path "%path%;C:\Users\%username%\AppData\Roaming\npm"

5. npm install -g bower
6. Profit!

If everything went fine you should be able to user "bower" command in CMD.


FAQ

1. What is Chocolatey?  
Chocolatey is a Machine Package Manager, somewhat like apt-get, but built with Windows in mind.

2. What is NodeJS?  
Node.js is a platform built on Chrome's JavaScript runtime for easily building fast, scalable network applications.

3. What is NPM (Node Package Manager)?  
NPM is a package manager for JavaScript, and is the default for Node.js

4. What is NuGet?  
NuGet is package manager for Microsoft development platform including .NET

5. What is PATH?  
PATH is an environment variable on Unix-like operating systems, DOS, OS/2, and Microsoft Windows, specifying a set of directories where executable programs are located. In our case, we added the locations of GIT and NPM executables in PATH env variable in order to be able to use them directly in CMD.

6. From where bower downloads packages?  
bower install jquery (from registered packages in bower repository)  
bower install git://github.com/user/package.git (from GitHub)  
bower install user/package (from GitHub - with shorthand syntax)  
  
7. What is the simplest way in order to make my custom package publicly available via Bower?
	- Include bower.json manifest in the root folder
	- Push it to GitHub repository
	- Profit!  


Having more questions, you can reach me out on twitter @bojanv91 


