### The Making of "Hello, world."
This repo's README decribes the mechanics necessary to _run_ "Hello, world." In this section, we will break down how it all works, from ClojureDart configuration, thru library usage, Matrix dataflow, Flutter interop and testing on all platforms, including actual and simulated mobile.

The intended audience is both Dart devs who do not know Clojure, and Clojure devs unfamiliar with Flutter. So some materail will seem old hat to some.

#### In the beginning...
In the beginning we created an empty directory named for our app and populated it with a single file, `deps.edn`:
```
{:paths ["src"] ; one or more directory trees where CLJD should look for source to compile
 :deps ;; library dependencies
        {tensegritics/clojuredart 
        {:git/url "https://github.com/tensegritics/ClojureDart.git"
         :sha "ae1b485e84ccc35b122f776dfc7cc62198274701"}
        kennytilton/flutter-mx
        {:git/url "https://github.com/kennytilton/flutter-mx.git"
         :sha "37f79ced88d27b015075b8c6a2b5c98c2c306e6a"}}
 :aliases {:cljd {:main-opts ["-m" "cljd.build"]}} 
 :cljd/opts {:kind :flutter
             :main counter-app.main}} ;; the app entry point
```
That file tells Clojure and ClojureDart (CLJD) utilities what the need to know to manage out project.

Specifically, it tells CLJD enough to populate the rest of the directory with assets required to build and run our app.
```
clj -M:cljd init
```
