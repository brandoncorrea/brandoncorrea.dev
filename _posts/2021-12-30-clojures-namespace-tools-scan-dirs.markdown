---
layout: post
title:  "Clojure's Namespace Tools: scan-dirs"
date:   2021-12-30 7:52:05 -0400
categories: clojure apprenticeship
---

[clojure.tools.namespace][clojure-tools] is a library that helps you track changes in your
project's filesystem and namespace dependencies. In that is `clojure.tools.namespace.dir`, 
which has a `scan-dirs` function that accepts a map and populates it with the current 
projects files and dependencies.

````clojure
=> (d/scan-dirs {})
{:clojure.tools.namespace.dir/files
 #{#object[java.io.File 0x56b45f48 "/Projects/my-project/src/my_project/core.clj"]
   #object[java.io.File 0x26c1717 "/Projects/my-project/spec/my_project/core_spec.clj"]},
 :clojure.tools.namespace.track/deps
 {:dependencies
  {my-project.core-spec #{my-project.core speclj.core},
   my-project.core #{clojure.tools.namespace.dir clojure.pprint}},
  :dependents
  {my-project.core #{my-project.core-spec},
   speclj.core #{my-project.core-spec},
   clojure.tools.namespace.dir #{my-project.core},
   clojure.pprint #{my-project.core}}},
 :clojure.tools.namespace.track/unload
 (my-project.core-spec my-project.core),
 :clojure.tools.namespace.track/load
 (my-project.core my-project.core-spec),
 :clojure.tools.namespace.file/filemap
 {#object[java.io.File 0x26c1717 "/Projects/my-project/spec/my_project/core_spec.clj"]
  my-project.core-spec,
  #object[java.io.File 0x56b45f48 "/Projects/my-project/src/my_project/core.clj"]
  my-project.core},
 :clojure.tools.namespace.dir/time 1640969282001}
````

Okay so that's a LOT of information! What do we _actually_ care about here? Well let's 
take a look at the keys to break this down a little.

````clojure
=> (keys (d/scan-dirs {}))
(:clojure.tools.namespace.dir/files
 :clojure.tools.namespace.track/deps
 :clojure.tools.namespace.track/unload
 :clojure.tools.namespace.track/load
 :clojure.tools.namespace.file/filemap
 :clojure.tools.namespace.dir/time)
````

### `::files`

The first thing we have here is files. This is basically just a listing of all Clojure
files in a project.

````clojure
:clojure.tools.namespace.dir/files
 #{#object[java.io.File 0x56b45f48 "/Projects/my-project/src/my_project/core.clj"]
   #object[java.io.File 0x26c1717 "/Projects/my-project/spec/my_project/core_spec.clj"]}
````

Now if we add `new_cljs_file.cljs` and `new_cljc_file.cljc` to `src`, we can rerun the scan
and see that only the cljc file is added while the cljs file is ignored. I'm not exactly sure
why this is–maybe there's an option that can be passed down to support this? Or maybe this 
is just how `scan-dirs` is expected to operate.

````clojure
:clojure.tools.namespace.dir/files
 #{#object[java.io.File 0xbdecc21 "/Projects/my-project/src/my_project/core.clj"]
   #object[java.io.File 0x4d33940d "/Projects/my-project/src/my_project/new_cljc_file.cljc"]
   #object[java.io.File 0x43cf6ea3 "/Projects/my-project/spec/my_project/core_spec.clj"]},
````

### `::deps`

In our results, we also get a mapping of sources to dependencies as well as dependencies to sources.

````clojure
:clojure.tools.namespace.track/deps
{:dependencies
 {my-project.core-spec #{my-project.core speclj.core},
  my-project.core #{clojure.tools.namespace.dir clojure.pprint}},
 :dependents
 {my-project.core #{my-project.core-spec},
  speclj.core #{my-project.core-spec},
  clojure.tools.namespace.dir #{my-project.core},
  clojure.pprint #{my-project.core}}}
````

`:dependencies` is a map where the keys represent a file namespace and the values represent what
those namespaces depend on. So `my-project.core`, for example, `:require`s `clojure.pprint` and 
`clojure.tools.namespace.dir`. So in the `:dependencies` map, both of those namespaces are included 
for core. 

Likewise, `my-project.core-spec` only `:require`s depends on `my-project.core` and `speclj.core`.
So those namespaces are included in that namespace's dependencies.

````clojure
; my_project.clj
(ns my-project.core
    (:require [clojure.pprint :as pp]
              [clojure.tools.namespace.dir :as d]))

; my_project_spec.clj
(ns my-project.core-spec
    (:require [speclj.core :refer :all]
              [my-project.core :refer :all]))
````

On the other side of things, we have `:dependents` which is an inversion of this dependency map 
where the `:require`d modules are mapped to those namespaces that depend on them.

### `::load` & `::unload`

These two keys don't seem to change when I add, remove, or update files. So I can't say with
confidence what these actually represent. Although, they do seem to only hold the namespaces
declared in my project's source files.

````clojure
:clojure.tools.namespace.track/unload
 (my-project.core-spec my-project.core),
 :clojure.tools.namespace.track/load
 (my-project.core my-project.core-spec)
````

### `::filemap`

`filemap` is a map with `java.io.File` objects being the keys and the namespaces associated in those
files as the values. Since each file _should_ have a unique namespace, you should be able to look up
a file by its namespace and a namespace by its file using the `filemap`.

````clojure
:clojure.tools.namespace.file/filemap
 {#object[java.io.File 0x26c1717 "/Projects/my-project/spec/my_project/core_spec.clj"]
  my-project.core-spec,
  #object[java.io.File 0x56b45f48 "/Projects/my-project/src/my_project/core.clj"]
  my-project.core}
````

Something I found a bit confusing about this interface is that the input you provide `scan-dirs` 
doesn't actually affect the output in any meaningful way. You don't have a difference report,
a new file report, or anything else like that. It really just gives you a listing of all the files 
and namespaces in your project.

Now that's not to say that this function isn't useful. `scan-dirs` gives you _tons_ of useful
information that you can work off of! Particularly `::filemap` and `::deps`. Without this,
you'd have to write your own reader function, and who wants to do that?!

[clojure-tools]: https://github.com/clojure/tools.namespace