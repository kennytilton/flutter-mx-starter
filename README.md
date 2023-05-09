# Flutter/MX Starter

A repo to get you started with your own app using [ClojureDart](https://github.com/Tensegritics/ClojureDart) and [Flutter/Mx](https://github.com/kennytilton/flutter-mx).

To create your own:

* git clone https://github.com/kennytilton/flutter-mx-starter.git unicorn
* cd unicorn
* rm -rf .git
* mv src/acme src/unicorn
* edit the source globally replacing "acme" with "unicorn"
* clj -M:cljd init

To test:
* open -A Simulatore
* clj -M:cljd flutter

Now to get `git` back into the picture:
* git init
* git add README.md clean.sh deps.edn lib/main.dart pubspec.* src test
* git commit -am "clone from starter"

Look for @kennytilton in the #matrix or #clojuredart channels on the #Clojurians Slack and I will help you get going.
