<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->

## Pattern Matching Plays Nice With

# Sealed Types and Records <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

note:

**Target time: 27m**

---

<!-- .slide: data-auto-animate" -->

### Sealed types yield completeness

<pre data-id="exhaustiveness-animation"><code class="java" data-trim data-line-numbers>
public interface Effect {}

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
public sealed interface Effect permits Delay, EffectLoop, Octave, Overdrive, Reverb, Tremolo, Tuner {}

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

note:
Here no default case is needed; the compiler is already aware that all cases have been handled.

---

<!-- .slide: data-auto-animate" -->

### Sealed types yield completeness

<pre data-id="exhaustiveness-animation"><code class="java" data-trim data-line-numbers>
public sealed interface Effect permits Delay, EffectLoop, Octave, Overdrive, Reverb, Tremolo, Tuner {}

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
<a href="https://openjdk.org/projects/amber/design-notes/patterns/pattern-matching-for-java">https://openjdk.org/projects/amber/design-notes/patterns/pattern-matching-for-java</a>
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

Like we said before records are the first Java construct that supports deconstruction.
You can even deconstruct using a var pattern, as long as you apply it to records.

---

<!-- .slide: data-auto-animate" -->

## Patterns in enhanced for

<pre><code class="java" data-trim data-line-numbers>
record Delay(int timeInMs) implements Effect {}
</code></pre>

<pre data-id="patterns-enhanced-for-animation"><code class="java" data-trim data-line-numbers>
static void printDelays(List&lt;Delay&gt; delays) {
    for (Delay delay : delays) {
        System.out.println("Delay found with timeInMs=" + delay.timeInMs());
    }
}
</code></pre>

note:

When dealing with a record in an enhanced for loop, you can start using pattern matching there as well.

---

<!-- .slide: data-auto-animate" -->

## Patterns in enhanced for

<pre><code class="java" data-trim data-line-numbers>
record Delay(int timeInMs) implements Effect {}
</code></pre>

<pre data-id="patterns-enhanced-for-animation"><code class="java" data-trim data-line-numbers>
static void printDelays(List&lt;Delay&gt; delays) {
    for (Delay(int timeInMs) : delays) {
        System.out.println("Delay found with timeInMs=" + timeInMs);
    }
}
</code></pre>

note:

Like so.
The implementation of the for loop only receives the `timeInMs` field.

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
            <td><strong>19</strong></td>
            <td>Preview</td>
            <td><a href="https://openjdk.java.net/jeps/405">JEP 405</a></td>
        </tr>
        <tr>
            <td><strong>n/a</strong></td>
            <td>Second preview</td>
            <td><a href="https://openjdk.java.net/jeps/432">JEP 432</a></td>
        </tr>
    </tbody>
</table>

note:

So to summarise: almost all pattern matching features are made available for records first.
With the intent to extend the support to arbitrary classes later.
