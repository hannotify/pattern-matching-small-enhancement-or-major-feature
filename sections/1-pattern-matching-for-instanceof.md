<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
# Pattern Matching for <!-- .element: class="stroke" -->
# `instanceof` <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

---

<!-- .slide: data-background="img/background/guitar-store-close-up.jpg" data-background-color="black" data-background-opacity="0.2" -->
![music-store-phase-1](diagrams/music-store-phase-1.puml.png "Music store class diagram")

<https://pxhere.com/en/photo/548063> <!-- .element: class="attribution" -->

note:

Introduce our 'music store' code example.<br/>
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

This is a famous pattern, both straightforward and understood by all Java programmers.

But - drawbacks!

* verbose
* why should both the type test (1.) and the cast (2.) be necessary?
* the actual logic ('use lesPaul') seems less important
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

`lesPaul` is an ordinary local variable.
But its declaration location is different.

We are used to local variables being declared "at the left margin", or as part of a for loop, for example.
Patterns declare local variables "in the middle" of a statement or expression, which may take a little time to get used to.

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

<!-- .slide: data-auto-animate" -->

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

<!-- .slide: data-auto-animate" -->

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

<!-- .slide: data-auto-animate" -->

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
</code></pre>

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

	return lesPaul.isInTune();
}
</code></pre>

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

...

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

Finally, if the variables' scopes don't overlap, for example in if-chains, you can reuse the same variable name within the same block.

There are two declarations of 'effect', but each is only in scope within "its own" branch and so there is no overlap and hence no conflict.
I don't think reusing the same variable name is a good idea, though. 
Just because it's possible doesn't mean we have to do it. 😉

---

(...)

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

A **type pattern**:

* consists of a *type* and a *variable name*
* makes the variable accessible in the following code block 

<small>(but only when the passed value is of the given type)<small>

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
        <tr class="fragment">
            <td><strong>15</strong></td>
            <td>Second preview</td>
            <td><a href="https://openjdk.java.net/jeps/375">JEP 375</a></td>
        </tr>
        <tr class="fragment">
            <td><strong>16</strong></td>
            <td>Final</td>
            <td><a href="https://openjdk.java.net/jeps/394">JEP 394</a></td>
        </tr>
    </tbody>
</table>

