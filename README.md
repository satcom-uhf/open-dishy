# DIY dishy.starlink.com

Reimplement the web interface and functionality removed from Starlink User Terminals in early 2024.

## Overview

Use a stand-alone web-server to provide the traditional web-based "dishy" interface. 

## Installation

### 0. Prerequisites

  1. web-server must have direct route to 192.168.100.1 (the User Terminal)
  2. firewall must allow connections to/from TCP port 9201

### 1. Web server

Included are simple configuration files for popular web servers. These should be installed and enabled according to the web-server's documentation. Server-specific instructions may be found in README.md files in the sub-directories here. Currently supported: nginx apache2 lighttpd

### 2. Hostnames

To avoid hard-coded IP addresses in the Javascript code as was the case with the Starlink implementation we need to locally map:

  router.starlink.com (the Wifi router - optional)
  dishy.starlink.com  (the web server)

In most cases router.starlink.com should point to 192.168.1.1

Because local networks have a myriad of ways to configure local DNS names it is beyond the scope of this document to provide specific instructions. A simple configuration on the local host only would be to add these entries to `/etc/hosts` (on GNU/Linux and MacOS). If the web server is on 10.0.0.1 we might have:
```
192.168.1.1 router.starlink.com
10.0.0.1 dishy.starlink.com
```

### 3. Usage

On a client that can correctly resolve the hostname dishy.starlink.com to the IP address of the web-server simply visit:

http://dishy.starlink.com

## How it Works

Originally the User Terminal (a.k.a. dishy.starlink.com on 192.168.100.1) served a large HTML file and associated fonts, images, and Javascript. In early 2024 the content of the HTML file was mostly removed, leaving only a Starlink logo. Investigations showed that the supporting Javascript, fonts, and images were still being served. The original HTML was recovered from a saved web page from 2023. The page included embedded fonts and images which have been extracted.

The HTML and Javascript was originally minified to save space. That has been reversed so the code can be read and edited easily.

There are two Javascript files: api.bundle.web.js and app.bundle.web.js. There are two copies of each file; the saved 2023 and latest 2024 versions. It turns out app.bundle.web.js hasn't changed; api.bundle.web.js doubled in size but - strangely - when deminified (using Firefox developer tools to Pretty-print it) the files are almost identical in both size and content - excepting different variable names due to minification.

Originally app.bundle.web.js had hard-coded IP addresses; these have been replaced with hostnames (192.168.100.1 -> dishy.starlink.com, 192.168.1.1 -> router.starlink.com) so no code edits are required to make them work - as long as those hostnames are defined and will resolve locally.

The NETWORK STATISTICS and SPEED (test) functionality depends on the Starlink Wifi router being reachable from the browser.

The web-server has to proxy requests to 192.168.100.1 TCP port 9201 (the User Terminal) to allow generic Remote Procedure Call (gRPC) queries to the User Terminal to be accepted by the U.T.