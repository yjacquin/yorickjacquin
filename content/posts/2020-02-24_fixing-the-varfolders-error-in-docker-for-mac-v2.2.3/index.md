---
title: "Fixing the var/folders error in Docker for Mac v2.2.3"
author: "Yorick Jacquin"
date: 2020-02-24T11:41:35.744Z
lastmod: 2020-04-26T16:59:26+02:00

description: ""

subtitle: "I recently got my former macbook stolen at a conference and so I had to get a new one from my company and reinstall everything we needed…"

image: "/posts/2020-02-24_fixing-the-varfolders-error-in-docker-for-mac-v2.2.3/images/1.gif" 
images:
 - "/posts/2020-02-24_fixing-the-varfolders-error-in-docker-for-mac-v2.2.3/images/1.gif" 
 - "/posts/2020-02-24_fixing-the-varfolders-error-in-docker-for-mac-v2.2.3/images/2.png" 
 - "/posts/2020-02-24_fixing-the-varfolders-error-in-docker-for-mac-v2.2.3/images/3.png" 
 - "/posts/2020-02-24_fixing-the-varfolders-error-in-docker-for-mac-v2.2.3/images/4.png" 
 - "/posts/2020-02-24_fixing-the-varfolders-error-in-docker-for-mac-v2.2.3/images/5.png" 
 - "/posts/2020-02-24_fixing-the-varfolders-error-in-docker-for-mac-v2.2.3/images/6.png" 


aliases:
    - "/fixing-the-var-folders-error-in-docker-for-mac-v2-2-3-2a40e776132d"
---

I recently got my former MacBook stolen at a conference and so I had to get a new one from my company and reinstall everything we needed for all our repositories.

I know, how sad…




![image](/posts/2020-02-24_fixing-the-varfolders-error-in-docker-for-mac-v2.2.3/images/1.gif)

Me knowing I had to go over setting up everything again



So I go ahead, and install **Docker Desktop for Mac** as I did a year ago until I realized it has a brand new UI! How cool and shiny, you would say!

Except that the file-sharing UI changed and along with it some features disappeared…




![image](/posts/2020-02-24_fixing-the-varfolders-error-in-docker-for-mac-v2.2.3/images/2.png)

Old version of Docker Desktop with editable paths



In the previous version shown above, you could edit the paths to add custom paths you could not access via the finder.

This was useful since MacOs namespaces the `/var` folder with a `/private` folder so your `/var/folders` are symlinked to `/private/var/folders` .

But In order to make it work, docker requires that you share files both from `/var/folders` and `/private/var/folders` but the new UI shown below does not allow us to edit the paths and we have to pick them directly through the Finder.




![image](/posts/2020-02-24_fixing-the-varfolders-error-in-docker-for-mac-v2.2.3/images/3.png)

New Docker Desktop for Mac UI





![image](/posts/2020-02-24_fixing-the-varfolders-error-in-docker-for-mac-v2.2.3/images/4.png)

var folder is inside a private folder



Problem: For the **Finder**, `/var/folders` does not exist and without both `/var/folders` and `/private/var/folders` added to the shared files, you will get this kind of **error** while running `docker-compose up` :
``Error response from daemon: Mounts denied:  
The path /var/folders/lx/d20nhfdfbd26b9cgdwjf3vpr0600gn/T/services-H7326VH is not shared from OS X and is not known to Docker.  
You can configure shared paths from Docker -&gt; Preferences... -&gt; File Sharing.  
See https://docs.docker.com/docker-for-mac/osxfs/#namespaces for more info.``

The solution is to **manually edit** the configuration file for your docker installation located at:
`~/Library/Group\ Containers/group.com.docker/settings.json`

At the top of the file you will see an array that looks like this:
`“filesharingDirectories” : [  
  “\/Users”,  
  “\/Volumes”,  
  “\/private”,  
  “\/tmp”  
],`

You want to append the 2 following lines:
`“\/var\/folders”,  
“\/private\/var\/folders”`

Your array should now look like this (mind the comma after `&#34;\/tmp&#34;` ):
`“filesharingDirectories” : [  
  “\/Users”,  
  “\/Volumes”,  
  “\/private”,  
  “\/tmp”,  
  “\/var\/folders”,  
  “\/private\/var\/folders”  
],`

Save the file and exit. Now restart your Docker by clicking the icon on the top bar:




![image](/posts/2020-02-24_fixing-the-varfolders-error-in-docker-for-mac-v2.2.3/images/5.png)

Click Restart



Wait for it to restart and then go back to _Preferences -&gt;Resources -&gt;File sharing_ and Tada! 🎉 🎉 🎉




![image](/posts/2020-02-24_fixing-the-varfolders-error-in-docker-for-mac-v2.2.3/images/6.png)

You did it! 👏



Now you can go back to your dockerized project, run `docker-compose up` and this error should be gone 😄

### **If this article was helpful to you feel free to 👏 clap 👏 or share it with you coworkers in need!**
