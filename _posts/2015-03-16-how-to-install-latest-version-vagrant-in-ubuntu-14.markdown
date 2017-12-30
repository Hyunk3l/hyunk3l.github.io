---
layout: post
title:  "How to install latest version of Vagrant in Ubuntu 14"
img: posts/vagrant.jpg
categories: [devops]
tags: [vagrant, ubuntu]
---

First of all go to [Vagrant download page](https://www.vagrantup.com/downloads.html) and download the latest version.

The package will be a .deb file.

Once downloaded, open a terminal and type:

```
sudo su
dpkg -i name-of-the-package-downloaded.deb
apt-get update
```

That's all! Now Latest version of Vagrant should be installed in your OS.