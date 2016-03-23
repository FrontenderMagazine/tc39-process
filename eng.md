The most recent release of ECMAScript, ES6, is large and was standardized almost
6 years after ES5 (December 2009 vs. June 2015). There are two main problems 
with so much time passing between releases:

*   Features that are ready sooner than the release have to wait until the
    release is finished.
   
*   Features that take long are under pressure to be wrapped up, because
    postponing them until the next release would mean a long wait. Such features may
    also delay a release.
   

Therefore, starting with ECMAScript 2016 (ES7), releases will happen more
frequently and be much smaller as a consequence. There will be one release per 
year and it will contain all features that are finished by a yearly deadline.

This blog post describes the details of this new release process, along with
four features that will probably be in ECMAScript 2016.

 
Before we get started, let’s refresh our memories: who or what is TC39?

[TC39 (Ecma Technical Committee 39)][1] is the committe that evolves JavaScript
. Its members are companies (among others, all major browser vendors
). [TC39 meets regularly][2], its meetings are attended by delegates that
members send and by invited experts. Minutes of the meetings are
[available online][3] and give you a good idea of how TC39 works.

Occasionally (even in this blog post), you’ll see the term *TC39 member*
referring to a human. Then it means: a delegate sent by a TC39 member company.

It is interesting to note that TC39 operates by consensus: Decisions are only
made if every member (as represented by its delegates) agrees.

### The TC39 process {#the_tc39_process}

Each proposal for an ECMAScript feature goes through the following *maturity
stages*, starting with stage 0. The progression from one stage to the next one
must be approved by TC39.

#### Stage 0: strawman {#stage_0%3A_strawman}

**What is it?** A free-form way of submitting ideas for evolving ECMAScript.
Submissions must come either from a TC39 member or a non-member who
[has registered as a TC39 contributor][4].

**What’s required?** The document must be reviewed at a TC39 meeting (
[source][5]) and is then added to [the page with stage 0 proposals][6].

#### Stage 1: proposal {#stage_1%3A_proposal}

**What is it?** A formal proposal for the feature.

**What’s required?** A so-called *champion* must be identified who is
responsible for the proposal. Either the champion or a co-champion must be a 
member of TC39
([source][5]). The problem solved by the proposal must be described in prose.
The solution must be described via examples, an API and a discussion of 
semantics and algorithms. Lastly, potential obstacles for the proposal must be 
identified, such as interactions with other features and implementation 
challenges. Implementation-wise, polyfills and demos are needed.

**What’s next?** By accepting a proposal for stage 1, TC39 declares its
willingness to examine, discuss and contribute to the proposal. Going forward, 
major changes to the proposal are expected.

#### Stage 2: draft {#stage_2%3A_draft}

**What is it?** A first version of what will be in the specification. At this
point, an eventual inclusion of the feature in the standard is likely.

**What’s required?** The proposal must now additionally have a formal
description of the syntax and semantics of the feature (using the formal 
language of the ECMAScript specification). The description should be as complete
as possible, but can contain todos and placeholders. Two experimental 
implementations of the feature are needed, but one of them can be in a 
transpiler such as Babel.

**What’s next?** Only incremental changes are expected from now on.

**What is it?** The proposal is mostly finished and now needs feedback from
implementations and users to progress further.

**What’s required?** The spec text must be complete. Designated reviewers (
appointed by TC39, not by the champion) and the ECMAScript spec editor must sign
off on the spec text. There must be at least two spec-compliant implementations
(which don’t have to be enabled by default
).

**What’s next?** Henceforth, changes should only be made in response to
critical issues raised by the implementations and their use.

#### Stage 4: finished {#stage_4%3A_finished}

**What is it?** The proposal is ready to be included in the standard.

**What’s required?** The following things are needed before a proposal can
reach this stage:

*   [Test 262][7] acceptance tests (roughly, unit tests for the language
    feature, written in JavaScript
    ).
*   Two spec-compliant shipping implementations that pass the tests.
*   Significant practical experience with the implementations.
*   The ECMAScript spec editor must sign off on the spec text.

**What’s next?** The proposal will be included in the ECMAScript specification
as soon as possible. When the spec goes through its yearly ratification as a 
standard, the proposal is ratified as part of it.

### Don’t call them ECMAScript features {#dont_call_them_ecmascript_features}

As you can see, you can only be sure that a feature will be included in the
standard once its proposal has reached stage 4. Then its inclusion in the next 
ECMAScript release is probable, but not 100% sure, either (it may take longer). 
Therefore, you can’t call proposals (e.g.) “ES7 features” or “ES2016 features”, 
anymore. My two favorite ways of writing headings for articles and blog posts 
are therefore:

*   “ECMAScript proposal: the foo feature”. The stage of the proposal is
    mentioned at the beginning of the article.
   
*   “ES.stage2: the foo feature”

If a proposal is at stage 4, I’d be OK with calling it an ES20xx feature, but
it’s safest to wait until the spec editor confirms what release it will be 
included in.`Object.observe` is an example of an ECMAScript proposal that had
progressed until stage 2, but was ultimately withdrawn.

### ECMAScript stage 3 proposals {#ecmascript_stage_3_proposals}

Chances are good that the following features will be in ECMAScript 2016.

#### Exponentiation Operator (Rick Waldron) {#exponentiation_operator_%
28rick_waldron%29
}

The proposed feature is `**` as an infix operator for exponentiation:

    x ** y
    

is produces the same result as

    Math.pow(x, y)
    

Examples:

    let squared = 3 ** 2; // 9
        
        let num = 3;
        num **= 2;
        console.log(num); // 9
    

Further reading:

*   [Exponentiation Operator][8] (Rick Waldron)

#### `Array.prototype.includes` (Domenic Denicola, Rick Waldron) {#array.
prototype.includes_%28domenic_denicola_rick_waldron%29
}

The Array method `includes` has the following signature:

    Array.prototype.includes(value : any) : boolean
    

It returns `true` if `value` is an element of its receiver (`this`) and `false`

    > ['a', 'b', 'c'].includes('a')
        true
        > ['a', 'b', 'c'].includes('d')
        false
    

`includes` is similar to `indexOf` – the following two expressions are mostly
equivalent:

    arr.includes(x)
        arr.indexOf(x) >= 0
    

The only exception is that `includes()` finds `NaN`, whereas `indexOf()` doesn
’t:

    > [NaN].includes(NaN)
        true
        > [NaN].indexOf(NaN)
        -1
    

`includes` does not distinguish between `+0` and `-0` (
[which is how almost all of JavaScript works][9]):

    > [-0].includes(+0)
        true
    

Typed Arrays will also have a method `includes()`:

    let tarr = Uint8Array.of(12, 5, 3);
        console.log(tarr.includes(5)); // true
    

##### Frequently asked questions {#frequently_asked_questions}

*   **Why is the method called `includes` and not `contains`?**  
    The latter was the initial choice, but that broke code on the web (
    [MooTools adds this method to `Array.prototype`][10]).

*   **Why is the method called `includes` and not `has`?**  
    `has` is used for keys (`Map.prototype.has`), `includes` is used for
    elements
    (`String.prototype.includes`). The elements of a Set can be viewed as being
    both keys and values, which is why there is a
   `Set.prototype.has` (and no `includes`).
*   **[The ES6 method `String.prototype.includes`][11] works with strings, not
    characters. Isn’t that inconsistent w.r.t.
   `Array.prototype.includes`?**  
    If Array `includes` worked exactly like string `includes`, it would accept
    arrays, not single elements. But the two
   `includes` follow the example of `indexOf`; characters are seen as a special
    case and strings with arbitrary lengths as the general case.
   

##### Further reading {#further_reading}

*   [`Array.prototype.includes`][12] (Domenic Denicola, Rick Waldron)

#### SIMD.JS – SIMD APIs + polyfill (John McCutchan, Peter Jensen, Dan Gohman
, Daniel Ehrenberg
) {#simd.js_%E2%80%93_simd_apis_%2B_polyfill_%
28john_mccutchan_peter_jensen_dan_gohman_daniel_ehrenberg%29
}

SIMD stands for “single instruction, multiple data”, the ability of a CPU
to apply an operand to vectors of values (instead of single values). One popular
example of a SIMD instruction set is[SSE (Streaming SIMD Extensions)][13], as
supported by Intel processors.

This is a short code example, consult the sources below for more information.

    var a = SIMD.float32x4(1.0, 2.0, 3.0, 4.0);
        var b = SIMD.float32x4(5.0, 6.0, 7.0, 8.0);
        var c = SIMD.float32x4.add(a,b);
    

Further reading:

#### Async Functions (Brian Terlson) {#async_functions_%28brian_terlson%29}

Before I can explain async functions, I need to explain how Promises and
generators can be combined to perform asynchronous operations via synchronous-
looking code.

##### Writing async code via Promises and generators {#
writing_async_code_via_promises_and_generators
}

For functions that compute their one-off results asynchronously, Promises,
which are part of ES6, are becoming increasingly popular. One example is
[the client-side `fetch` API][14], which is an alternative to XMLHttpRequest
for retrieving files. Using it looks as follows:

    function fetchJson(url) {
            return fetch(url)
            .then(request => request.text())
            .then(text => {
                return JSON.parse(text);
            })
            .catch(error => {
                console.log(`ERROR: ${error.stack}`);
            });
        }
        fetchJson('http://example.com/some_file.json')
        .then(obj => console.log(obj));
    

co is a library that uses Promises and generators to enable a coding style that
looks more synchronous, but works the same as the style used in the previous 
example:

    const fetchJson = co(function* () {
            try {
                let request = yield fetch(url);
                let text = yield request.text();
                return JSON.parse(text);
            }
            catch (error) {
                console.log(`ERROR: ${error.stack}`);
            }
        });
    

Every time the callback (a generator function!) yields a Promise to co, the
callback gets suspended. Once the Promise is settled, co resumes the callback: 
if the Promise was fulfilled,`yield` returns the fulfillment value, if it was
rejected,`yield` throws the rejection error. Additionally, co promisifies the
result returned by the callback (similarly to how`then()` does it).

##### Async functions {#async_functions}

Async functions are basically dedicated syntax for what co does:

    async function fetchJson(url) {
            try {
                let request = await fetch(url);
                let text = await request.text();
                return JSON.parse(text);
            }
            catch (error) {
                console.log(`ERROR: ${error.stack}`);
            }
        }
    

Internally, async functions work much like generators, but they are not
translated to generator functions.

##### Variants {#variants}

The following variants of async functions exist:

*   Async function declarations: `async function foo() {}` 
*   Async function expressions: `const foo = async function () {};` 
*   Async method definitions: `let obj = { async foo() {} }` 
*   Async arrow functions: `const foo = async () => {};` 

##### Further reading {#further_reading_2}

### Further reading {#further_reading_3}

The following were important sources of this blog post:

Other things to read:

 [1]: http://www.ecma-international.org/memento/TC39.htm
 [2]: http://www.ecma-international.org/memento/TC39-M.htm
 [3]: https://github.com/tc39/tc39-notes

 [4]: http://www.ecma-international.org/memento/contribute_TC39_Royalty_Free_Task_Group.php
 [5]: https://github.com/tc39/ecma262/blob/master/FAQ.md
 [6]: https://github.com/tc39/ecma262/blob/master/stage0.md
 [7]: http://test262.ecmascript.org/
 [8]: https://github.com/rwaldron/exponentiation-operator
 [9]: http://speakingjs.com/es5/ch11.html#two_zeros

 [10]: https://esdiscuss.org/topic/having-a-non-enumerable-array-prototype-contains-may-not-be-web-compatible

 [11]: http://exploringjs.com/es6/ch_strings.html#leanpub-auto-checking-for-containment-and-repeating-strings
 [12]: https://github.com/tc39/Array.prototype.includes/
 [13]: https://en.wikipedia.org/wiki/Streaming_SIMD_Extensions
 [14]: https://fetch.spec.whatwg.org/#concept-request