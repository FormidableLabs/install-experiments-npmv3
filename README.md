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

## Experiment - npm shrinkwrap

First, let's shrinkwrap:

```sh
$ npm shrinkwrap
```

Baseline test: re-install from scratch produces same tree:

```sh
$ rm -r node_modules
$ npm install
install-experiments@0.0.1 /Users/rroemer/scm/fmd/install-experiments
├─┬ builder@2.9.0
│ └── SNIPPED
└── lodash@3.10.1
```

Update the shrinkwrap. Diff:

```diff
diff --git a/package.json b/package.json
index 0927aed..7bcd26e 100644
--- a/package.json
+++ b/package.json
@@ -13,6 +13,6 @@
   },
   "dependencies": {
     "builder": "2.9.0",
-    "lodash": "^3.10.1"
+    "lodash": "^4.8.2"
   }
 }
```

Remove existing shrinkwrap and recreate:

```sh
$ rm npm-shrinkwrap.json
$ npm install
$ npm shrinkwrap
```

produces the expected diff:

```diff
diff --git a/npm-shrinkwrap.json b/npm-shrinkwrap.json
index 5a4fedb..b99722a 100644
--- a/npm-shrinkwrap.json
+++ b/npm-shrinkwrap.json
@@ -29,7 +29,14 @@
     },
     "builder": {
       "version": "2.9.0",
-      "from": "builder@2.9.0"
+      "from": "builder@2.9.0",
+      "dependencies": {
+        "lodash": {
+          "version": "3.10.1",
+          "from": "lodash@>=3.10.1 <4.0.0",
+          "resolved": "https://registry.npmjs.org/lodash/-/lodash-3.10.1.tgz"
+        }
+      }
     },
     "chalk": {
       "version": "1.1.3",
@@ -57,9 +64,9 @@
       "resolved": "https://npme.walmart.com/j/js-yaml/_attachments/js-yaml-3.5.5.tgz"
     },
     "lodash": {
-      "version": "3.10.1",
-      "from": "lodash@>=3.10.1 <4.0.0",
-      "resolved": "https://registry.npmjs.org/lodash/-/lodash-3.10.1.tgz"
+      "version": "4.8.2",
+      "from": "lodash@>=4.8.2 <5.0.0",
+      "resolved": "https://registry.npmjs.org/lodash/-/lodash-4.8.2.tgz"
     },
     "nopt": {
       "version": "3.0.6",
```

**Conclusion**: Works as expected.
