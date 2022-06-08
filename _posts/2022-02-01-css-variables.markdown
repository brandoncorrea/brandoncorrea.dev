---
layout: post
title:  "CSS Variables"
date:   2022-02-01 7:52:05 -0400
categories: css javascript clojure apprenticeship
---

Over the past couple of days, I've been working on implementing 
a "theming" feature on a website for light mode and dark mode.
The goal here was to make it so that the light and dark themes could
be toggled with a class, `light-mode` or `dark-mode`.

To begin, we needed to create our light and dark mode CSS classes.

````css
.light-mode {
    --text-color: #000;
    --back-color: #fff;
    --border-color: #2e2e2e;
}

.dark-mode {
    --text-color: #fff;
    --back-color: #444;
    --border-color: #3e3e3e;
}
````

While CSS variables are typically done using a `:root` selector, applying the
variables to class names seemed to work best for what we were trying to achieve.
Now in the rest of the CSS, these variables can be referenced using 
`var(--variable-name)`

````css
.textbox {
    color: var(--text-color);
    border-color: var(--border-color);
    background-color: var(--back-color);
}
````

Once we have our CSS in place, all that's left to do is add and remove 
class names from HTML elements, which we can do with JavaScript!

````javascript
function toggleClass(from, to) {
    document.body.classList.add(to);
    document.body.classList.remove(from);
}
function toggleLightMode() {
    toggleClass('dark-mode', 'light-mode');
}
function toggleDarkMode() {
    toggleClass('light-mode', 'dark-mode');
}
````

Now in the ClojureScript environment, this is going to look a _little_ different.

````clojure
(defn class-list [] (-> js/document .-body .-classList))
(defn toggle-class [from to]
  (let [class-list (class-list)]
    (.add class-list to)
    (.remove class-list from)))
(defn toggle-light-mode []
  (toggle-class "dark-mode" "light-mode"))
(defn toggle-dark-mode []
  (toggle-class "light-mode" "dark-mode"))
````

This will compile to just about the same code, but all done the ClojureScript way!
