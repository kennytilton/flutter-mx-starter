# Flutter/MX Starter

A starter/instructional repo to help get you started on your own app using [ClojureDart](https://github.com/Tensegritics/ClojureDart) and [Flutter/MX](https://github.com/kennytilton/flutter-mx).

This repo out of the box will run this "Hello, world." app, a display-only exercise:

<img src="https://github.com/kennytilton/flutter-mx/blob/main/image/HW%20d%20Sim.png" width="240">

That is Brian Kernighan, popularizer of the "Hello, world." app/instructional approach.

Once we have that running, we will go through the source code in detail, with complete `f/mx` beginners in mind, be they Clojurians or Flutteristas. The highlight will be how native Flutter constructors can be combined with `f/mx` widgets, and especially how we use syntax to make that mixture more readable.

Next, we will build something from scratch, viz. the classic Flutter Counter app. That will introduce our first UI control and a bit of reactive state, our first encounter with Matrix (MX) state management.

With that under your belt, you should be able to get rolling on your own Unicorn, with help as always from `@kennytilton` on the Clojurians Slack, in #matrix or #clojuredart.

### Prerequisites

First, work thru steps #1 and #2 in this [CLJD Quick Start](https://github.com/Tensegritics/ClojureDart/blob/main/doc/flutter-quick-start.md) to get you rocking on ClojureDart and Flutter development in general. You can skip this if you are already coding with CLJD and Flutter.

### Clone

Clone this repo locally, changing to your desired project name, here "unicorn":
```bash
git clone https://github.com/kennytilton/flutter-mx-starter.git unicorn
cd unicorn
rm -rf .git
```
We will do a fresh `git init` after a few more steps.

Now we start the internal name change, from "acme":
```bash
mv src/acme src/unicorn
```
Now edit the source globally replacing "acme" with "unicorn":
```bash
find . -type f -exec sh -c 'file -b "$1" | grep -q ASCII' sh {} \; -exec sed -i '' 's/acme/unicorn/g' {} +
```
When that ^^^ is done, initialize the project, BUT ignore the final instructions on how "to run your application". WARNING: this step can pause for a minute after displaying "Got dependencies.":
```bash
clj -M:cljd init
```
...and  test. To use an iOS sim:
```bash
open -a Simulator
clj -M:cljd flutter
```
You should now see Mr. Kernighan and the app on your sim.

To test other ways, check out [this section of the f/mx README](https://github.com/kennytilton/flutter-mx/blob/main/README.md#2-you-now-have-three-delightful-options).

Now to get `git` back into the picture:
```bash
git init
git add .gitignore .gitattributes README.md clean.sh deps.edn lib/main.dart pubspec.* src image
git commit -am "clone from flutter-mx-starter"
```
### Maintenance
As Clojure Dart and Flutter/MX move forward, you might want to move with them

#### Flutter/Dart
Easy:
```bash
flutter upgrade
```
#### ClojureDart
Easy:
```bash
clj -M:cljd upgrade
```
#### Flutter/MX
Get the latest SHA from [the f/mx repo](https://github.com/kennytilton/flutter-mx) and copy into `deps.edn`:
```
{:paths ["src"] ; where your cljd files are
 :deps {tensegritics/clojuredart
        {:git/url "https://github.com/tensegritics/ClojureDart.git"
         :sha "84b18f8e67556862f265ea41f3a9e5f9506cdbef"}

        kennytilton/flutter-mx
        {:git/url "https://github.com/kennytilton/flutter-mx.git"
         :sha "a7250e50a7016499ca1effb63b8d0606f373c247"}} <================= COPY NEW F/MX SHA HERE
 :aliases {:cljd {:main-opts ["-m" "cljd.build"]}}
 :cljd/opts {:kind :flutter
             :main unicorn.main}}
```
### Next steps
If we followed the steps above accurately, we did manage to get a CLJD Flutter/MX demo app running, but we did not learn very much about how it all works. In our next doc, we look at the infrastructure behind the scenes: [The Making of "Hello, world."](https://github.com/kennytilton/flutter-mx-starter/blob/main/doc/hello-world.md).

Then we build a second "app" from scratch, a [Flutter/MX implementation](https://github.com/kennytilton/flutter-mx-starter/blob/main/doc/counter-app.md) of the classic Flutter Counter app.

### Support
Look for @kennytilton in the #matrix or #clojuredart channels on the #Clojurians Slack and I will help you get going.
