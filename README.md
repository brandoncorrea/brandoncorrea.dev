# brandoncorrea.dev

Personal blog website.

## Run

    # Create CSS directory
    mkdir assets/css

    # Compile SCSS once for production...
    sass _styles/main.scss assets/css/stylesheet.css --style compressed

    # ...or watch for changes in development
    sass --watch _styles/main.scss:assets/css/stylesheet.css

    # Run
    bundle exec jekyll serve

## Structure

### _layouts

Not quite sure how this works, but I believe this defines layouts in an HTML/Ruby structure.

I'd guess that the `layout` property defines the name for the given HTML layout that the file describes.

### _posts

New blog posts go here. Be sure to include `title`, `date`, and `categories`
properties. 

`layout` is optional, but should be set to `post` to conform it to all other 
blog posts. If the post requires special styling, it may also be set to that as well.

````
---
layout: post
title:  "My Awesome Blog Post"
date:   YYYY-MM-DD HH:MM:SS -0400
categories: coffee steak COW
---
````

### _styles

SCSS for the entire site.

### _assets

Anything your pages depend on (scripts, images, etc.)

## Creating a New Page

New pages are created in the root director with either an `html` or `markdown` 
file extension. 

These file must contain a header with at least a 
permalink property, similar to the one below.

````
---
permalink: /coffee.html
title: Coffee Makes the World Go Round
layout: default
---
````
