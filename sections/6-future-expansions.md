<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->

# Future Expansions <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

note:

**Target time: --m**

---

### Unnamed patterns

<pre data-id="type-inference-animation"><code class="java" data-trim data-line-numbers="2">
static boolean isDelayTimeEqualToReverbRoomSize(EffectLoop effectLoop) {
    if (effectLoop instanceof EffectLoop(Delay(var timeInMs), Reverb(var name, var roomSize))) {
        return timeInMs == roomSize;
    }
    return false;
}
</code></pre>

note:

Let's start with an expansion that we know is in the near future. "Unnamed patterns"

Remember our var pattern example?
In this example the compiler can infer the needed types from the deconstruction patterns in the `Delay` and `Reverb` class.
Although it seems a bit of a waste to map the `name` variable from the Reverb class.
Because we don't do anything with it.
In this case we could choose to apply an 'unnamed pattern'.

---

### Unnamed patterns

<pre data-id="type-inference-animation"><code class="java" data-trim data-line-numbers>
static boolean isDelayTimeEqualToReverbRoomSize(EffectLoop effectLoop) {
    if (effectLoop instanceof EffectLoop(Delay(var timeInMs), Reverb(_, var roomSize))) {
        return timeInMs == roomSize;
    }
    return false;
}
</code></pre>

note:

So an 'unnamed pattern' is like a var pattern: it doesn't specify a type and so the type is inferred from the deconstruction pattern.
But there is a big difference: an unnamed pattern _doesn't bind a value to a variable_.
That's why there is no variable name, and just an underscore character to denote the unnamed pattern.
It's like you tell the compiler: "Compiler, I don't care about the first value at all, just make sure to map the second value (which is `roomSize`) to a variable.
You also tell your fellow developers that you deliberately don't use the second value.

---

<!-- .slide: data-auto-animate" -->

### Optimization

<pre data-id="optimization-animation"><code class="java" data-trim data-line-numbers>
static String apply(Effect effect) {
    return switch(effect) {
        case Delay(int timeInMs) -> String.format("Delay active of %d ms.", timeInMs);
        case Reverb(String name, int roomSize) -> String.format("Reverb active of type %s and roomSize %d.", name, roomSize);
        case Overdrive(int gain) -> String.format("Overdrive active with gain %d.", gain);
        case Tremolo(int depth, int rate) -> String.format("Tremolo active with depth %d and rate %d.", depth, rate);
        case Tuner(int pitchInHz) -> String.format("Tuner active with pitch %d. Muting all signal!", pitchInHz);
        case EffectLoop(var effects) -> effects.stream().map(Effect::apply).collect(Collectors.joining(System.lineSeparator()));
        default -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

note:
Another use case for unnamed patterns is optimization of a specific case branch.
To demonstrate this, let's return to our switch expression example.
Now the EffectLoop branch could be quite performance heavy, because of the recursive call.
So if we could avoid executing it when it is not needed, we would.

---

<!-- .slide: data-auto-animate" -->

### Optimization

<pre data-id="optimization-animation"><code class="java" data-trim data-line-numbers="4">
static String apply(Effect effect) {
    return switch(effect) {
        // ...
        case EffectLoop(var effects) -> effects.stream().map(Effect::apply).collect(Collectors.joining(System.lineSeparator()));
        default -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

note:
Let's hide the other branches for now to be able to focus on this case branch a bit better.
Now, here is my optimization idea.
Whenever an effect loop contains an active tuner, the signal is muted, right?
So why bother processing the entire effect loop when you know all those effects aren't going to change the guitar tone at all?

---

<!-- .slide: data-auto-animate" -->

### Optimization

<pre data-id="optimization-animation"><code class="java" data-trim data-line-numbers="4-5">
static String apply(Effect effect) {
    return switch(effect) {
        // ...
        case EffectLoop(Tuner(int pitchInHz), _) -> String.format("The EffectLoop contains a tuner with pitch %d. Muting all signal!", pitchInHz);
        case EffectLoop(var effects) -> effects.stream().map(Effect::apply).collect(Collectors.joining(System.lineSeparator()));
        default -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

note:
This is where unnamed patterns come in.
We could define a deconstruction pattern that checks for the presence of a tuner.
Using an unnamed pattern we can tell the compiler that we don't care about any other effects.
Now, if there is a Tuner present in the effect loop, the top case branch will be executed.
If no Tuner is present, the 'regular' case branch will be executed as before.

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
        <tr style="opacity:0.4;">
            <td> var pattern </td>
            <td><code>var timeInMs</code></td>
        </tr>
        <tr>
            <td> unnamed pattern </td>
            <td><code>_</code></td>
        </tr>
    </tbody>
</table>

<https://thumbs.gfycat.com/DefiantElasticGadwall.webp> <!-- .element: class="attribution" -->

---

## Feature Status

### Unnamed Patterns

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
            <td><strong>21</strong></td>
            <td>Preview</td>
            <td><a href="https://openjdk.org/jeps/443">JEP 443</a></td>
        </tr>
    </tbody>
</table>

<https://openjdk.org/jeps/443>

note:

Unnamed patterns are part of JEP 443, which is targeted for Java 21.
The JEP will also add support for unnamed local variables, by the way.
(example: catching an exception but not planning to use the exception variable)

---

## Primitive types in patterns, instanceof and switch

TODO

<https://openjdk.org/jeps/8288476>


---

<!-- .slide: data-background="img/background/dragons.jpg" data-background-color="black" data-background-opacity="0.7" -->

## Here be dragons! <!-- .element: class="stroke" -->

<blockquote class="explanation">
    We can't be sure <strong>at all</strong> that the following features will appear in Java as depicted.
    They can change a <strong>lot</strong> in the meantime.
</blockquote>

<https://www.pexels.com/photo/dragon-festival-during-nighttime-6068535/> <!-- .element: class="attribution" -->

note:
This is like a disclaimer`++`.

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
<a href="https://openjdk.org/projects/amber/design-notes/patterns/pattern-matching-for-java">https://openjdk.org/projects/amber/design-notes/patterns/pattern-matching-for-java</a>
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

### Other ideas

<ul>
    <li>Deconstruction patterns for arbitrary classes</li>
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
