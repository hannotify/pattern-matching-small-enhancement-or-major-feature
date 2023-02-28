<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->

# Pattern Matching for <!-- .element: class="stroke" -->

# `switch` <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

note:

**Target time: 8m**

"Who has used pattern matching for switch already?"
(available in preview from Java 17)

---

<!-- .slide: data-background="img/background/guitar-store-close-up.jpg" data-background-color="black" data-background-opacity="0.2" -->

![music-store-phase-1](diagrams/music-store-phase-1.puml.png "Music store class diagram")

<https://pxhere.com/en/photo/548063> <!-- .element: class="attribution" -->

note:

Remember our code example?
We will now extend it for a bit.

---

<!-- .slide: data-background="img/background/stompboxes.jpg" data-background-color="black" data-background-opacity="0.2" -->

![music-store-phase-2](diagrams/music-store-phase-2.puml.png "Music store class diagram")

<https://pxhere.com/en/photo/544037> <!-- .element: class="attribution" -->

note:

Let's introduce some more classes from the local music store.
An `Effect` interface with a few implementations.

---

<!-- .slide: data-background="img/background/stompboxes.jpg" data-background-color="black" data-background-opacity="0.4" -->

![music-store-phase-4](diagrams/music-store-phase-4.puml.png "Music store class diagram")

<https://pxhere.com/en/photo/544037> <!-- .element: class="attribution" -->

note:
Let's continue by connecting `Guitar` to the effects.
We'll use an `Amplifier` and an `EffectLoop`.

---

<!-- .slide: data-auto-animate" -->

<pre data-id="switch-expression-animation"><code class="java" data-trim data-line-numbers>
String apply(Effect effect) {
    String formatted = "";
    if (effect instanceof Delay) {
        Delay de = (Delay) effect;
        formatted = String.format("Delay active of %d ms.", de.getTimeInMs());
    } else if (effect instanceof Reverb) {
        Reverb re = (Reverb) effect;
        formatted = String.format("Reverb active of type %s and roomSize %d.", re.getName(), re.getRoomSize());
    } else if (effect instanceof Overdrive) {
        Overdrive ov = (Overdrive) effect;
        formatted = String.format("Overdrive active with gain %d.", ov.getGain());
    } else if (effect instanceof Tremolo) {
        Tremolo tr = (Tremolo) effect;
        formatted = String.format("Tremolo active with depth %d and rate %d.", tr.getDepth(), tr.getRate());
    } else if (effect instanceof Tuner) {
        Tuner tu = (Tuner) effect;
        formatted = String.format("Tuner active with pitch %d. Muting all signal!", tu.getPitchInHz());
    } else if (effect instanceof EffectLoop) {
        EffectLoop el = (EffectLoop) effect;
        formatted = el.getEffects().stream().map(e -> apply(e)).collect(Collectors.joining(System.lineSeparator()));
    } else {
        formatted = String.format("Unknown effect active: %s.", effect);
    }
    return formatted;
}
</code></pre>

note:
This example contains 25 lines of code.

---

<!-- .slide: data-auto-animate" -->

<pre data-id="switch-expression-animation"><code class="java" data-trim data-line-numbers="5,8,11,14,17,20,22">
String apply(Effect effect) {
    String formatted = "";
    if (effect instanceof Delay) {
        Delay de = (Delay) effect;
        formatted = String.format("Delay active of %d ms.", de.getTimeInMs());
    } else if (effect instanceof Reverb) {
        Reverb re = (Reverb) effect;
        formatted = String.format("Reverb active of type %s and roomSize %d.", re.getName(), re.getRoomSize());
    } else if (effect instanceof Overdrive) {
        Overdrive ov = (Overdrive) effect;
        formatted = String.format("Overdrive active with gain %d.", ov.getGain());
    } else if (effect instanceof Tremolo) {
        Tremolo tr = (Tremolo) effect;
        formatted = String.format("Tremolo active with depth %d and rate %d.", tr.getDepth(), tr.getRate());
    } else if (effect instanceof Tuner) {
        Tuner tu = (Tuner) effect;
        formatted = String.format("Tuner active with pitch %d. Muting all signal!", tu.getPitchInHz());
    } else if (effect instanceof EffectLoop) {
        EffectLoop el = (EffectLoop) effect;
        formatted = el.getEffects().stream().map(this::apply).collect(Collectors.joining(System.lineSeparator()));
    } else {
        formatted = String.format("Unknown effect active: %s.", effect);
    }
    return formatted;
}
</code></pre>

note:
But it actually contains only 7 lines of _real_ business logic.
The rest is all ceremony!
So how can we reduce the ceremony?

Of course, with the introduction of JEP 305 (Pattern Matching for instanceof) we are able to simplify this code example...

---

<!-- .slide: data-auto-animate" -->

<pre data-id="switch-expression-animation"><code class="java" data-trim data-line-numbers>
String apply(Effect effect) {
    String formatted = "";
    if (effect instanceof Delay de) {
        formatted = String.format("Delay active of %d ms.", de.getTimeInMs());
    } else if (effect instanceof Reverb re) {
        formatted = String.format("Reverb active of type %s and roomSize %d.", re.getName(), re.getRoomSize());
    } else if (effect instanceof Overdrive ov) {
        formatted = String.format("Overdrive active with gain %d.", ov.getGain());
    } else if (effect instanceof Tremolo tr) {
        formatted = String.format("Tremolo active with depth %d and rate %d.", tr.getDepth(), tr.getRate());
    } else if (effect instanceof Tuner tu) {
        formatted = String.format("Tuner active with pitch %d. Muting all signal!", tu.getPitchInHz());
    } else if (effect instanceof EffectLoop el) {
        formatted = el.getEffects().stream().map(this::apply).collect(Collectors.joining(System.lineSeparator()));
    } else {
        formatted = String.format("Unknown effect active: %s.", effect);
    }
    return formatted;
}
</code></pre>

note:
...to _this_.

Let's make the business logic stand out again.

---

<!-- .slide: data-auto-animate" -->

<pre data-id="switch-expression-animation"><code class="java" data-trim data-line-numbers="4,6,8,10,12,14,16">
String apply(Effect effect) {
    String formatted = "";
    if (effect instanceof Delay de) {
        formatted = String.format("Delay active of %d ms.", de.getTimeInMs());
    } else if (effect instanceof Reverb re) {
        formatted = String.format("Reverb active of type %s and roomSize %d.", re.getName(), re.getRoomSize());
    } else if (effect instanceof Overdrive ov) {
        formatted = String.format("Overdrive active with gain %d.", ov.getGain());
    } else if (effect instanceof Tremolo tr) {
        formatted = String.format("Tremolo active with depth %d and rate %d.", tr.getDepth(), tr.getRate());
    } else if (effect instanceof Tuner tu) {
        formatted = String.format("Tuner active with pitch %d. Muting all signal!", tu.getPitchInHz());
    } else if (effect instanceof EffectLoop el) {
        formatted = el.getEffects().stream().map(this::apply).collect(Collectors.joining(System.lineSeparator()));
    } else {
        formatted = String.format("Unknown effect active: %s.", effect);
    }
    return formatted;
}
</code></pre>

note:
We're down to 19 lines of code.

And still, it doesn't feel very elegant.
Still 12 lines of code are dedicated to ceremony.
And I don't like the repetition of else-if's.

We're dealing with an equality test with multiple options here.
A better option than if-else would be `switch`, of course.
Although the `switch`-statement is currently very limited: it can only take numbers, strings and enum.
But hang on: the `instanceof` keyword was extended to take type patterns in JEP 305; why can't we generalize the `switch` statement to take type patterns, too?

---

<!-- .slide: data-auto-animate" -->

### Switch expression

<pre data-id="switch-expression-animation"><code class="java" data-trim data-line-numbers>
String apply(Effect effect) {
    return switch(effect) {
        default            -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

note:
Let's make use of switch expressions (JEP 361), which became standard functionality in Java 14.
We'll use `default` to replace the final `else` branch in our previous code example.
Also: we can get rid of the local variable, because we're using `switch` as an expression here.
Now, let's add some `case` statements.

---

<!-- .slide: data-auto-animate" -->

### Switch expression

<pre data-id="switch-expression-animation"><code class="java" data-trim data-line-numbers="3-4">
String apply(Effect effect) {
    return switch(effect) {
        case Delay de      -> String.format("Delay active of %d ms.", de.getTimeInMs());
        case Reverb re     -> String.format("Reverb active of type %s and roomSize %d.", re.getName(), re.getRoomSize());
        default            -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

note:
Using type patterns, we can have a reference to the specific Effect implementation type within the case expression.

---

<!-- .slide: data-auto-animate" -->

### Switch expression

<pre data-id="switch-expression-animation"><code class="java" data-trim data-line-numbers="5-7">
String apply(Effect effect) {
    return switch(effect) {
        case Delay de      -> String.format("Delay active of %d ms.", de.getTimeInMs());
        case Reverb re     -> String.format("Reverb active of type %s and roomSize %d.", re.getName(), re.getRoomSize());
        case Overdrive ov  -> String.format("Overdrive active with gain %d.", ov.getGain());
        case Tremolo tr    -> String.format("Tremolo active with depth %d and rate %d.", tr.getDepth(), tr.getRate());
        case Tuner tu      -> String.format("Tuner active with pitch %d. Muting all signal!", tu.getPitchInHz());
        default            -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Switch expression

<pre data-id="switch-expression-animation"><code class="java" data-trim data-line-numbers="8">
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

---

<!-- .slide: data-auto-animate" -->

### Switch expression

<pre data-id="switch-expression-animation"><code class="java" data-trim data-line-numbers>
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
And there we have it: 'Pattern Matching for `switch`' can reduce our code example to just 11 lines.
That is much better!

---

<!-- .slide: data-background="img/background/stompboxes.jpg" data-background-color="black" data-background-opacity="0.4" -->

![music-store-phase-4](diagrams/music-store-phase-4.puml.png "Music store class diagram")

<https://pxhere.com/en/photo/544037> <!-- .element: class="attribution" -->

note:
Q:

- Why didn't we use the type system, by implementing the `apply()` method?

A:

- We could have done that, but that approach has no need for pattern matching.
- On top of that: what if we wanted to add an operation that has no meaning for the entire effect loop?

---

### Sensible operations to the effect loop

- `apply()`
- `setVolume(int volume)`
- `contains(Effect... effect)`

---

### Nonsensical operations to the effect loop

- `isTunerActive()`
- `isDelayTimeEqualToReverbRoomSize()`
- `isToneSuitableToPlayPrideInTheNameOfLove()`

note:

- This category of operations is nonsensical to a lot of Effect implementations.
- Adding methods to the Effect interface would just pollute the API.

---

<!---.slide: data-visibility="hidden" -->

## Visitor pattern

```java
interface EffectVisitor<T> {
    T visit(Tuner effect);
    T visit(Delay effect);
    T visit(Reverb effect);
    // ...
}
class IsTunerActiveVisitor implements EffectVisitor<Boolean> {
    public Boolean visit(Tuner effect) {
        return !effect.isInTune();
    }

    public Boolean visit(Delay effect) { return false; }
    public Boolean visit(Reverb effect) { return false; }
    // ...
}
```

note:

Typically the visitor pattern is used in cases like this to separate traversal of a data structure from the data structure definition itself.

- quite verbose
- box primitives
- intrusive: each Effect implementation needs an `accept` method for each Visitor it accepts
- elements we're traversing must be in a hierarchy and thus need a common Visitor-specific supertype.

---

<!-- .slide: data-auto-animate" -->

### Switch expression

<pre data-id="switch-expression-animation"><code class="java" data-trim data-line-numbers>
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
So this method would make a lot more sense if it was `static`.

---

<!-- .slide: data-auto-animate" -->

### Switch expression

<pre data-id="switch-expression-animation"><code class="java" data-trim data-line-numbers>
static String apply(Effect effect) {
    return switch(effect) {
        case Delay de      -> String.format("Delay active of %d ms.", de.getTimeInMs());
        case Reverb re     -> String.format("Reverb active of type %s and roomSize %d.", re.getName(), re.getRoomSize());
        case Overdrive ov  -> String.format("Overdrive active with gain %d.", ov.getGain());
        case Tremolo tr    -> String.format("Tremolo active with depth %d and rate %d.", tr.getDepth(), tr.getRate());
        case Tuner tu      -> String.format("Tuner active with pitch %d. Muting all signal!", tu.getPitchInHz());
        case EffectLoop el -> el.getEffects().stream().map(Effect::apply).collect(Collectors.joining(System.lineSeparator()));
        default            -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

note:
So this method would make a lot more sense if it was `static`.

---

## Benefits of pattern matching

- No need for the Visitor pattern or a common Visitor-specific supertype (like `EffectVisitor`)
- A single expression instead of many assignments
- Less error-prone (in adding cases)
- More concise
- Safer - the compiler can check for missing cases

---

<!-- .slide: data-auto-animate" -->

### But what if `effect` is `null`?

<pre data-id="null-in-switch"><code class="java" data-trim data-line-numbers="2">
static String apply(Effect effect) {
    return switch(effect) { // throws NullPointerException!
        case Delay de      -> String.format("Delay active of %d ms.", de.getTimeInMs());
        case Reverb re     -> String.format("Reverb active of type %s and roomSize %d.", re.getName(), re.getRoomSize());
        case Overdrive ov  -> String.format("Overdrive active with gain %d.", ov.getGain());
        case Tremolo tr    -> String.format("Tremolo active with depth %d and rate %d.", tr.getDepth(), tr.getRate());
        case Tuner tu      -> String.format("Tuner active with pitch %d. Muting all signal!", tu.getPitchInHz());
        case EffectLoop el -> el.getEffects().stream().map(Effect::apply).collect(Collectors.joining(System.lineSeparator()));
        default            -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

note:
Traditionally, switch statements and expressions throw NullPointerException if the selector expression evaluates to null, so if you wanted to prevent this, you had to test for null outside of the switch expression.

---

<!-- .slide: data-auto-animate" -->

### Combining case labels

<pre data-id="null-in-switch"><code class="java" data-trim data-line-numbers="9">
static String apply(Effect effect) {
    return switch(effect) {
        case Delay de      -> String.format("Delay active of %d ms.", de.getTimeInMs());
        case Reverb re     -> String.format("Reverb active of type %s and roomSize %d.", re.getName(), re.getRoomSize());
        case Overdrive ov  -> String.format("Overdrive active with gain %d.", ov.getGain());
        case Tremolo tr    -> String.format("Tremolo active with depth %d and rate %d.", tr.getDepth(), tr.getRate());
        case Tuner tu      -> String.format("Tuner active with pitch %d. Muting all signal!", tu.getPitchInHz());
        case EffectLoop el -> el.getEffects().stream().map(Effect::apply).collect(Collectors.joining(System.lineSeparator()));
        case null, default -> String.format("Unknown or malfunctioning effect active: %s.", effect);
    };
}
</code></pre>

note:
However, it will be able to combine them, just as any other two case labels can be combined.

---

<!-- .slide: data-background="img/background/binary-code.jpg" data-background-color="black" data-background-opacity="0.3" -->

## Demo

- Guarded patterns

<https://pxhere.com/en/photo/1458897> <!-- .element: class="attribution" -->

note:

# Guarded patterns

A _guarded pattern_ is the combination of a pattern and a boolean expression.
This boolean expression must additionally be true in order for the guarded pattern to match.
We can use guarded patterns to further refine a matched pattern by applying a boolean expression.

---

<!-- .slide: data-auto-animate" -->

### Guarded patterns

<pre data-id="guards-animation"><code class="java" data-trim data-line-numbers=>
String apply(Effect effect, Guitar guitar) {
    return switch(effect) {
        // (...)
        case Tremolo tr -> String.format("Tremolo active with depth %d and rate %d.", tr.getDepth(), tr.getRate());
        case Tuner tu -> String.format("Tuner active with pitch %d. Muting all signal!", tu.getPitchInHz());
        case EffectLoop el -> el.getEffects().stream().map(this::apply).collect(Collectors.joining(System.lineSeparator()));
        default -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

note:

---

<!-- .slide: data-auto-animate" -->

### Guarded patterns

<pre data-id="guards-animation"><code class="java" data-trim data-line-numbers="5">
String apply(Effect effect, Guitar guitar) {
    return switch(effect) {
        // (...)
        case Tremolo tr -> String.format("Tremolo active with depth %d and rate %d.", tr.getDepth(), tr.getRate());
        case Tuner tu when !tu.isInTune(guitar) -> String.format("Guitar is in need of tuning - tuner active with pitch %d. Muting all signal!", tu.getPitchInHz());
        case EffectLoop el -> el.getEffects().stream().map(this::apply).collect(Collectors.joining(System.lineSeparator()));
        default -> String.format("Unknown effect active: %s.", effect);
    };
}
</code></pre>

note:
So we saw in the demo that the case block will only be executed if the effect is a `Tuner` and the `Guitar` is not in tune already.

One of the main reasons for Java to start supporting guarded patterns is to prevent further testing in a case block, like this:

---

<!-- .slide: data-auto-animate -->

### Guarded patterns

<pre data-id="overloaded-guards-animation"><code class="java" data-trim data-line-numbers>
switch(effect) {
    // ...
    case Tuner tu: 
        if (!tu.isInTune(guitar)) { // tuning is needed }
        else { // no tuning is needed }
        break;
    // ...
}
</code></pre>

note:
This is how you would have to write it if no guarded patterns were available.
We would have to use a good old switch statement instead of a switch expression.

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
        <tr>
            <td>guarded pattern</td>  
            <td><code>Tuner tu when <br> !tu.isInTune(guitar)</code></td>
        </tr>
    </tbody>
</table>

<https://thumbs.gfycat.com/DefiantElasticGadwall.webp> <!-- .element: class="attribution" -->

---

## Feature Status

### Pattern Matching for Switch

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
        <tr>
            <td><strong>20</strong></td>
            <td>Fourth preview <br/></td>
            <td><a href="https://openjdk.java.net/jeps/433">JEP 433</a></td>
        </tr>
    </tbody>
</table>
