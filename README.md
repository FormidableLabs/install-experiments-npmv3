Install Experiments!
====================

This repo gives a simple test of what happens with npm v3, flattening, and
incremental npm updates.

## Starting Point

```js
// <root>/package.json
{
  "dependencies": {
    "builder": "2.9.0",
    "lodash": "^3.10.1"
  }
}
```

Builder has the exact same lodash requirements: `"^3.10.1"`

Commands:

```sh
$ npm install
install-experiments@0.0.1 /Users/rroemer/scm/fmd/install-experiments
├─┬ builder@2.9.0
│ └── SNIPPED
└── lodash@3.10.1
```

Where is lodash?

```sh
$ find . -name lodash
./node_modules/lodash
```

## Experiment - npm install

Now, let's upgrade lodash via package.json!

```js
// <root>/package.json
{
  "dependencies": {
    "builder": "2.9.0",
    "lodash": "^4.8.2"
  }
}
```

With this diff:

```diff
 $ git diff
diff --git a/package.json b/package.json
index 1e79df8..1330008 100644
--- a/package.json
+++ b/package.json
@@ -6,6 +6,6 @@
   "license": "MIT",
   "dependencies": {
     "builder": "2.9.0",
-    "lodash": "^3.10.1"
+    "lodash": "^4.8.2"
   }
 }
```

And now just run _another_ npm install:

```sh
$ npm install
install-experiments@0.0.1 /Users/rroemer/scm/fmd/install-experiments
├─┬ builder@2.9.0
│ └── lodash@3.10.1
└── lodash@4.8.2
```


Where is lodash? Looks to be in _both_ places, as we want:


```sh
$ find . -name lodash
./node_modules/builder/node_modules/lodash
./node_modules/lodash
```

**Conclusion**: Works as expected.

## Experiment - npm install --save VERSION

Now, let's upgrade lodash via the command line!

```sh
$ npm install --save lodash@4.8.2
install-experiments@0.0.1 /Users/rroemer/scm/fmd/install-experiments
├─┬ builder@2.9.0
│ └── lodash@3.10.1
└── lodash@4.8.2
```

Where is lodash? Looks to be in _both_ places, as we want:


```sh
$ find . -name lodash
./node_modules/builder/node_modules/lodash
./node_modules/lodash
```

**Conclusion**: Works as expected.
