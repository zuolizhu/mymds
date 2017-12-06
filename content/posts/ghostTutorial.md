---
title: "ghostTutorial"
date: 2017-11-28T18:30:01-05:00
draft: true
---

Ghost is one of my favorite blog system. This tutorial is written for my future referencing.

---

Basic environment requirements:

- Node.js

- ghost-cli

  - To install it, run `npm install ghost-cli -g`

---
#Installation

After environment setup, create a folder `mkdir heyGhost` then `cd heyGhost`. This folder will be the local workspace for ghost. 

Run `ghost install local`to install ghost on current folder. (This is for development only, not for production.)

At this time, we should be able to access ghost blog at http://localhost:xxxx/

Below are some basic commands:

`ghost stop`

`ghost start`

`ghost ls` to list current running ghost project. (like Docker)

---

###For additional theme development, make sure check the box Public API in ghost admin-panel, `Labs`section.



