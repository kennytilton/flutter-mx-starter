# Flutter/MX Starter

A repo to get you started with your own app using [ClojureDart](https://github.com/Tensegritics/ClojureDart) and [Flutter/Mx](https://github.com/kennytilton/flutter-mx).

### Prerequisites

Steps #1 and #2 in this [CLJD Quick Start](https://github.com/Tensegritics/ClojureDart/blob/main/doc/flutter-quick-start.md) to get you rocking on Flutter tooling

### Clone

Clone this repo locally, changing to your desired project name, here "unicorn":
```bash
git clone https://github.com/kennytilton/flutter-mx-starter.git unicorn
cd unicorn
rm -rf .git
```
Start the internal name change, from "acme":
```bash
mv src/acme src/unicorn
```
Now edit the source globally replacing "acme" with "unicorn":
```bash
find . -type f -exec sed -i '' 's/acme/unicorn/g' {} +
```
When that ^^^ is done, initialize the project, BUT ignore the final instructions on how "to run your application":
```bash
clj -M:cljd init
```
...and  test:
```bash
open -a Simulator
clj -M:cljd flutter
```
You should now see the standard Flutter "counter" app used as a "Hello, world" in Flutter.

Now to get `git` back into the picture:
```bash
git init
git add README.md clean.sh deps.edn lib/main.dart pubspec.* src test
git commit -am "clone from flutter-mx-starter"
```
### Maintenance
As Clojure Dart and Flutter/MX move forward, you might want to move with them
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
         :sha "a6cd31d44507bf81c6baf038e344bd0605955154"}} <================= COPY NEW F/MX SHA HERE
 :aliases {:cljd {:main-opts ["-m" "cljd.build"]}}
 :cljd/opts {:kind :flutter
             :main unicorn.main}}
```
## Support
Look for @kennytilton in the #matrix or #clojuredart channels on the #Clojurians Slack and I will help you get going.
