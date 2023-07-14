### The Making of "Hello, world."
This repo's README decribes the mechanics necessary to _run_ "Hello, world." In this section, we will break down how it all works, from ClojureDart configuration, thru library usage, Matrix dataflow, Flutter interop and testing on all platforms, including actual and simulated mobile.

The intended audience is both Dart devs who do not know Clojure, and Clojure devs unfamiliar with Flutter. So some materail will seem old hat to some.

#### In the beginning...
In the beginning, we got a running start by cloning this repository into a new `unicorn` directory, and that repo already contained the key to creating a new `f/mx` project: `deps.edn`. If we were starting from scratch, we would have created an empty directory named for our app...
```
mkdir unicorn
cd unicorn
```
...and populated it with the same single file, `deps.edn`:
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

