<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->

## Pattern Matching Plays Nice With

# Sealed Types and Records <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

note:

**Target time: 29m**

---

<!-- .slide: data-background="img/background/binary-code.jpg" data-background-color="black" data-background-opacity="0.3" -->

## Demo

- Make `Effect` a sealed type

<https://pxhere.com/en/photo/1458897> <!-- .element: class="attribution" -->

note:

- All implementations of a sealed type must be `final`, `sealed` or `non-sealed` ([more info on non-sealed](https://stackoverflow.com/questions/63860110/what-is-the-point-of-extending-a-sealed-class-with-a-non-sealed-class))

Example of non-sealed: `UserCustomEffect`.

---

<!-- .slide: data-auto-animate" -->

### Sealed types yield completeness

<pre data-id="exhaustiveness-animation"><code class="java" data-trim data-line-numbers>
static String apply(Effect effect) {
    return switch(effect) {
        case Delay(int timeInMs) -> String.format("Delay active of %d ms.", timeInMs);
        case Reverb(String name, int roomSize) -> String.format("Reverb active of type %s and roomSize %d.", name, roomSize);
        case Overdrive(int gain) -> String.format("Overdrive active with gain %d.", gain);
        case Tremolo(int depth, int rate) -> String.format("Tremolo active with depth %d and rate %d.", depth, rate);
        case Tuner(int pitchInHz) -> String.format("Tuner active with pitch %d. Muting all signal!", pitchInHz);
        case EffectLoop(Tuner(int pitchInHz), _) -> String.format("The EffectLoop contains a tuner with pitch %d. Muting all signal!", pitchInHz);
        case EffectLoop(var effects) -> effects.stream().map(Effect::apply).collect(Collectors.joining(System.lineSeparator()));
        default -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Sealed types yield completeness

<pre data-id="exhaustiveness-animation"><code class="java" data-trim data-line-numbers>
static String apply(Effect effect) {
    return switch(effect) {
        case Delay(int timeInMs) -> String.format("Delay active of %d ms.", timeInMs);
        case Reverb(String name, int roomSize) -> String.format("Reverb active of type %s and roomSize %d.", name, roomSize);
        case Overdrive(int gain) -> String.format("Overdrive active with gain %d.", gain);
        case Tremolo(int depth, int rate) -> String.format("Tremolo active with depth %d and rate %d.", depth, rate);
        case Tuner(int pitchInHz) -> String.format("Tuner active with pitch %d. Muting all signal!", pitchInHz);
        case EffectLoop(Tuner(int pitchInHz), _) -> String.format("The EffectLoop contains a tuner with pitch %d. Muting all signal!", pitchInHz);
        case EffectLoop(var effects) -> effects.stream().map(Effect::apply).collect(Collectors.joining(System.lineSeparator()));
    };
}
</code></pre>

<br/>
<small>
<a href="https://cr.openjdk.java.net/~briangoetz/amber/pattern-match.html">https://cr.openjdk.java.net/~briangoetz/amber/pattern-match.html</a>
</small>

note:
Here no default case is needed; the compiler is already aware that all cases have been handled.

---

<!-- .slide: data-auto-animate" -->

### Records

#### Input:

- Commit to the class being a transparent carrier for its data.

<h4>Output:</h4>
<ul>
    <li>constructors</li>
    <li>accessor methods</li>
    <li><code>equals()</code>-implementation</li>
    <li><code>hashCode()</code>-implementation</li>
    <li><code>toString()</code>-implementation</li>
    <li class="fragment">deconstruction pattern</li>
</ul>

---

<!---.slide: data-visibility="hidden" -->
<!-- .slide: data-auto-animate" -->

## Record patterns

<pre><code class="java" data-trim data-line-numbers>
record Amplifier(String name, EffectLoop stockEffects, EffectLoop auxEffects) { }
record EffectLoop(Delay delay, Reverb reverb) { }
</code></pre>

<pre><code class="java" data-trim data-line-numbers>
static String switchOn(Amplifier amplifier) {}
    return switch(effectLoop) {
        // ...
        case Amplifier(var name, EffectLoop(Delay(int timeInMs), Reverb r), var auxEffects) -> String.format("Stock delay active: timeInMs=%d.", timeInMs);
        // ...
    } 
}
</code></pre>

note:

Record patterns are part of JEP 405.
It will be the first installment of Java's support for deconstruction patterns.
It will probably be in preview in Java 19.

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

<!-- .slide: data-background="img/background/freddie-mercury.jpg" data-background-color="black" data-background-opacity="0.4" -->

## It's a kind of Pattern <!-- .element: class="stroke" -->

<table style="font-size: 100%">
    <thead>
        <tr>
            <th> pattern </th>
            <th><code>example</code></th>
        </tr>
    </thead>
    <tbody>
        <tr style="opacity:0.4;">
            <td>..</td>
            <td>..</td>
        </tr>
        <tr style="opacity:0.4;">
            <td> var pattern </td>
            <td><code>var timeInMs</code></td>
        </tr>
        <tr style="opacity:0.4;">
            <td> any pattern </td>
            <td><code>_</code></td>
        </tr>
        <tr>
            <td> array pattern </td>
            <td><code>EffectLoop(var name, var effect, ...)</code></td>
        </tr>
    </tbody>
</table>

<https://thumbs.gfycat.com/DefiantElasticGadwall.webp> <!-- .element: class="attribution" -->

---

## Feature Status

### Sealed Types

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
            <td><strong>15</strong></td>
            <td>Preview</td>
            <td><a href="https://openjdk.java.net/jeps/360">JEP 360</a></td>
        </tr>
        <tr>
            <td><strong>16</strong></td>
            <td>Second preview</td>
            <td><a href="https://openjdk.java.net/jeps/397">JEP 397</a></td>
        </tr>
        <tr>
            <td><strong>17</strong></td>
            <td>Final</td>
            <td><a href="https://openjdk.java.net/jeps/409">JEP 409</a></td>
        </tr>
    </tbody>
</table>

---

## Feature Status

### Completeness

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
            <td><strong>17</strong></td>
            <td>Preview</td>
            <td><a href="https://openjdk.java.net/jeps/406">JEP 406</a></td>
        </tr>
        <tr>
            <td><strong>18</strong></td>
            <td>Second preview <br/></td>
            <td><a href="https://openjdk.java.net/jeps/420">JEP 420</a></td>
        </tr>
        <tr>
            <td><strong>19</strong></td>
            <td>Third preview <br/></td>
            <td><a href="https://openjdk.java.net/jeps/427">JEP 427</a></td>
        </tr>           
    </tbody>
</table>


---

## Feature Status

### Record Patterns

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
            <td><strong>n/a</strong></td>
            <td>Preview</td>
            <td><a href="https://openjdk.java.net/jeps/405">JEP 405</a></td>
        </tr>
    </tbody>
</table>

note:

Record patterns will probably be in preview in Java 19, though this has not been confirmed yet.
Array patterns are not part of any JEP yet.
