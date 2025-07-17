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

Specifically for optional parameters [here](<handbook/2/functions.html#optional-parameters>).

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

# 2 Investigations

## 2.1 On functional programming alternatives to typescript

2025-07-13 Wk 28 Sun - 11:06

Trying to remember some language that compiles down to javascript I've seen before...

From this [reddit post](<https://www.reddit.com/r/typescript/comments/o2sn3p/which_is_the_best_functional_programming_library/>),

[fp-ts](<https://gcanti.github.io/fp-ts/>), [effect-ts](<https://github.com/Effect-TS/effect>)

---

Languages,

[elm](<https://elm-lang.org/>), [pure script](<https://www.purescript.org/>), 

I found it! It was [ReasonML](<https://reasonml.github.io/>)!


# 3 Issues

## 3.1 No trigger on text grep using MutationObserver

- [ ] 

2025-07-13 Wk 28 Sun - 19:36

This works but if the string contains `|` or `@` it does not seem to.


# 4 References

1. [esbuild docs](<https://esbuild.github.io/getting-started/>) ^1
2. [greasemonkey manual](<https://wiki.greasespot.net/Greasemonkey_Manual>) ^2

**Search**
[forum post on use of greasemonkey alternatives](<https://news.ycombinator.com/item?id=26057106>).