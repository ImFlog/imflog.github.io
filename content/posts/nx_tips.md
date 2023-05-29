+++
title="NX tips"
date=2023-05-29

[taxonomies]
categories = ["Tips"]
tags = ["NX", "Monorepo"]

[extra]
comments = true
+++

# Context
I recently started working on trying to improve the development experience on the frontend part of my company.
The goal was to be able to:
* reduce the dependency management issues that we were having
* co-localize the code to reduce friction between teams
* reduce build time by using caches
* share a common set of practice (linting, testing ...) with ease.

Basically we wanted to use a [monorepo](https://monorepo.tools/).

So I compared multiple tools:
* [Turborepo](https://turbo.build/)
* [NX](https://nx.dev/)
* [Lerna](https://lerna.js.org/)
* [Rush](https://rushjs.io/)

I won't go much into details about the differences between those tools, it's not the purpose of this article, but we ended up choosing NX.
In the next part I will list some tips about how to use NX, and will probably update this article with new tips as I discover them.

# NX tricks
NX as a pretty solid documentation, but sometimes I feel like it's missing some examples, that's why I am writing this article, for my future self.

## Running root level commands
To run root level command, you can modify your `package.json` to add a script like this one:
```json
{
  "scripts": {
    "format": "nx run-many --target=lint --all --parallel"
  }
}
```
This will allow you to run `npm run format` and it will run the `lint` target on all the projects in parallel.
But what bothered me was that It forces developers to switch tool to run project level commands while the `nx` CLI would be used everywhere else.
So I wanted to be able to run `nx format` instead of `npm run format`.
To do so you can update your `package.json` with the following:
```json
{
   "nx": {},
   "scripts": {
      "format": "nx run-many --target=lint --all --parallel"
   }
}
```
And voilà, you can now run `nx format` and it will run the `lint` target on all the projects in parallel.

Note that the opposite is also possible, to run everything through `npm` but keep the caching and parallelization of NX
by prefixing commands by `nx exec -- ...`.

Full documentation is available [here](https://nx.dev/recipes/other/root-level-scripts).

## Defining common targets
NX allows you to configure your monorepo packages in a `project.json` file (one per package).
In my context, I wanted to define some common targets that would be used by all the packages,
but I didn't want to copy/paste the same targets in all the `project.json` files.

So How can I share those targets but still be able to configure them per package if needs be ?

The answer is to use `targetDefaults` in the `nx.json` file (the global project configuration that is at the root of the monorepo).
Here we could define our previous lint configuration like this:
```json
{
   "extends": "nx/presets/npm.json",
   "$schema": "./node_modules/nx/schemas/nx-schema.json",
   "targetDefaults": {
      "lint": {
         "executor": "@nx/linter:eslint",
         "options": {
            "lintFilePatterns": ["src/**/*.ts"]
         }
      }
   }
}
```
Then we would need to update the `project.json` of each of the packages that want to use this target like so if no override are required:
```json
{
   "name": "my-package",
   "$schema": "../../node_modules/nx/schemas/project-schema.json",
   "targets": {
      "lint": {}
   }
}
```
But if we need to override parts of the target configuration, we can:
```json
{
   "name": "my-package",
   "$schema": "../../node_modules/nx/schemas/project-schema.json",
   "targets": {
      "lint": {
         "options": {
            "lintFilePatterns": ["src/**/*.ts", "src/**/*.js"]
         }
      }
   }
}
```
That's a good balance between monorepo global convention and specific configuration per package.

Unfortunately this feature is not well documented [here](https://nx.dev/reference/nx-json#target-defaults), but I managed to find
an issue and an associated PR that explains what I told you before: https://github.com/nrwl/nx/pull/12435.

# Conclusion
That's it for now, I am not sure if I update this article or create new ones / split this one in multiple articles in the future
so keep an eye on my blog if you are interested in this kind of articles.

Flo.