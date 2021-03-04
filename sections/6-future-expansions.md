<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
# Future Expansions <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

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

<https://cr.openjdk.java.net/~briangoetz/amber/pattern-match.html> <!-- .element: class="attribution" -->

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

### Guards

<pre data-id="guards-animation"><code class="java" data-trim data-line-numbers="1">
String apply(Effect effect, Guitar guitar) {
    return switch(effect) {
        // (...)
        case Tremolo tr-> String.format("Tremolo active with depth %d and rate %d.", tr.getDepth(), tr.getRate());
        case Tuner tu -> String.format("Tuner active with pitch %d. Muting all signal!", tu.getPitchInHz());
        case EffectLoop el -> el.getEffects().stream().map(this::apply).collect(Collectors.joining(System.lineSeparator()));
        default -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

<https://openjdk.java.net/jeps/8213076> <!-- .element: class="attribution" -->

note:
A *guard* is an additional boolean expression that must additionally be true in order for a pattern to match.
In this case we could use a guard to prevent unnecessary tuning...

---

<!-- .slide: data-auto-animate" -->

### Guards

<pre data-id="guards-animation"><code class="java" data-trim data-line-numbers="5">
String apply(Effect effect, Guitar guitar) {
    return switch(effect) {
        // (...)
        case Tremolo tr-> String.format("Tremolo active with depth %d and rate %d.", tr.getDepth(), tr.getRate());
        case Tuner tu && !tu.isInTune(guitar) -> String.format("Guitar is in need of tuning - tuner active with pitch %d. Muting all signal!", tu.getPitchInHz());
        case EffectLoop el -> el.getEffects().stream().map(this::apply).collect(Collectors.joining(System.lineSeparator()));
        default -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

note:
...like so.
Now the case block will only be executed if the effect is a `Tuner` and the `Guitar` is not in tune already.

---

### Array patterns

Constructing a <code>Guitar</code> array:

<pre><code class="java" data-trim data-line-numbers>
var lesPaul = new Guitar("Gibson Les Paul", GuitarType.LES_PAUL);
var strat = new Guitar("Fender Classic Strat", GuitarType.STRATOCASTER);
var guitarArray = new Guitar[] { lesPaul, strat };
</code></pre>

<span class="fragment">
Deconstructing a <code>Guitar</code> array of 2 elements:

<pre><code class="java" data-trim data-line-numbers>
if (guitarArray instanceof Guitar[] { var lesPaul, var strat }) {
    // use lesPaul, strat
}
</code></pre>
</span>

<span class="fragment">
Deconstructing a <code>Guitar</code> array of 2 or more elements:

<pre><code class="java" data-trim data-line-numbers>
if (guitarArray instanceof Guitar[] { var lesPaul, var strat, ... }) {
    // use lesPaul, strat (... binds nothing)
}
</code></pre>
</span>

<https://mail.openjdk.java.net/pipermail/amber-spec-experts/2021-January/002695.html> <!-- .element: class="attribution" -->

note:
(after the 2nd code example)
Of course arrays can have more than 2 elements, so we need a way to express this.

---

### Varargs patterns

Varargs are like arrays, so:

<pre><code class="java" data-trim data-line-numbers>
case GuitarCabinet(var lesPaul, var strat, var tele):
</code></pre>

<span class="fragment">
...could be syntactic sugar for:

<pre><code class="java" data-trim data-line-numbers>
case GuitarCabinet(Guitar[] { var lesPaul, var strat, var tele}):
</code></pre>
</span>

<span class="fragment">
...and to match 3 or more elements:
<pre><code class="java" data-trim data-line-numbers>
case GuitarCabinet(var lesPaul, var strat, var tele, ...}):
</code></pre>
</span>

<https://mail.openjdk.java.net/pipermail/amber-spec-experts/2021-January/002695.html> <!-- .element: class="attribution" -->

---

### Other planned expansions

<ul>
    <li class="fragment">Guard patterns:<br/><code>true(BooleanExpression)</code> or <code>false(BooleanExpression)</code>
    <li class="fragment">AND patterns:<br/><code>PatternOne&PatternTwo</code>
    <li class="fragment">Patterns in <code>catch</code> clauses:<br/><small>(will most likely complement multi-catch blocks)</small>
    <li class="fragment">Collection patterns
    <li class="fragment">Record patterns
</ul>

<https://mail.openjdk.java.net/pipermail/amber-spec-experts/2021-January/002758.html> <!-- .element: class="attribution" -->

note:
As you can see, the details are getting murkier by the minute.
This is because these are **very new ideas** and are likely to change. 
Or to not be ever implemented at all.
