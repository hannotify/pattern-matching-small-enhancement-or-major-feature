<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
# Future Expansions <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

note:

**Target time: 47m**

---

<!-- .slide: data-background="img/background/dragons.jpg" data-background-color="black" data-background-opacity="0.7" -->
## Here be super dragons! <!-- .element: class="stroke" -->

<blockquote class="explanation">
    We can't be sure that the following features will appear in Java as depicted, <strong>if at all</strong>.<br/>
    Proceed with caution!
</blockquote>

<https://www.pexels.com/photo/dragon-festival-during-nighttime-6068535/> <!-- .element: class="attribution" --> 

note:
This is like a disclaimer`+++`.

---

<!-- .slide: data-auto-animate" -->

### Pattern bind statements

<pre data-id="pattern-bind-statements-animation"><code class="java" data-trim data-line-numbers>
var reverb = new Reverb("ChamberReverb", 2);

__let Reverb(String name, int roomSize) = reverb;

// do something with name & roomSize
</code></pre>

<br/>
<small>
<a href="https://cr.openjdk.java.net/~briangoetz/amber/pattern-match.html">https://cr.openjdk.java.net/~briangoetz/amber/pattern-match.html</a>
</small>

note:
A pattern binding statement destructures a target using a pattern.
Here, we assert that the pattern will match, so we destructure the target and bind its components to new variables. 

BTW - the "double underscore" syntax is a convention used by the Java language designers to suggest that the syntax is merely a placeholder, for purposes of exposition. 
So we can't be sure at all that `let` will be the actual keyword for pattern bind statements.

---

<!-- .slide: data-auto-animate" -->

### Pattern bind statements

<pre data-id="pattern-bind-statements-animation"><code class="java" data-trim data-line-numbers="4">
var reverb = new Reverb("ChamberReverb", 2);

__let Reverb(String name, int roomSize) = reverb;
else throw new IllegalArgumentException("not a Reverb!");

// do something with name & roomSize
</code></pre>

note:
Now if we cannot guarantee the pattern will match, we can provide an else clause like this.

---

<!-- .slide: data-auto-animate" -->

## Array patterns

<pre><code class="java" data-trim data-line-numbers>
record EffectLoop(String name, int volume, Effect... effects) { }
</code></pre>

<pre data-id="array-pattern-animation"><code class="java" data-trim data-line-numbers>
static String apply(EffectLoop effectLoop) {}
    return switch(effectLoop) {
        case EffectLoop(var name, var volume) -> "Effect loop contains no effects.";
    } 
}
</code></pre>

note:


---

<!-- .slide: data-auto-animate" -->

## Array patterns

<pre><code class="java" data-trim data-line-numbers>
record EffectLoop(String name, int volume, Effect... effects) { }
</code></pre>

<pre data-id="array-pattern-animation"><code class="java" data-trim data-line-numbers>
static String apply(EffectLoop effectLoop) {}
    return switch(effectLoop) {
        case EffectLoop(var name, var volume) -> "Effect loop contains no effects.";
        case EffectLoop(_, _, var effect) -> "Effect loop contains exactly one effect.";
    } 
}
</code></pre>

note:


---

<!-- .slide: data-auto-animate" -->

## Array patterns

<pre><code class="java" data-trim data-line-numbers>
record EffectLoop(String name, int volume, Effect... effects) { }
</code></pre>

<pre data-id="array-pattern-animation"><code class="java" data-trim data-line-numbers>
static String apply(EffectLoop effectLoop) {}
    return switch(effectLoop) {
        case EffectLoop(var name, var volume) -> "Effect loop contains no effects.";
        case EffectLoop(_, _, var effect) -> "Effect loop contains exactly one effect.";
        case EffectLoop(_, _, var effect, ...) -> "Effect loop contains more than one effect.";
    } 
}
</code></pre>

note:

---

<!-- .slide: data-auto-animate" -->

## Array patterns

<pre><code class="java" data-trim data-line-numbers>
record EffectLoop(String name, int volume, Effect... effects) { }
</code></pre>

<pre data-id="array-pattern-animation"><code class="java" data-trim data-line-numbers>
static String apply(EffectLoop effectLoop) {}
    return switch(effectLoop) {
        case EffectLoop(var name, var volume) -> "Effect loop contains no effects.";
        case EffectLoop(_, _, var effect) -> "Effect loop contains exactly one effect.";
        case EffectLoop(_, _, var effect, ...) -> "Effect loop contains more than one effect.";
        case EffectLoop(_, _, var effect1, var effect2) -> "Effect loop contains exactly two effects.";
        case EffectLoop(_, _, var effect1, var effect2, ...) -> "Effect loop contains more than two effects.";
    } 
}
</code></pre>

note:

---

<!---.slide: data-visibility="hidden" -->

### Other ideas

<ul>
    <li>Deconstruction patterns for all classes</li>
    <li>Enhanced array patterns<br/><small><code>String[] { [8] -> var eighthElement, [9] -> var ninthElement}</code></small></li>
    <li>AND patterns</li>
    <li>Patterns in <code>catch</code> clauses</li>
    <li>Collection patterns</li>
</ul>

<br/>
<br/>
<small>
<a href="https://mail.openjdk.java.net/pipermail/amber-spec-experts/2021-January/002758.html">https://mail.openjdk.java.net/pipermail/amber-spec-experts/2021-January/002758.html</a>
</small>

note:
As you can see, the details are getting murkier by the minute.
This is because these are **very new ideas** and are likely to change. 
Or to not be ever implemented at all.

---

### Other ideas

<ul>
    <li class="fragment">AND patterns:<br/><code>PatternOne&PatternTwo</code>
    <li class="fragment">Patterns in <code>catch</code> clauses:<br/><small>(will most likely complement multi-catch blocks)</small>
    <li class="fragment">Collection patterns
</ul>

<br><br>
<small>
<a href="https://mail.openjdk.java.net/pipermail/amber-spec-experts/2021-January/002758.html">https://mail.openjdk.java.net/pipermail/amber-spec-experts/2021-January/002758.html</a>
</small>

note:
As you can see, the details are getting murkier by the minute.
This is because these are **very new ideas** and are likely to change. 
Or to not be ever implemented at all.
