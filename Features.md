# ESNext Features to Support in CoffeeScript

Per the [README](./README.md), we have two main goals for the future of CoffeeScript: adding support for important ES2015+ (ESNext) features that CoffeeScript currently lacks, and changing CoffeeScript’s output to be ESNext code. See also the [project board for CoffeeScript 2.0.0](https://github.com/coffeescript6/discuss/projects/1).

Features are ordered by priority, which is determined (subjectively) by how important each feature is to the CoffeeScript community. The most important criterion is whether the lack of specific feature affects CoffeeScript’s viability for a potential project: if a developer must choose between CoffeeScript and a particular library or framework, whatever missing feature is causing that choice to need to be made is a top priority for us to fix. This list is based on the [issues](https://github.com/coffeescript6/discuss/issues) for this repo and the discussion in [#8](https://github.com/coffeescript6/discuss/issues/8).

## Top Priority

These features affect interopability and should take priority over all other features. These should be implemented ASAP in the current compiler, in addition to any new compiler that gets created later.

### Modules: `import` and `export` [(#7)](https://github.com/coffeescript6/discuss/issues/7)

> CoffeeScript 1.x and 2

[This has been merged](https://github.com/jashkenas/coffeescript/pull/4300) into the original CoffeeScript compiler. It will ship with the next release.

### Classes [(#22)](https://github.com/coffeescript6/discuss/issues/22)

> CoffeeScript 2 only

As part of CoffeeScript 2, we will revise CoffeeScript’s `class` syntax to be compatible with ECMAScript’s `class`; this means no code in class bodies, no using `this` in a constructor before calling `super`, etc. We will inherit the limitations of ECMAScript’s `class`, but the benefit of being able to extend them and generate native ES2015 classes. Getters and setters [(#17)](https://github.com/coffeescript6/discuss/issues/17) would need to be implemented as part of supporting ES classes.

### Tagged template literals [(#28)](https://github.com/coffeescript6/discuss/issues/28)

> CoffeeScript 1.x and 2

Some new libraries require support for ECMAScript template literals, which are incompatible with CoffeeScript’s backticks. Support would be added for `myTag"hello #{'wo'+'rld'}"` or `myTag"""some multiline string"""`. Such syntax is currently not allowed in CoffeeScript, so enabling support for it would not be a breaking change.

## Medium Priority

These features aren’t required for CoffeeScript to be used in any project, but there’s great desire in the community for these to be added.

### `async`/`await` [(#10)](https://github.com/coffeescript6/discuss/issues/10)

> CoffeeScript 1.x and 2

> An [old pull request](https://github.com/jashkenas/coffeescript/pull/3757) basically implements this feature exactly as we’ve outlined it below, though the PR contains an ES5 shim which should be removed.

`async`/`await` isn’t completely standardized yet; it’s not part of ES2015 or ES2016, though it is supported in Edge and in Chrome behind a flag. [It has reached Stage 4 of ES2017](https://github.com/tc39/proposals/blob/master/finished-proposals.md).

CoffeeScript’s version would add only the `await` keyword, which would automatically cause CoffeeScript to output an `async` keyword where needed. This behavior is similar to how generators are handled, where the presence of a `yield` keyword causes CoffeeScript to declare the function as `function*`: the presence of `await` would cause CoffeeScript to declare its associated function with `async`.

So to take the example from http://stackabuse.com/node-js-async-await-in-es7/, this JavaScript:

```js
var request = require('request-promise');

async function main() {  
  var body = await request.get('https://api.github.com/repos/scottwrobinson/camo');
  console.log('Body:', body);
}
main();
```

could be written in CoffeeScript as:

```coffee
request = require 'request-promise'

main = ->
  body = await request.get 'https://api.github.com/repos/scottwrobinson/camo'
  console.log 'Body:', body

main()
```

### Backticked blocks [(#42)](https://github.com/coffeescript6/discuss/issues/42)

> CoffeeScript 1.x and 2

Like triple quotes `"""` or `'''`, triple backticks ```` ``` ```` would delineate a block of passthrough JavaScript code. This would enable template literals to work within backticked blocks, and also provide a way to passthrough JavaScript code that might itself contain backticks (such as in Markdown comments, or in ES2015 template literals).

## Low Priority

These are nice-to-have features that should be implemented as time permits, probably only in the “new” compiler if one gets created. Any change that causes ES2015 output and isn’t opt-in needs to either be enabled by a flag or only in the new, ESNext-outputting compiler.

### Template literals [(#41)](https://github.com/coffeescript6/discuss/issues/41)

> CoffeeScript 2 only

Output CoffeeScript’s interpolated strings—`"hello, #{name}!"`—as ES2015 template literals: `` `hello, ${name}!` ``

### Fat arrows `=>` output as `=>` [(#8)](https://github.com/coffeescript6/discuss/issues/8)

> CoffeeScript 2 only

Fat arrows should be transpiled into ES2015 `=>`. They took our good idea, let’s celebrate by using it.

### Destructuring assignment [(#18)](https://github.com/coffeescript6/discuss/issues/18)

> CoffeeScript 2 only

### `for … of` [(#11)](https://github.com/coffeescript6/discuss/issues/11)

> CoffeeScript 2 only

There should be some way to output ESNext `for … of`, perhaps via a new `for` syntax. Awaiting consensus.

## Uncertain

These are features we’re not sure we will implement, in any version of CoffeeScript:

### Inferred `let` assignment [(#1)](https://github.com/coffeescript6/discuss/issues/1)

> CoffeeScript 2 only

When a variable isn’t declared using the new operator that produces `const` (see above), CoffeeScript should automatically declare it with `let` whenever possible.

### Block assignment `let` and `const` assignment operators [(#31)](https://github.com/coffeescript6/discuss/issues/31) or [(#35)](https://github.com/coffeescript6/discuss/issues/35)

> CoffeeScript 2 only

For whatever reason, `let` and `const` are among the most popular features introduced by ES2015. Awaiting consensus on which of these two proposals we want to adopt, if either. Per [#1](https://github.com/coffeescript6/discuss/issues/1), there seems to be consensus that we want some way to support `const` and probably `let` in CoffeeScript.

If we support `const` only, [(#31)](https://github.com/coffeescript6/discuss/issues/31), a new `:=` operator would be added, e.g. `a := 1` would become `const a = 1;`. This would give us the “throw an error if a `const`-declared variable is reassigned” feature of `const`, plus block-scoping but only for `const`s.

If we support `let` and `const`, [(#35)](https://github.com/coffeescript6/discuss/issues/35), which would be necessary if we want to gain the block-scope aspects of the new `let` and `const` keywords, we would need two new operators: `:=` for `let` and `:==` for `const`. The `:=`-defined variables would get their `let` declarations grouped together at the tops of their blocks.

## No Action

These are other features that have been discussed, but the consensus at the moment is that no action should be taken to implement them.

### Decorators [(#9)](https://github.com/coffeescript6/discuss/issues/9)

### Type annotations [(#12)](https://github.com/coffeescript6/discuss/issues/12)

