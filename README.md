# Flutter/MX Starter

A repo to get you started with your own app using [ClojureDart](https://github.com/Tensegritics/ClojureDart) and [Flutter/Mx](https://github.com/kennytilton/flutter-mx).

To create your own clone locally, to your desired project name, here "unicorn":
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
When that ^^^ is done:
```bash
clj -M:cljd init
```
...and  test:
```bash
open -a Simulator
clj -M:cljd flutter
```

Now to get `git` back into the picture:
```bash
git init
git add README.md clean.sh deps.edn lib/main.dart pubspec.* src test
git commit -am "clone from flutter-mx-starter"
```
## Support
Look for @kennytilton in the #matrix or #clojuredart channels on the #Clojurians Slack and I will help you get going.
