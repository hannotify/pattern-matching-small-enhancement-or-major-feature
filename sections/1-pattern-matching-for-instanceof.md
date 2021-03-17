<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
# Pattern Matching for <!-- .element: class="stroke" -->
# `instanceof` <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

---

<!-- .slide: data-background="img/background/guitar-store-close-up.jpg" data-background-color="black" data-background-opacity="0.2" -->
![music-store-phase-1](diagrams/music-store-phase-1.puml.png "Music store class diagram")

<https://pxhere.com/en/photo/548063> <!-- .element: class="attribution" -->

note:

(introduce our 'local music store' code example)<br/>
All demo code is on Github - link will be on the final slide.

---

### Instanceof-and-cast

<pre><code class="java" data-trim data-line-numbers>
if (product instanceof Guitar) {
    Guitar lesPaul = 
                     (Guitar) product;
    // use lesPaul
}
</code></pre>

---


### Instanceof-and-cast

<pre><code class="java" data-trim data-line-numbers="1">
if (product instanceof Guitar) { // 1. is product a Guitar?
    Guitar lesPaul = 
                     (Guitar) product;
    // use lesPaul
}
</code></pre>

---


### Instanceof-and-cast

<pre><code class="java" data-trim data-line-numbers="3">
if (product instanceof Guitar) { // 1. is product a Guitar?
    Guitar lesPaul = 
                     (Guitar) product; // 2. perform conversion
    // use lesPaul
}
</code></pre>

---

### Instanceof-and-cast

<pre><code class="java" data-trim data-line-numbers="2">
if (product instanceof Guitar) { // 1. is product a Guitar?
    Guitar lesPaul = // 3. declare variable, bind value
                     (Guitar) product; // 2. perform conversion
    // use lesPaul
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Improve the situation

<pre data-id="improve-situation-animation"><code class="java" data-trim data-line-numbers>
if (product instanceof Guitar) { // 1. is product a Guitar?
    Guitar lesPaul = // 3. declare variable, bind value
                     (Guitar) product; // 2. perform conversion
    // use lesPaul
}
</code></pre>

note: 

This is a famous pattern and it is understood by all Java programmers.

But - drawbacks!

* verbose
* why should both the type test (1.) and the cast (2.) be necessary?
* the actual logic ('use lesPaul') seems less important, when it actually is the most important
* the `Guitar` type is repeated 3 times, which makes it difficult to maintain

The situation would improve dramatically if we could compress the 3 steps shown here into a single one.

---

<!-- .slide: data-auto-animate" -->

### Improve the situation

<pre data-id="improve-situation-animation"><code class="java" data-trim data-line-numbers>
if (product instanceof Guitar lesPaul) { 
    // use lesPaul
}
</code></pre>

note:

This is a *type pattern* (mark with mouse).

And it does 3 things at the same time:

1. testing
2. conditional extraction
3. binding to variable(s)

---

<!-- .slide: data-background="img/background/chalkboard.jpg" data-background-color="black" data-background-opacity="0.3" -->
## Type pattern <!-- .element: class="stroke" -->

<blockquote class="explanation">
    Consists of a predicate that specifies a type, along with a single binding variable.
</blockquote>

<https://www.pexels.com/photo/person-holding-white-chalk-625219/> <!-- .element: class="attribution" -->

note:

A type pattern consists of:

1. a predicate, that specifies a type (`Guitar`)
2. a binding variable (`lesPaul`)

It looks like a variable declaration (which is not an accident!).

---

<!-- .slide: data-background="img/background/chalkboard.jpg" data-background-color="black" data-background-opacity="0.3" -->
## Pattern matching <!-- .element: class="stroke" -->

<blockquote class="explanation">
    Allows the conditional extraction of components from objects to be expressed more concisely and safely.
</blockquote>

<https://www.pexels.com/photo/person-holding-white-chalk-625219/> <!-- .element: class="attribution" -->

note:

"Pattern matching for instanceof" is the first feature in Java that uses the pattern matching concept.
It "allows the conditional (...)"

It is not a new concept, a lot of programming languages support pattern matching:

* Haskell
* C#
* Erlang
* [Scala](https://docs.scala-lang.org/tour/pattern-matching.html)

---

## Declaring 'in the middle'

<pre><code class="java" data-trim data-line-numbers>
if (product instanceof Guitar lesPaul) { 
    // use lesPaul
}
</code></pre>

note:

So, let's get into some details.

`lesPaul` is an ordinary local variable.
But its declaration location is different.

We are used to local variables being declared "at the left margin", or as part of a for loop, for example.
But patterns declare local variables "in the middle" of a statement or expression, which may take a little time to get used to.

---

<!-- .slide: data-auto-animate" -->

## Scoping

**'Regular' local variable ('block scoping')**

* The block in which it is declared.

<pre data-id="block-scoping-animation"><code class="java" data-trim data-line-numbers>
void playTunedGuitar() { 
    Guitar lesPaul = new Guitar("Les Paul");

    if (!lesPaul.isInTune()) {
        Guitar fenderStrat = new Guitar("Fender Stratocaster");
        fenderStrat.play();
    }
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

## Scoping

**'Regular' local variable ('block scoping')**

* The block in which it is declared.

<pre data-id="block-scoping-animation"><code class="java" data-trim data-line-numbers>
void playTunedGuitar() { 
    Guitar lesPaul = new Guitar("Les Paul");

    if (!lesPaul.isInTune()) {
        Guitar fenderStrat = new Guitar("Fender Stratocaster");
        fenderStrat.play();
        // fenderStrat is in scope
    }
    // fenderStrat is not in scope
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

## Scoping

**Pattern binding variable ('flow scoping')**

* The set of places where it would definitely be assigned.

<pre data-id="flow-scoping-animation"><code class="java" data-trim data-line-numbers>
if (product instanceof Guitar lesPaul) {
    // can use lesPaul here
} else {
    // can't use lesPaul here
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

## Scoping

**Pattern binding variable ('flow scoping')**

* The set of places where it would definitely be assigned.

<pre data-id="flow-scoping-animation"><code class="java" data-trim data-line-numbers>
if (product instanceof Guitar lesPaul && lesPaul.isInTune()) {
    // can use lesPaul here
} else {
    // can't use lesPaul here
}

</code></pre>

---

<!-- .slide: data-auto-animate data-visibility="hidden" -->

## Scoping

**Pattern binding variable ('flow scoping')**

* The set of places where it would definitely be assigned.

<pre data-id="flow-scoping-animation"><code class="java" data-trim data-line-numbers>
if (product instanceof Guitar lesPaul 
        || lesPaul.isInTune()) {
    // ...
} else {
    // can't use lesPaul here
}
</code></pre>

---

<!-- .slide: data-auto-animate data-visibility="hidden" -->

## Scoping

**Pattern binding variable ('flow scoping')**

* The set of places where it would definitely be assigned.

<pre data-id="flow-scoping-animation"><code class="java" data-trim data-line-numbers>
if (product instanceof Guitar lesPaul 
        || lesPaul.isInTune()/* can't use lesPaul here */) {
    // ...
} else {
    // can't use lesPaul here
}
</code></pre>

---

<!-- .slide: data-auto-animate data-visibility="hidden" -->

## Scoping

**Pattern binding variable ('flow scoping')**

* The set of places where it would definitely be assigned.

<pre data-id="flow-scoping-animation"><code class="java" data-trim data-line-numbers>
if (product instanceof Guitar lesPaul 
        || lesPaul.isInTune()/* can't use lesPaul here */) {
    // can't use lesPaul here
} else {
    // can't use lesPaul here
}
</code></pre> -->

---

<!-- .slide: data-auto-animate" -->

## Scoping

**Pattern binding variable ('flow scoping')**

* The set of places where it would definitely be assigned.

<pre data-id="flow-scoping-2-animation"><code class="java" data-trim data-line-numbers>
boolean isTunedGuitar(Object product) {
	if (!(product instanceof Guitar lesPaul)) {
		return false;
    }

	// This code is only reachable if 'product' is
	// a Guitar, so 'lesPaul' is in scope.
	return lesPaul.isInTune();
}
</code></pre>

---

<!-- .slide: data-background="img/background/stompboxes.jpg" data-background-color="black" data-background-opacity="0.2" -->
![music-store-phase-2](diagrams/music-store-phase-2.puml.png "Music store class diagram")

<https://pxhere.com/en/photo/544037> <!-- .element: class="attribution" -->

note:

Let's introduce some more classes from the local music store.

---

<!-- .slide: data-auto-animate" -->

## Scoping

**Pattern binding variable ('flow scoping')**

* The set of places where it would definitely be assigned.

<pre data-id="flow-scoping-3-animation"><code class="java" data-trim data-line-numbers>
void test(Effect effect) {
	if (effect instanceof Reverb stockEffect)
		stockEffect.setRoomSize(25);
	else if (effect instanceof Delay stockEffect)
		stockEffect.setTimeInMs(200);
}
</code></pre>

note:

Finally, if the scopes of the variables don't overlap, for example in if-chains, you can reuse the same variable name within the same block.
There are two declarations of 'effect', but each is only in scope within "its own" branch and so there is no overlap and hence no conflict.

Fun fact: this would not have been possible with classic 'block scoping'.

---

<!-- .slide: data-background="img/background/marshall-amplifier.jpg" data-background-color="black" data-background-opacity="0.4" -->
![music-store-phase-3](diagrams/music-store-phase-3.puml.png "Music store class diagram")

<https://pxhere.com/en/photo/853260> <!-- .element: class="attribution" -->

note:
Let's connect `Guitar` with the effects.
We'll use an `Amplifier` and an `EffectLoop`.

---

<!-- .slide: data-background="img/background/binary-code.jpg" data-background-color="black" data-background-opacity="0.3" -->
## Demo

* Simplify implementation of `equals`
* Loop through a set of `Effect`s and apply 'pattern matching for instanceof'

<https://pxhere.com/en/photo/1458897> <!-- .element: class="attribution" -->

note:

* **Guitar.java** Simplify implementation of `equals`
* **AmplifierTest.java** Loop through a set of `Effect`s and apply 'pattern matching for instanceof'

---

## Benefits

* Nearly 100% of casts will just disappear! 
* More concise
* Eliminates cut/paste errors

---

## `instanceof` grammar

The `instanceof` grammar is extended accordingly:

<pre><code class="java" data-trim data-line-numbers="4,6,7">
RelationalExpression:
  ...
  RelationalExpression instanceof ReferenceType
  RelationalExpression instanceof Pattern

Pattern:
  ReferenceType Identifier
</code></pre>

<https://openjdk.java.net/jeps/305> <!-- .element: class="attribution" -->

---

<!-- .slide: data-background="img/background/freddie-mercury.jpg" data-background-color="black" data-background-opacity="0.4" -->
## It's a kind of Pattern <!-- .element: class="stroke" -->

<blockquote class="explanation">
    <strong>type pattern</strong>
    <br/>
    <code data-trim data-noescape>
    Guitar lesPaul
    </code>
</blockquote>

<https://www.pexels.com/photo/gray-metal-statue-of-man-raising-hand-near-dock-825430/> <!-- .element: class="attribution" -->

---

## Feature Status

<table style="font-size: 100%">
    <thead>
        <tr>
            <th>Java version</th>
            <th>Feature status</th>
            <th>JEP</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><strong>14</strong></td>
            <td>Preview</td>
            <td><a href="https://openjdk.java.net/jeps/305">JEP 305</a></td>
        </tr>
        <tr>
            <td><strong>15</strong></td>
            <td>Second preview</td>
            <td><a href="https://openjdk.java.net/jeps/375">JEP 375</a></td>
        </tr>
        <tr>
            <td><strong>16</strong></td>
            <td>Final</td>
            <td><a href="https://openjdk.java.net/jeps/394">JEP 394</a></td>
        </tr>
    </tbody>
</table>

---

<!-- .slide: data-background="img/background/joker.jpg" data-background-color="black" data-background-opacity="0.4" -->
## Why so serious?

<ul>
    <li class="fragment"><em>Surely</em> a less invasive approach exists?</li>
    <li class="fragment"><strong>Flow typing</strong> has been considered.</li>
    <li class="fragment">It infers refined types based on past conditionals.</li>
    <li class="fragment">But... it is suited for <code>instanceof</code> checks only.</li>
    <li class="fragment">And pattern matching can be useful for more language concepts!</li>
</ul>

<https://pxhere.com/en/photo/835435> <!-- .element: class="attribution" -->

note:

But why such a serious, generic solution?
"Gwhy... So... Seriousa?"
Surely a less invasive approach exists?
Yes, it does. And it has been considered by the languange designers.
_Flow typing_ is a proposed mechanism that infers refined types based on past conditionals.
Sounds useful, right?
But... it is suited for `instanceof` checks only.
Whereas *pattern matching* can do the same, **and** be useful for more language concepts.
Such as switch expressions!
