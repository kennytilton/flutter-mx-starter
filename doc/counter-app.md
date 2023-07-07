### Building the Flutter Counter App with Flutter/MX

Now that we have a "Hello, world." running, we are ready to build a small app from scratch. The classic Flutter starter app is a simple "counter" app, which:
* displays a counter starting at zero;
* offers a button we can use to increment the counter; and
* has a bit of nice layout and labelling.

In the Flutter world, IDEs such as VSCode and IntelliJ generate this app automatically on demand. We will build ours in `f/mx` from scratch. But first, let us look at the Dart version we will replicate, greatly abbreviated to start:
```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
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
* the `w/` prefix references the alias established by `["package:flutter/widgets.dart" :as w]`. Clojure source files handle such linkage individually;
* the method invocation syntax is `(method class arguments*)` instead of `class.method( arguments*)`;
* standard CLJD interop preserves the camelCase; but
* `f/mx` wrappers `run-fx-app` uses Lisp kebab case by convention. CLJD exposes the Flutter API in the same camelCase used by Dart, but `f/mx` proxy constructors are named in kebab-case. This is to preserve our sanity, since `f/mx` components can be built with native Flutter elements.
  
So now we just need to build a proxy for a `MaterialApp`.

#### Replicating `MaterialApp`
We can use `hello_world.app` as our starting point. We will leave the original "Hello, world." app as is, and build our Counter app alongside it.

1. Create a new file/namespace for our app. Use your chosen IDE to create a new file `counter.cljd` alongside `hello_world.cljd`.
2. Replace any automatically generated content of `counter.cljd` with the contents of `hello_world.cljd`.
3. Restore the `ns` name `counter-app.counter`.
4. Delete the function `make-app`.
