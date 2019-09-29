---
title: Require-Less-Autoprefixer
date: 2017-08-23 11:06:08
---

We use `Require.js` as our module loader and `r.js` as our assets bundler and we use `LESS` as our CSS preprocessor for its smarter syntax. Now we want to add `autoprefixer`.

There's no `postcss` plugin for `Require.js` and since we pack template, controller and stylesheet together for each compoponent, we have to use some `LESS` plugin to achieve this.

So we choose `less-plugin-autoprefix`.

The problem is that the API of `less-plugin-autoprefix` requires us to pass an initialized plugin instance to the `LESS` options. However, during the `r.js` optimization, the function `context.configure` is called, which does a deep clone of the config object but only clones objects' `ownProperties`, which will ignore the plugin installer function as it's inherited from its prototype, so that later the less plugin cannot be installed.

A quickfix:
```js
var autoprefixPlugin = new LessPluginAutoprefix();
autoprefixPlugin.install = LessPluginAutoprefix.prototype.install;
```
