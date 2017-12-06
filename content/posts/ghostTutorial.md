---
title: "ghostTutorial"
date: 2017-11-28T18:30:01-05:00
draft: true
---

Ghost is one of my favorite blog system. This tutorial is talked about setup ghost and build own theme from scratch. This tutorial is written for my future referencing as well.

---

Basic environment requirements:

- Node.js

- ghost-cli

  - To install it, run `npm install ghost-cli -g`

---
# Installation

After environment setup, create a folder `mkdir heyGhost` then `cd heyGhost`. This folder will be the local workspace for ghost. 

Run `ghost install local`to install ghost on current folder. (This is for development only, not for production.)

At this time, we should be able to access ghost blog at http://localhost:xxxx/

Below are some basic commands:

`ghost stop`

`ghost start`

`ghost ls` to list current running ghost project. (like Docker)

---

### For additional theme development, make sure check the box Public API in ghost admin-panel, **Labs** section.

---

## To start make my own theme

1. Make new folder with a theme name(whatever) inside`Ghost\content\themes`
  (_Make sure inside right folder. Not in *__Ghost\current\content\theme__*_)
2. Create three important files : `index.hbs` `post.hbs` and [`package.json`](#package.json)
3. Activate theme
   1. stop running ghost `ghost stop`
   2. `ghost start development`
   3. refresh web browser page, and go to admin panel, **Design** tab, find the theme just created, and click **activate**
4. Start working on theme development now.

---
### package.json

```json
{
    "name": "whatever-theme",
    "description": "A simple theme for ghost blog",
    "version": "0.0.1",
    "engines": {
        "ghost": ">=1.0.0"
    },
    "author": {
        "name": "Zuoli Zhu",
        "email": "zuolizhu5960x@gmail.com"
    },
    "config": {
        "post_per_page": 6
    }
}
```



---

### Bind blog title and description in `index.hbs`

As simple as 

```html
<header>
    <h1>{{ @blog.title }}</h1>
    <h2>{{ @blog.description }}</h2>
</header>
```

### Bind blog posts

**No space between `{{#foreach posts}}` and `{{/foreach}}`**

```html
<header>
    <h1>{{ @blog.title }}</h1>
    <h2>{{ @blog.description }}</h2>
</header>

<main>
    <!-- For each one of the posts, display the title -->
    {{#foreach posts}}
        <h1>{{title}}</h1>
    {{/foreach}}
</main>
```

change the number of posts displayed in the page, simple change the number in package.json

```json
"config": {
        "post_per_page": 6
    }
```

