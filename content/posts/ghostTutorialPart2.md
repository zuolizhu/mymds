---
title: "GhostTutorialPart2"
date: 2017-12-05T20:23:05-05:00
draft: true
---

This is the 2nd part of ghost blog tutorial. Last tutorial I setup the environment and bound the very basic content within the `index.hbs` and `post.hbs`. This part will focus on styling.

---

## Add Bulma CSS

### Before add it, wrap all the content into a default container

---



1. Create a new file called `default.hbs` at theme root folder `Ghost\content\themes\whatever`

2. Inside `index.hbs`, at the very top line, add `{{!< default}}`. 

   ```html
   {{!< default}}
   <header>
       <h1>{{ @blog.title }}</h1>
       <h2>{{ @blog.description }}</h2>
   </header>
   ```

   This makes everything inside `index.hbs` are accessible from `default.hbs`

3. Then inside `default.hbs` file, add the boilerplate code :

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <meta http-equiv="X-UA-Compatible" content="ie=edge">
       <title>{{meta_title}}</title>
       {{ghost_head}}
   </head>
   <body>
       {{{body}}}
       {{ghost_foot}}
   </body>
   </html>
   ```

   Remember to add the `{{meta_title}}` to bind the blog tile, and `{{ghost_head}}` to bind the header. Then`{{{body}}}` to access the content from `index.hbs`. At the bottom before </body> tag, add `{{ghost_foot}}` to access the ghost footer.

   In this way, we can wrap all stuff into a default container.

   We will also add our components later on.

---

## Adding CSS Framework Bulma

Underneath title `<title>{{meta_title}}</title>`, add `bumla CDN`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>{{meta_title}}</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.6.1/css/bulma.css">
    {{ghost_head}}
</head>
<body>
    {{{body}}}
    {{ghost_foot}}
</body>
</html>
```

Then refresh the page. You will see something different.

This also works for `Bootstrap` and `Materializecss`.

---

