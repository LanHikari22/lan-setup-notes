---
status: pend
---
#external 

2025-07-17 Wk 29 Thu - 05:07
# 1 Journal

2025-07-13 Wk 28 Sun - 08:32

## 1.1 Importing a common typescript library

- [x] 

2025-07-13 Wk 28 Sun - 08:49

in [build.sh](<https://github.com/LanHikari22/lan-exp-scripts/blob/main/scripts/2025/Wk28-001-greasemonkey/build.sh>), we use `esbuild` to turn typescript to javascript to be used as Greasemonkey scripts. Over in typescript, we would like to be able to import common scripts.

In [esbuild docs](<https://esbuild.github.io/getting-started/>) [[#^1]], 

Import looks like:

```typescript
import * as esbuild from 'esbuild'
import * as Server from 'react-dom/server'
import * as esbuild from 'https://deno.land/x/esbuild@v0.25.6/mod.js'
```


Import syntax is explained [here](<https://www.typescriptlang.org/docs/handbook/2/modules.html>).

2025-07-13 Wk 28 Sun - 10:44

This works. Also [build.sh](<https://github.com/LanHikari22/lan-exp-scripts/blob/main/scripts/2025/Wk28-001-greasemonkey/build.sh>) is able to create a single js file out of all these imports for greasemonkey.

## 1.2 Marking typescript functions incomplete 

- [x] 

2025-07-13 Wk 28 Sun - 10:35

I would like to have something like `todo!()` from rust.

We could use `_notImplemented` from this [forum answer](<https://stackoverflow.com/a/54100211/6944447>).


## 1.3 Typescript syntax for default parameters

- [x] 

2025-07-13 Wk 28 Sun - 10:49

General typescript documentation is [here](<https://www.typescriptlang.org/>). 

Specifically for optional parameters `handbook/2/functions.html#optional-parameters`.

There is a `?` operator that marks variables optional. `x? : number` seems to signify `x? : number | undefined`. 

## 1.4 Logging objects in typescript and networking packets in userscript

- [x] We can get object params and infer type
- [x] Seems not possible to get specific JSON response details with userscript

2025-07-13 Wk 28 Sun - 19:39

```typescript
log_info(`${JSON.stringify(e)}`);
```

Trying to know the type of `e` in 

```typescript
  const observer = new PerformanceObserver((list) => {
    list //_
      .getEntries()
      .forEach((e) => {
[...]
```

We see it is of type `PerformanceEntry` but this is just the base type. We also know it has `e.initiatorType`.

```sh
rg 'extends PerformanceEntry' /usr/share/code/resources/app/extensions/node_modules/typescript/lib
```

```
/usr/share/code/resources/app/extensions/node_modules/typescript/lib/lib.webworker.d.ts
4842:interface PerformanceMark extends PerformanceEntry {
4857:interface PerformanceMeasure extends PerformanceEntry {
4904:interface PerformanceResourceTiming extends PerformanceEntry {

/usr/share/code/resources/app/extensions/node_modules/typescript/lib/lib.dom.d.ts
15392:interface LargestContentfulPaint extends PerformanceEntry {
18090:interface PerformanceEventTiming extends PerformanceEntry {
18113:interface PerformanceMark extends PerformanceEntry {
18128:interface PerformanceMeasure extends PerformanceEntry {
18247:interface PerformancePaintTiming extends PerformanceEntry {
18260:interface PerformanceResourceTiming extends PerformanceEntry {
```

```sh
rg 'initiatorType' /usr/share/code/resources/app/extensions/node_modules/typescript/lib
```

```
/usr/share/code/resources/app/extensions/node_modules/typescript/lib/lib.webworker.d.ts
4919:    /** [MDN Reference](https://developer.mozilla.org/docs/Web/API/PerformanceResourceTiming/initiatorType) */
4920:    readonly initiatorType: string;

/usr/share/code/resources/app/extensions/node_modules/typescript/lib/lib.dom.d.ts
18275:    /** [MDN Reference](https://developer.mozilla.org/docs/Web/API/PerformanceResourceTiming/initiatorType) */
18276:    readonly initiatorType: string;
```

`PerformanceResourceTiming`.

Docs: [PerformanceObserver](<https://developer.mozilla.org/en-US/docs/Web/API/PerformanceObserver>). [PerformanceResourceTiming](<https://developer.mozilla.org/en-US/docs/Web/API/PerformanceResourceTiming>).

This [answer](<https://stackoverflow.com/a/11597827/6944447>) has some resources for reading AJAX with Greasemonkey.

2025-07-13 Wk 28 Sun - 21:10

This [reddit post](<https://www.reddit.com/r/GreaseMonkey/comments/1afkjax/tampermonkey_read_https_requests/>) claims that getting access to detailed network information in a userscript is not possible. But it may only be possible via scripts.

## 1.5 Reading entities by xpath

2025-07-13 Wk 28 Sun - 21:29

[this forum answer](<https://stackoverflow.com/a/266337/6944447>) gives an example on evaluating xpath to get html items.

This can also be used in the web console.

```typescript
document.evaluate("YOUR_XPATH_HERE", document, null, XPathResult.FIRST_ORDERED_NODE_TYPE, null)
```

We can also see in typescript that we get an `HtmlElement` for `singleNodeValue`:

```sh
rg 'innerText' /usr/share/code/resources/app/extensions/node_modules/typescript/lib
```

```
/usr/share/code/resources/app/extensions/node_modules/typescript/lib/lib.dom.d.ts
10717:    /** [MDN Reference](https://developer.mozilla.org/docs/Web/API/HTMLElement/innerText) */
10718:    innerText: string;
```

2025-07-15 Wk 29 Tue - 04:33

`HTMLElement` does not have `value`. But some do like textareas. `HtmlInputElement` does.

## 1.6 Creating command code that runs every T ms

- [x] Interpret command from textarea every T ms 
- [x] Erase text in textarea once proceessed

2025-07-14 Wk 29 Mon - 19:14

From [this answer](<https://stackoverflow.com/a/3138784/6944447>), we can use

```typescript
setInterval(function, delay)
```

2025-07-15 Wk 29 Tue - 09:29

Had to use

```sh
elem.dispatchEvent(new Event("input", { bubbles: true }));
```

When editing textareas, or the change shows visually but seems to be overwritten on next action. 

Just setting `.value` and reading textarea value every n ms seems sufficient for this.

## 1.7 Creating Command that can be summoned via console to retrieve information

- [ ] Able to invoke a command from the web console
- [ ] Able to fetch data to a file to be downloaded

2025-07-26 Wk 30 Sat - 04:27
 
 [Greasemonkey manual](https://wiki.greasespot.net/Greasemonkey_Manual) [[#^2]].

We're using this build script currently to generate a single `app.js` to be used in GreaseMonkey from our typescript project:

```sh
#!/bin/bash

SCRIPT_DIR=$(dirname "$(readlink -f "$0")")

build() {
    basename="$1"

    npx esbuild "$basename.ts" --bundle --format=iife --outfile=build/$basename.body.js
    awk '/\/\/ ==\/UserScript==/ { print; exit } { print }' $basename.ts > build/$basename.js
    echo >> build/$basename.js
    cat build/$basename.body.js >> build/$basename.js
    rm build/$basename.body.js
}

pushd $SCRIPT_DIR

build app

popd
```

Which generates a file that looks like

```js
// ==UserScript==
// @name         some_app_naame
// @namespace    some_namespace
// @version      1.0
// @description  Some App description
// @author       Lan
// @match        *://*/*
// @grant        none
// ==/UserScript==

(() => {
  // ../../../../../cloned/gh/LanHikari22/lan-exp-scripts/scripts/2025/persistent/000-greasemonkey/common.ts
  var DEFAULT_VERBOSE = true;
  function stamp() {
    return (/* @__PURE__ */ new Date()).toLocaleString(void 0, { hour12: false });
  }
  function log_info(s) {
    console.log(`${stamp()} - INFO - ${s}`);
  }
  // ...

  // my_app.ts
  // ...

  // some_module.ts
  // ...
  
  // some_other_module.ts
  function on_interval() {
    const typed = read_textarea();
    if (typed == void 0) {
      return;
    }
    if (typed.startsWith("/hello")) {
      log_info("Hello!");
      write_textarea("You said Hi!");
    }
	// ...
  }
  function main() {
    watch_on_dom_text_change(
      ["Some Match Text"],
      () => click_button("SomeButton")
    );
    watch_on_dom_text_change(
      [
        //_
        "This text",
        "That text",
        "This other text"
      ],
      //_
      //   .map((item) => `Preserved commented out map?`)
      () => {
        click_button("Button1");
        click_button("Button2);
      }
    );
    setInterval(on_interval, 1e3);
  }
  (function() {
    "use strict";
    log_info(`TRIGGER`);
    main();
  })();
})();
```


None  of those functions are exposed to us in web_console. For example, we cannot use `log_info`.

Let's try to add this to the top after the UserScript comment

```diff
// ==UserScript==
// @name         some_app_naame
// @namespace    some_namespace
// @version      1.0
// @description  Some App description
// @author       Lan
// @match        *://*/*
// @grant        none
// ==/UserScript==
+ 
+ function do_I_exist() {
+   console.log("I do exist!");
+ }
```

We cannot access `do_I_exist()` not even if we make the function an export.

2025-07-26 Wk 30 Sat - 04:55

2025-07-30 Wk 31 Wed - 14:32

The resolution of this [issue](https://github.com/delta-domain-rnd/delta-trace/blob/main/lan/topics/practice/ctf/topic/entries/2025/000%20Solving%20Mountain%20and%20Dragon%20CTF.md#43-firefox-cannot-establish-connection-to-server-at-localhost) can help us here. We should be able to setup a command line interface via websockets.


### 1.7.1 Pend


# 2 Tasks

## 2.1 Create a websocket console to issue commands from

- [ ] 

2025-08-05 Wk 32 Tue - 04:57

Previously we just used some textbox to issue commands. But we can do better with websockets.

The script implementing these commands is private, so we will report mostly status here. But the code and method are similar to this [puzzle issue](https://github.com/delta-domain-rnd/delta-trace/blob/main/lan/topics/practice/ctf/topic/entries/2025/000%20Solving%20Mountain%20and%20Dragon%20CTF.md#43-firefox-cannot-establish-connection-to-server-at-localhost).

Our starting commands:

| Command     | Purpose                                                                                         |
| ----------- | ----------------------------------------------------------------------------------------------- |
| `/hello`    | Testing                                                                                         |
| `/help`     | Displays what the commands are and what they do                                                 |
| `/connect`  | Starts a new websocket client over a port 3005. Will be used for sending data.                  |
| `/download` | A command to download some structured data from the website. Received over websocket port 3005. |

2025-08-05 Wk 32 Tue - 05:34

We would like our `on_update` for web_control to run continuously. We run `start` once, and then update regularly. In main, we add

```ts
wctrl.start()
setInterval(wctrl.on_update, 100);
```

The reason is that in other circumstances `on_update` may be externally provided synchronized to frame draw.

### 2.1.1 Pend

# 3 Issues

## 3.1 No trigger on text grep using MutationObserver

- [x] Archived

2025-07-13 Wk 28 Sun - 19:36

This works but if the string contains `|` or `@` it does not seem to.

### 3.1.1 Archived

# 4 HowTos

## 4.1 Modify git email and name for a commit

- [x] 

2025-08-05 Wk 32 Tue - 05:01

From this [stackoverflow answer](https://stackoverflow.com/a/1320317),

```sh
git commit --amend --no-edit --reset-author
```

This should allow the last commit to get the updated email/name.

# 5 Investigations

## 5.1 On functional programming alternatives to typescript

2025-07-13 Wk 28 Sun - 11:06

Trying to remember some language that compiles down to javascript I've seen before...

From this [reddit post](<https://www.reddit.com/r/typescript/comments/o2sn3p/which_is_the_best_functional_programming_library/>),

[fp-ts](<https://gcanti.github.io/fp-ts/>), [effect-ts](<https://github.com/Effect-TS/effect>)

---

Languages,

[elm](<https://elm-lang.org/>), [pure script](<https://www.purescript.org/>), 

I found it! It was [ReasonML](<https://reasonml.github.io/>)!

# 6 External Links

Referenced by
1. [Solving Mountain and Dragon CTF](https://github.com/delta-domain-rnd/delta-trace/blob/main/lan/topics/practice/ctf/topic/entries/2025/000%20Solving%20Mountain%20and%20Dragon%20CTF.md)

# 7 References

1. [esbuild docs](<https://esbuild.github.io/getting-started/>) ^1
2. [greasemonkey manual](<https://wiki.greasespot.net/Greasemonkey_Manual>) ^2

**Search**
[forum post on use of greasemonkey alternatives](<https://news.ycombinator.com/item?id=26057106>).