### The Making of "Hello, world."
This repo's README decribes the mechanics necessary to _run_ "Hello, world." Hopefully that went well. In this section, we will break down _how it all works_, from ClojureDart configuration, thru library usage, Matrix dataflow, Flutter interop and testing on all platforms, including actual and simulated mobile.

The intended audience is both Dart devs who do not know Clojure, and Clojure devs unfamiliar with Flutter. So some materail will seem old hat to some.

#### "Hello, world." misunderstood
Many misunderstand the point of the "Hello, world." exercise. They think the point is, as described in K&R:
```
_Print the words_
    hello, world
```
So we see "solutions" like:
```
$ python3
Python 3.11.3 (main, Apr  7 2023, 19:25:52) [Clang 14.0.0 (clang-1400.0.29.202)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> print("Hello, world")
Hello, world
```
Looks satisfactory, right? Now let's see what the authors had in mind:
> [Writing this program] is the big hurdle; to leap over it you have to create the program text somewhere, compile it successfully, load it, run it, and find out where your output went. With these mechanical details mastered, everything else is relatively easy.

They leave out successfully referencing an external library, and linking where necessary. For the Python programmer, or ClojureDart, the equivalent sequence would be:
* install Xcode;
* install Dart/Flutter;
* install Clojure;
* install ClojureDart
* pick an IDE and get that working with CLJD;
* require the right Flutter packages;
* compile/run; and
* build for relase.

Let's get started!

#### In the beginning...
In the beginning, we got a running start by cloning this repository into a new `unicorn` directory, and that repo already contained the key to creating a new `f/mx` project: `deps.edn`. If we were starting from scratch, we would have created an empty directory named for our app...
```
mkdir unicorn
cd unicorn
```
...and populated it with the same single file, `deps.edn`:
```
{:paths ["src"] ; where your cljd files are
 :deps {tensegritics/clojuredart
        {:git/url "https://github.com/tensegritics/ClojureDart.git"
         :sha "5ac0b6ccecd8a41e1a317e164dfc2dd9062d739a"}

        kennytilton/flutter-mx
        {:git/url "https://github.com/kennytilton/flutter-mx.git"
         :sha "0b4bd358acf60db6e40a83c9b35bd16ca78d4935"}}
 :aliases {:cljd {:main-opts ["-m" "cljd.build"]}}
 :cljd/opts {:kind :flutter
             :main unicorn.main}}
```
That file tells Clojure and ClojureDart (CLJD) utilities what the need to know to manage out project.

Specifically, it tells CLJD enough to populate the rest of the directory with assets required to build and run our app. Still in the same root directory:
```
clj -M:cljd init
```
#### Key assets
The first interesting asset created by `init`, familiar to Clojure developers, is the `src` directory pointed to by the `:paths` property of `deps.edn`. The plural "paths" means business: the `f/mx` library itself has a second directory of source containing dozens of examples used during testing, but which we do not want burdening apps which include `f/mx`.

Flutter developers will recognize a second vital asset, `pubspec.yaml`, where Dart/Flutter library dependencies are defined. Let us look at that more closely:

##### pubspec.yaml
The Flutter ecosystem is supported by a substantial package repository maintainer by Google, [pub.dev](https://pub.dev/). Let us see how that works. Suppose we want to generate UUIDs, and want a nice rich UUID library to help. Here is how we proceed.

1. Search `pub.dev` for a UUID package. We find [uuid](https://pub.dev/packages/uuid).
2. Now click on the ["Installing" tab](https://pub.dev/packages/uuid/install).
3. We see several options. We want the one for Flutter:
   ```flutter pub add uuid```
4. Back in our terminal, we enter exactly that command, and our `pubspec.yaml` is modified to pull `uuid`.

Is everything OK? Enter this:
```
flutter doctor
```
That may report issues for us to resolve. Or, over time, libraries can change and come to conflict. We can check with this command:
```
dart pub outdated
```
...and hopefully cure with a simple:
```
dart pub upgrade
```
Left as an exercise is modifying our source to work with any breaking package changes.

##### ios/Podfile and Podfile.lock
Every couple of months or so I encounter a build error that includes a suggestion to run `pod imstall`. It is not clear how that gets triggered, but as the error suggests, this has always wordked for me:
```
cd ios
pod install
cd ..
```
### The source
And now at long last we get to look at some code. 

#### The namespace
We begin with the first form, a so-called "namespace" (hence `ns`) that defines the library coordinates, here `acme.world`, and the other libraries and packages needed by this source file.

```clojure
(ns acme.hello-world
  (:require
    [tilton.fmx.api :as fx
     :refer [scaffold app-bar text center column material-app]]
    ["package:flutter/widgets.dart" :as w]
    ["package:flutter/material.dart" :as m
     :refer [ThemeData MainAxisAlignment]]
    ["package:flutter/painting.dart" :as p]))
```
First we see the `ns` name, `acme.hello-world`. By convention, this corresponds to a file with extension `.clj[|c|d]` located in the file system at `_srcPath_/acme` with filename `hello_world`. _srcPath_ must be one listed in the `:paths` option of `deps.edn`. The underscore is no typo, rather a nod to the Java underlying Clojure proper.

We also see a CLJD dependency, `tilton.fmx.api`, and several flutter packages. CLJD provides the latter, but to get our own `fmx` library we need the dependency `kennytilton/flutter-mx`, again specified in `deps.edn`.

Worth noting:
* circularities are not allowed (so `tilton.fmx.api` cannot reference `acme.hello-world`);
* aliases are used to refer to library entities, eg, `fx/hero` and `m/Image` we will see;
* the `:refer` clause lets us use widgets like `scaffold` and `app-bar` without aliases.


