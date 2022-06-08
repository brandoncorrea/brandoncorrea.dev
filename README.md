# brandoncorrea.dev

To start, run this command and navigate to localhost:4000
```
bundle exec jekyll serve
```

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
