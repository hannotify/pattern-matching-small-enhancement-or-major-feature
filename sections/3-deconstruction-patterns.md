<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->

# Deconstruction Patterns <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

note:

**Target time: 20m**

**Switch keyboard layout naar Qwerty Hanno! 'met Ctrl+Space'**

---

<!-- .slide: data-background="img/background/wall-disclaimer.jpg" data-background-color="black" data-background-opacity="0.5" -->

## Disclaimer <!-- .element: class="stroke" -->

<blockquote class="explanation">
    The following feature are still in 'preview' stage.
    Syntax and implementation specifics may still change.
</blockquote>

<https://pxhere.com/en/photo/1359311> <!-- .element: class="attribution" -->

note:

---

<!-- .slide: data-auto-animate" -->

### Deconstruction patterns

<pre data-id="deconstruction-patterns-animation"><code class="java" data-trim data-line-numbers>
String apply(Effect effect) {
    return switch(effect) {
        case Delay de      -> String.format("Delay active of %d ms.", de.getTimeInMs());
        case Reverb re     -> String.format("Reverb active of type %s and roomSize %d.", re.getName(), re.getRoomSize());
        case Overdrive ov  -> String.format("Overdrive active with gain %d.", ov.getGain());
        case Tremolo tr    -> String.format("Tremolo active with depth %d and rate %d.", tr.getDepth(), tr.getRate());
        case Tuner tu      -> String.format("Tuner active with pitch %d. Muting all signal!", tu.getPitchInHz());
        case EffectLoop el -> el.getEffects().stream().map(this::apply).collect(Collectors.joining(System.lineSeparator()));
        default            -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

note:
Let's get back to our switch expression example.

**Now imagine you implemented these effects as records.**

This means we can introduce a deconstruction pattern.

---

<!-- .slide: data-auto-animate" -->

### Deconstruction patterns

<pre data-id="deconstruction-patterns-animation"><code class="java" data-trim data-line-numbers="5">
String apply(Effect effect) {
    return switch(effect) {
        case Delay de      -> String.format("Delay active of %d ms.", de.getTimeInMs());
        case Reverb re     -> String.format("Reverb active of type %s and roomSize %d.", re.getName(), re.getRoomSize());
        case Overdrive(int gain) -> String.format("Overdrive active with gain %d.", gain);
        case Tremolo tr    -> String.format("Tremolo active with depth %d and rate %d.", tr.getDepth(), tr.getRate());
        case Tuner tu      -> String.format("Tuner active with pitch %d. Muting all signal!", tu.getPitchInHz());
        case EffectLoop el -> el.getEffects().stream().map(this::apply).collect(Collectors.joining(System.lineSeparator()));
        default            -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

note:
So now we've introduced a deconstruction pattern for the `Overdrive` case.
It's the thing that looks a bit like the signature of a constructor.
We no longer have an `overdrive` pattern variable available in the case branch; instead we just have the `gain` variable.
Well, we want to see that in action!

---

<!-- .slide: data-background="img/background/binary-code.jpg" data-background-color="black" data-background-opacity="0.3" -->

## Demo

- Deconstruction with Records

<https://pxhere.com/en/photo/1458897> <!-- .element: class="attribution" -->

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

Note:

So why does this work? As you all know, you get the implementation for all these methods for free.
In addition, Java offers you an implemenation for deconstruction patterns for records.

---

<!-- .slide: data-auto-animate" -->

### Deconstruction patterns

<pre data-id="deconstruction-patterns-2-animation"><code class="java" data-trim data-line-numbers>
String apply(Effect effect) {
    return switch(effect) {
        case Delay(int timeInMs) -> String.format("Delay active of %d ms.", timeInMs);
        case Reverb(String name, int roomSize) -> String.format("Reverb active of type %s and roomSize %d.", name, roomSize);
        case Overdrive(int gain) -> String.format("Overdrive active with gain %d.", gain);
        case Tremolo(int depth, int rate) -> String.format("Tremolo active with depth %d and rate %d.", depth, rate);
        case Tuner(int pitchInHz) -> String.format("Tuner active with pitch %d. Muting all signal!", pitchInHz);
        case EffectLoop(Set&lt;Effect&gt; effects) -> effects.stream().map(this::apply).collect(Collectors.joining(System.lineSeparator()));
        default -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

note:
Now, if all implementors of the Effect interface would become Records...
...the switch expression could look like this.
(We're too lazy to fix the arrow alignment here, sorry about that.)

But we can see that the benefits are quickly increasing!
For example with `Reverb` and `Tremolo`: we no longer have to call each getter separately.
We just perform one pattern match and we have all data available that we need.

It is a bit like going to the supermarket.
Do you enter and exit the supermarket again for each item you want to buy?
Or do you enter once, get all the stuff you need and then exit?

---

<!-- .slide: data-auto-animate" -->

### Can we combine patterns?

<pre data-id="composition-animation"><code class="java" data-trim data-line-numbers>
static boolean isDelayTimeEqualToReverbRoomSize(EffectLoop effectLoop) {

}
</code></pre>

note:
The previous pattern matches are fairly simple. However, can we combine these patterns to implement this method?

---

<!-- .slide: data-auto-animate" -->

### Pattern composition

<pre data-id="composition-animation"><code class="java" data-trim data-line-numbers>
static boolean isDelayTimeEqualToReverbRoomSize(EffectLoop effectLoop) {
    if (effectLoop instanceof EffectLoop(Delay(int timeInMs), Reverb(String name, int roomSize))) {
        return timeInMs == roomSize;
    }
    return false;
}
</code></pre>

note:
Yes, we can!

We can use patterns in conjunction with each other to achieve even more powerful matching logic.
We call this 'Pattern Composition', and it boils down to _nesting_ patterns.

Let's use an example to demonstrate this.
Suppose we have an `EffectLoop` and we want to know whether it contains both a `Reverb` and a `Delay` with the same value for their properties.

This is how easy the code could become if we use pattern composition!
(explain how the composed pattern would be matched)

---

<!-- .slide: data-auto-animate" -->

### Pattern composition

<pre data-id="composition-animation"><code class="java" data-trim data-line-numbers>
static boolean isDelayTimeEqualToReverbRoomSize(EffectLoop effectLoop) {
    return effectLoop.getEffects().stream()
        .filter(e -> e instanceof Delay || e instanceof Reverb)
        .map(dr -> {
            if (dr instanceof Delay d) {
                return d.getTimeInMs();
            } else {
                Reverb r = (Reverb) dr;
                return r.getRoomSize();
            }

        }).distinct().count() == 1;
}
</code></pre>

note:
A functional approach could look like this.
(explain how it works)
What a trainwreck, right?
We first tried an imperative approach.
Well, it didn't even make the slide.
Go figure!

---

### Var patterns

<pre data-id="type-inference-animation"><code class="java" data-trim data-line-numbers>
static boolean isDelayTimeEqualToReverbRoomSize(EffectLoop effectLoop) {
    if (effectLoop instanceof EffectLoop(Delay(int timeInMs), Reverb(String name, int roomSize))) {
        return timeInMs == roomSize;
    }
    return false;
}
</code></pre>

note:
Who of you likes to use `var` in Java?

I do, especially when the resulting type is really obvious.
Well, when you apply it to records, you can do the same with patterns.
You can use `var` instead of specifying an explicit type.

---

### Var patterns

<pre data-id="type-inference-animation"><code class="java" data-trim data-line-numbers="2">
static boolean isDelayTimeEqualToReverbRoomSize(EffectLoop effectLoop) {
    if (effectLoop instanceof EffectLoop(Delay(var timeInMs), Reverb(var name, var roomSize))) {
        return timeInMs == roomSize;
    }
    return false;
}
</code></pre>

note:

The compiler is able to infer the needed types here.

---

## Benefits of deconstruction patterns

<ul>
    <li>Better encapsulation<br/><small>a case branch only receives data that it actually references.</small>
    <li>More elegant logic<br/><small>by using pattern composition</small>
</ul>

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
            <td> type pattern </td>
            <td><code>Guitar lesPaul</code></td>
        </tr>
        <tr style="opacity:0.4;">
            <td> guarded pattern </td>  
            <td><code>Tuner tu when <br> !tu.isInTune(guitar)</code></td>
        </tr>
        <tr>
            <td> deconstruction pattern </td>  
            <td><code>Delay(int timeInMs)</code></td>
        </tr>
    </tbody>
</table>

<https://thumbs.gfycat.com/DefiantElasticGadwall.webp> <!-- .element: class="attribution" -->

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
            <td> type pattern </td>
            <td><code>Guitar lesPaul</code></td>
        </tr>
        <tr style="opacity:0.4;">
            <td> guarded pattern </td>  
            <td><code>Tuner tu when <br> !tu.isInTune(guitar)</code></td>
        </tr>
        <tr style="opacity:0.4;">
            <td> deconstruction pattern </td>  
            <td><code>Delay(int timeInMs)</code></td>
        </tr>
        <tr>
            <td> var pattern </td>
            <td><code>var timeInMs</code></td>
        </tr>
    </tbody>
</table>

<https://thumbs.gfycat.com/DefiantElasticGadwall.webp> <!-- .element: class="attribution" -->

---

## Feature Status

### Deconstruction Patterns for Records

<table style="font-size: 100%">
    <thead>
        <tr>
            <th>Java version</th>
            <th>Feature status</th>
            <th>JEP</th>
        </tr>
    </thead>
    <tbody>
        <tr class="greyed-out">
            <td><strong>19</strong></td>
            <td>Preview</td>
            <td><a href="https://openjdk.java.net/jeps/405">JEP 405</a></td>
        </tr>
        <tr class="greyed-out">
            <td><strong>20</strong></td>
            <td>Second preview</td>
            <td><a href="https://openjdk.java.net/jeps/432">JEP 432</a></td>
        </tr>
        <tr>
            <td><strong>21</strong></td>
            <td>Final</td>
            <td><a href="https://openjdk.org/jeps/440">JEP 440</a></td>
        </tr>
    </tbody>
</table>

note:

So to summarise: almost all pattern matching features are available for records first.
The same holds true for using var patterns, they are currently only supported for records.
With the intent to extend the support to arbitrary classes later.
Hanno will get into that in more detail later on.
