---
title: npm-query
section: 1
description: Dependency selector query
---

### Synopsis

<!-- AUTOGENERATED USAGE DESCRIPTIONS -->

### Description

The `npm query` command allows for usage of css selectors in order to retrieve
an array of dependency objects.

### Piping npm query to other commands

```bash
# find all dependencies with postinstall scripts & uninstall them
npm query ":attr(scripts, [postinstall])" | jq 'map(.name)|join("\n")' -r | xargs -I {} npm uninstall {}

# find all git dependencies & explain who requires them
npm query ":type(git)" | jq 'map(.name)' | xargs -I {} npm why {}
```

### Extended Use Cases & Queries

```stylus
// all deps
*

// all direct deps
:root > *

// direct production deps
:root > .prod

// direct development deps
:root > .dev

// any peer dep of a direct deps
:root > * > .peer

// any workspace dep
.workspace

// all workspaces that depend on another workspace
.workspace > .workspace

// all workspaces that have peer deps
.workspace:has(.peer)

// any dep named "lodash"
// equivalent to [name="lodash"]
#lodash

// any deps named "lodash" & within semver range ^"1.2.3"
#lodash@^1.2.3
// equivalent to...
[name="lodash"]:semver(^1.2.3)

// get the hoisted node for a given semver range
#lodash@^1.2.3:not(:deduped)

// querying deps with a specific version
#lodash@2.1.5
// equivalent to...
[name="lodash"][version="2.1.5"]

// has any deps
:has(*)

// deps with no other deps (ie. "leaf" nodes)
:empty

// manually querying git dependencies
[repository^=github:],
[repository^=git:],
[repository^=https://github.com],
[repository^=http://github.com],
[repository^=https://github.com],
[repository^=+git:...]

// querying for all git dependencies
:type(git)

// get production dependencies that aren't also dev deps
.prod:not(.dev)

// get dependencies with specific licenses
[license=MIT], [license=ISC]

// find all packages that have @ruyadorno as a contributor
:attr(contributors, [email=ruyadorno@github.com])
```

### Example Response Output

- an array of dependency objects is returned which can contain multiple copies of the same package which may or may not have been linked or deduped

```json
[
  {
    "name": "",
    "version": "",
    "description": "",
    "homepage": "",
    "bugs": {},
    "author": {},
    "license": {},
    "funding": {},
    "files": [],
    "main": "",
    "browser": "",
    "bin": {},
    "man": [],
    "directories": {},
    "repository": {},
    "scripts": {},
    "config": {},
    "dependencies": {},
    "devDependencies": {},
    "optionalDependencies": {},
    "bundledDependencies": {},
    "peerDependencies": {},
    "peerDependenciesMeta": {},
    "engines": {},
    "os": [],
    "cpu": [],
    "workspaces": {},
    "keywords": [],
    ...
  },
  ...
```

### Expecting a certain number of results

One common use of `npm query` is to make sure there is only one version of
a certain dependency in your tree.  This is especially common for
ecosystems like that rely on `typescript` where having state split
across two different but identically-named packages causes bugs.  You
can use the `--expect-results` or `--expect-result-count` in your setup
to ensure that npm will exit with an exit code if your tree doesn't look
like you want it to.


```sh
$ npm query '#react' --expect-result-count=1
```

Perhaps you want to quickly check if there are any production
dependencies that could be updated:

```sh
$ npm query ':root>:outdated(in-range).prod' --no-expect-results
```

### Package lock only mode

If package-lock-only is enabled, only the information in the package lock (or shrinkwrap) is loaded.  This means that information from the package.json files of your dependencies will not be included in the result set (e.g. description, homepage, engines).

### Configuration

<!-- AUTOGENERATED CONFIG DESCRIPTIONS -->
## See Also

* [dependency selectors](/using-npm/dependency-selectors)

