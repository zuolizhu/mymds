---
title: "GhostTutorialPart3"
date: 2017-12-05T23:53:58-05:00
draft: true
---

As CSS framework added, now it is time to break parts into smaller pieces (components) and polish it by adding styling!

---

### Cut big chunk into smaller pieces

1. Create a folder called `partials` (it has to be called partials) inside theme root folder `Ghost\content\themes\whatever`

2. Then create a file component file `header.hbs` inside **partials** folder.

3. Cut header from `index.hbs` into `header.hbs`.

   ```html
   <header>
       <h1>{{ @blog.title }}</h1>
       <h2>{{ @blog.description }}</h2>
   </header>
   ```

4. Then in `default.hbs`, inside `<body></body>`simply add

   `{{> header}}`, 

   ```html
   <body>
       {{> header}}
       {{{body}}}
       {{ghost_foot}}
   </body>
   ```

   ​

   then remember to restart ghost by run `ghost stop` and `ghost start development`. The header and description will comes out again.

---

## Styling header

Modify the original header a little bit,

```html
<header>
    <h1>{{ @blog.title }}</h1>
    <h2>{{ @blog.description }}</h2>
</header>
```

with Bulma header boilerplate: 

```html
<header class="hero is-primary">
    <div class="hero-head">
        <nav class="navbar is-primary">
            <div class="container">
                <!-- For mobile device -->
                <div class="navbar-burger">
                    <span></span>
                    <span></span>
                    <span></span>
                </div>
                <!-- For desktop device -->
                <div class="navbar-menu">
                    <a href="" class="navbar-item">Item 1</a>
                    <a href="" class="navbar-item">Item 2</a>
                    <a href="" class="navbar-item">Item 3</a>
                    <a href="" class="navbar-item">Item 4</a>
                </div>
            </div>
        </nav>
    </div>
    <!-- Blog title goes here. -->
    <div class="hero-body">
        <div class="container">
            <h1 class="title">{{@blog.title}}</h1>
            <h2 class="subtitle">{{@blog.description}}</h2>
        </div>
    </div>
</header>
```

**The burger menu won't work right now.**

---

## Turing boring text posts list into fancy posts card

One of the idea to display the posts list is to put them into card.

To do that, 

1. create `post_card.hbs` inside **partials** folder.

```html
<article class="card">
    <!-- Card image -->
    <!-- Decide if need a place holder image for card. -->
    {{#if feature_image}}
    <div class="card-image">
        <figure class="image is-4by3">
            <img src="{{feature_image}}" alt="{{title}}">
        </figure>
    </div>
    {{/if}}

    <!-- Card content -->
    <div class="card-content">
        <!-- Decide if display author's profile image. -->
        <!-- I prefer not to display autor profile image. -->
        {{!-- {{#if author.profile_image}}
        <div class="media">
            <div class="media-left">
                <figure class="image is-48x48">
                    <img src="{{author.profile_image}}" alt="Profile Image">
                </figure>
            </div>
        </div>
        {{/if}} --}}

        <!-- Display the tile of the post. -->
        <h1 class="title is-4">{{title}}</h1>

        <!-- Content of each card. -->
        <div class="content">
            <!-- Display description or catech the first 40 words of the post. -->
            <p>
                {{#if meta_description}}
                    {{meta_description}}
                {{else}}
                    {{excerpt words="40"}} ...
                {{/if}}
            </p>

            <!-- Display the author, tags and published time. -->
            <p>
                By {{author}} | {{tags}} <br/>
                <time>{{date}}</time>
            </p>
        </div>
    </div>
</article>
```

2. And then import this complex component inside `index.hbs`

   by replacing the `<h1><a href="{{url}}">{{title}}</a></h1>` in foreach loop,

   ```html
   <main>
       {{#foreach posts}}
           <!-- Import customized component. -->
           {{> post_card}}
       {{/foreach}}
   </main>
   ```

   and add the new created component into this loop.

---

## Add style to post card

1. Give the <main> tag a class `<main class="container">` in **`index.hbs`**.

2. And playing around with the bulma columns.

   ```html
   <main class="container">
       <div class="columns is-multiline">
           {{#foreach posts}}
               <div class="column is-half is-centered">
                   {{> post_card}}
               </div>    
           {{/foreach}}
       </div>
   </main>
   ```

   ​