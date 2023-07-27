### Building the Flutter Counter App with Flutter/MX

Now that we have a "Hello, world." running, we are ready to build a small app from scratch. The classic Flutter starter app is a simple "counter" app, which:
* displays a counter starting at zero;
* offers a button we can use to increment the counter; and
* has a bit of nice layout and labelling.

In the Flutter world, IDEs such as VSCode and IntelliJ generate this app automatically on demand. We will build ours in `f/mx` from scratch. But first, let us look at the Dart version we will replicate, greatly abbreviated:
```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.deepPurple),
        useMaterial3: true,
      ),
      home: const MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}
```
#### Replicating `runApp` in `f/mx`
If we look at the project `main` function, we see that things are set up already to run any given app:
```clojure
(ns counter-app.main
  (:require
    ["package:flutter/widgets.dart" :as w] ;; our "import"
    [tilton.fmx.api :as fx]
    [counter-app.hello-world :as hello]))

(defn main []
  (.ensureInitialized w/WidgetsFlutterBinding) ;; standard Flutter initialization
  (fx/run-fx-app                               ;; a 'runAPP` that can run f/mx proxy apps
    (hello/make-app)))                         ;; any function that returns an f/mx proxy app
```
Unwinding that:
* `hello/make-app` builds a Flutter/MX proxy for a Flutter "Hello, world" app;
* `fx/run-fx-app` invokes `m/runApp`, after:
  * converting our f/mx app to a native Dart/Flutter app; and
  * wrapping that native app in an anonymous stateless container, the role played by `MyApp` above.

Worth noting:
* `(.ensuredInitialzed w/WidgetsFlutterBinding)` is much like the native Dart `WidgetsFlutterBinding.ensureInitialized()`;
* the `w/` prefix references the alias established by `["package:flutter/widgets.dart" :as w]`. Clojure source files each must declare such linkage individually;
* the method invocation syntax is `(method class arguments*)` instead of `class.method( arguments*)`;
* standard CLJD interop preserves the camelCase; but
* `f/mx` wrappers `run-fx-app` uses Lisp kebab case by convention. CLJD exposes the Flutter API in the same camelCase used by Dart, but `f/mx` proxy constructors are named in kebab-case. This is to preserve our sanity, since `f/mx` components can be built with native Flutter elements as delegates or children.
  
So now we just need to build a proxy for a `MaterialApp`.

#### Replicating `MaterialApp`
We can use `hello_world.app` as our starting point. We will leave the original "Hello, world." app as is, and build our Counter app alongside it.

1. Create a new file/namespace for our app. Use your chosen IDE to create a new file `counter.cljd` alongside `hello_world.cljd`.
2. Replace any automatically generated content of `counter.cljd` with the contents of `hello_world.cljd`.
3. Restore the `ns` name `counter-app.counter`.
4. Replace the function `make-app` with this equivalent of the original `MaterialApp` call:

```
(material-app
    {:title "Flutter/MX Demo"
     :theme (m/ThemeData
              .useMaterial3 true
              .colorScheme (m/ColorScheme.fromSeed
                             .seedColor m/Colors.deepPurple))}
```
No, that will not display much, but we just want enough to hook up to our `run-app`. 

Save your changes and correct any errors. Once it compiles.

Back in `counter-app.main`:
* Add `[acme.counter-app :as counter]` to the list of `:require`s; and
* change the `make-app` alias from `hello` to `counter`:

```
(ns acme.main
  (:require
    ["package:flutter/widgets.dart" :as w]
    [tilton.fmx.api :as fx]
    [acme.hello-world :as hello]
    [acme.counter-app :as counter]))

(defn main []
  (.ensureInitialized w/WidgetsFlutterBinding)
  (fx/run-app
    (fx/fx-render nil
      (counter/make-app))))
```
Save again and you should see the app in your simulator change to a black screen:

<img src="https://github.com/kennytilton/flutter-mx-starter/blob/main/image/mat-app-only.png"
  width="20%" height="20%">

Success! Now let us add the `home` widget.

#### Home alone

`Flutter/MX`, by design, codes much like native Flutter. We want Flutter doc to be the doc for `f/mx`. This means that, once the syntax hurdle is overcome, a strong Flutter developer can work with `f/mx` as they do now with Flutter. That said, `f/mx` _does_ deviate from Flutter in a few ways intended to enhance the coding experience. 

One example of this is that we adopt the uniformity of the HTML DOM, where it is all nodes and child nodes, a simple tree. When we see a property (1) on a commonly used widget and (2) that seems like it could be a child, we make that transformation. In the case of `material-app`, we have it accept one child, and internally pass that to `MaterialApp` as `home:`.
```clojure
(defn make-app []
  (material-app
    {:title "Flutter/MX Demo"
     :theme (m/ThemeData
              .useMaterial3 true
              .colorScheme (m/ColorScheme.fromSeed
                             .seedColor m/Colors.deepPurple))}
    (scaffold
      {:appBar (app-bar
                 {:backgroundColor (fx/in-my-context [me ctx]
                                     (.-inversePrimary (.-colorScheme (m/Theme.of ctx))))
                  :title           (m/Text (mget me :title))})})))
  
```
> Replace your `make-app` with the one above and save. You should see the following:

<img src="https://github.com/kennytilton/flutter-mx-starter/blob/main/image/mat-scaffold.png"
  width="20%" height="20%">

Note the `app-bar` above. Its two properties are worth noting. To specify the `:backgroundColor`, the spec says to work off the `context` Theme. To access the context, we see a macro `in-my-context` being used to wrap a form in a callback with parameters `me` and `ctx`, the context. When `f/mx` internals "build" the `app-bar`, they will see this callback and know to invoke it in order to determine the background color.

A second interesting property is `:title`, with a value of `(mget me :title)`. For reasons we will explore in depth more and more, `me` is bound to the `material-app` widget. So just as the native code accessed `widget.title`, where widget was the MaterialApp, we access `(mget me :title)`.

#### Scaffold body <= child
Now let's give our Scaffold some content. Native Dart developers will know we do that by supplying a widget for the Scaffold `:body` parameter, as we see in the native Dart:
```dart
Scaffold ....
    body: Center(
           child: Column(
                    mainAxisAlignment: MainAxisAlignment.center,
                    children: <Widget>[
                       const Text('You have pushed the button this many times:')))
```
As with `MaterialApp`, the `f/mx` `scaffold` expects a single `kid` widget, which it will pass to the native Scaffold as `body:`.

> Replace the current `scaffold` form with the following, a partial implementation of the Counter App:

```clojure
(scaffold
  {:appBar (app-bar
             {:backgroundColor (fx/in-my-context [me ctx]
                                 (.-inversePrimary (.-colorScheme (m/Theme.of ctx))))
              :title           (m/Text (mget me :title))})}
   (center
    (column {:mainAxisAlignment m/MainAxisAlignment.center}
      (text {:style (p/TextStyle .color m/Colors.black
                      .fontSize 18.0)}
        "You have pushed the button this many times:"))))
```
Save and you should see:

<img src="https://github.com/kennytilton/flutter-mx-starter/blob/main/image/scaffold-one-label.png"
  width="20%" height="20%">

Note another nod to the regularity of HTML syntax: the string content of the f/mx `text` widget appears in the "child" position! This is like `<b>Hi, Mom!</b>` expanding to a bold node with a child `textContent`. Here, our f/mx string "child" gets passed as the first parameter to `m/Text` by f/mx internals, expanding like this:
```clojure
(m/Text "You have pushed the button this many times:"
   .style (p/TextStyle .color m/Colors.black .fontSize 14.0)
```
Now let's add the counter, and a button to increment it. We will see Matrix reactivity for the first time!

#### A counter and a (+) button

Now we can add the rest of the Counter App interface:
* local state to hold the count;
* a field to display the count; and
* a `FloatingActionButton` rigged to increment the counter.

All that can be found in the new version of `make-app`, below. Check the comments to see how we extended our app with state, display, and control:
```
(defn make-app []
  (material-app
    {:title "Flutter/MX Demo"
     :theme (m/ThemeData
              .useMaterial3 true
              .colorScheme (m/ColorScheme.fromSeed
                             .seedColor m/Colors.deepPurple))}
    (scaffold
      {:appBar (app-bar
                 {:backgroundColor (fx/in-my-context [me ctx]
                                     (.-inversePrimary (.-colorScheme (m/Theme.of ctx))))
                  :title           (m/Text (mget me :title))})
       :floatingActionButton
               (cF (fx/floating-action-button
                     {:onPressed (as-dart-callback []
                                   (mswap! (fm* :my-counter) :count inc))
                      :tooltip   "Increment"}
                     (m/Icon m/Icons.add)))}
      {;--- custom state goes in an optional, second map literal -------
         :name  :my-counter                                 ;; will we look this widget up by name
         :count (cI 0)}                                     ;; cI means "cell (for) Input"
      (center
        (column {:mainAxisAlignment m/MainAxisAlignment.center}
          (text {:style (p/TextStyle .color m/Colors.black
                          .fontSize 18.0)}
            "You have pushed the button this many times:")
          (text
            {:style (fx/in-my-context [me ctx]
                      (.-headlineMedium (.-textTheme (m/Theme.of ctx))))}
            ; all kids get silently wrapped in a formulaic cell
            ; this formula uses 'fasc' to search 'f'amily 'asc'endants for the first named :my-counter
            (str (mget (fasc :my-counter) :count))))))))
```
Save and click the (+) button a few times, and you should see the following:

<img src="https://github.com/kennytilton/flutter-mx-starter/blob/main/image/final-version.png"
  width="20%" height="20%">

Things to note:
* `f/mx` widgets follow the OO prototype model, meaning we can add ad hoc state as needed for our apps. Here we attach the `:count` property to the `scaffold`, and give the scaffold a `:name` so other widgets can reference it;
* search utilities such as `fm*` and `fasc` will need more documentation, under "Navigation";
* we wrapped the counter value in a `cI` "input cell" so we could mutate it; and
* an automatic `cFkids` wrapper is provided for all forms following the parameter map(s).

