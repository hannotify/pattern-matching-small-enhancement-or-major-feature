<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
## Pattern Matching Plays Nice With 
# Sealed Types and Records <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

---

<!-- .slide: data-background="img/background/binary-code.jpg" data-background-color="black" data-background-opacity="0.3" -->
## Demo

* Convert effect classes to a record
* Acquire constructor, accessor methods etc.

<https://pxhere.com/en/photo/1458897> <!-- .element: class="attribution" -->

note:
Records can only be immutable classes.

---

<!-- .slide: data-auto-animate" -->

### Records

#### Input:

* Commit to the class being a transparent carrier for its data.

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

<!-- .slide: data-background="img/background/binary-code.jpg" data-background-color="black" data-background-opacity="0.3" -->
## Demo

* Make `Effect` a sealed type

<https://pxhere.com/en/photo/1458897> <!-- .element: class="attribution" -->

note:
* Records can only be immutable classes.
* Records are implicitly final.
* All implementations of a sealed type must be `final`, `sealed` or `non-sealed` ([more info on non-sealed](https://stackoverflow.com/questions/63860110/what-is-the-point-of-extending-a-sealed-class-with-a-non-sealed-class))

Example of non-sealed: `UserCustomEffect`.

---

<!-- .slide: data-auto-animate" -->

### Exhaustiveness

<pre data-id="exhaustiveness-animation"><code class="java" data-trim data-line-numbers>
String apply(Effect effect) {
    return switch(effect) {
        case Delay(int timeInMs) -> String.format("Delay active of %d ms.", timeInMs);
        case Reverb(String name, int roomSize) -> String.format("Reverb active of type %s and roomSize %d.", name, roomSize);
        case Overdrive(int gain) -> String.format("Overdrive active with gain %d.", gain);
        case Tremolo(int depth, int rate) -> String.format("Tremolo active with depth %d and rate %d.", depth, rate);
        case Tuner(int pitchInHz) -> String.format("Tuner active with pitch %d. Muting all signal!", pitchInHz);
        case EffectLoop(Tuner(int pitchInHz), _) -> String.format("The EffectLoop contains a tuner with pitch %d. Muting all signal!", pitchInHz);
        case EffectLoop(Set&lt;Effect&gt; effects) -> effects.stream().map(this::apply).collect(Collectors.joining(System.lineSeparator()));
        default -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Exhaustiveness

<pre data-id="exhaustiveness-animation"><code class="java" data-trim data-line-numbers>
String apply(Effect effect) {
    return switch(effect) {
        case Delay(int timeInMs) -> String.format("Delay active of %d ms.", timeInMs);
        case Reverb(String name, int roomSize) -> String.format("Reverb active of type %s and roomSize %d.", name, roomSize);
        case Overdrive(int gain) -> String.format("Overdrive active with gain %d.", gain);
        case Tremolo(int depth, int rate) -> String.format("Tremolo active with depth %d and rate %d.", depth, rate);
        case Tuner(int pitchInHz) -> String.format("Tuner active with pitch %d. Muting all signal!", pitchInHz);
        case EffectLoop(Tuner(int pitchInHz), _) -> String.format("The EffectLoop contains a tuner with pitch %d. Muting all signal!", pitchInHz);
        case EffectLoop(Set&lt;Effect&gt; effects) -> effects.stream().map(this::apply).collect(Collectors.joining(System.lineSeparator()));
    };
}
</code></pre>

<https://cr.openjdk.java.net/~briangoetz/amber/pattern-match.html> <!-- .element: class="attribution" -->

note:
* No default case needed; the compiler knows that all cases have been handled.

---

## Feature Status 
### Records

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
            <td><a href="https://openjdk.java.net/jeps/359">JEP 359</a></td>
        </tr>
        <tr>
            <td><strong>15</strong></td>
            <td>Second preview</td>
            <td><a href="https://openjdk.java.net/jeps/384">JEP 384</a></td>
        </tr>
        <tr>
            <td><strong>16</strong></td>
            <td>Final</td>
            <td><a href="https://openjdk.java.net/jeps/395">JEP 395</a></td>
        </tr>
    </tbody>
</table>

note:
Records don't acquire deconstruction patterns yet; I expect this to be a part of the 'deconstruction patterns' feature.
Its status is still 'exploratory document'.

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
            <td>...</td>
        </tr>
    </tbody>
</table>

note:
Sealed Types in Final status is expected in JDK 17, but hasn't officially been announced yet.
