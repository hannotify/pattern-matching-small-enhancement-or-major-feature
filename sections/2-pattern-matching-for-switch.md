<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
# Pattern Matching for <!-- .element: class="stroke" -->
# `switch` <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

---

<!-- .slide: data-background="img/background/stompboxes.jpg" data-background-color="black" data-background-opacity="0.4" -->
![music-store-phase-4](diagrams/music-store-phase-4.puml.png "Music store class diagram")

<https://pxhere.com/en/photo/544037> <!-- .element: class="attribution" -->

note:
To demonstrate 'Pattern Matching for switch' I need a few more Effect implementations.
So let's introduce them to the domain.

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
But it actually contains only 7 lines of *real* business logic.
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
...to *this*.

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
* Why didn't we use the type system, by implementing the `apply()` method?

A: 
* We could have done that, but that approach has no need for pattern matching.
* On top of that: what if we wanted to add an operation that has no meaning for the entire effect loop?

---

### Sensible operations to the effect loop

* `apply()`
* `setVolume(int volume)`
* `contains(Effect... effect)`

---

### Nonsensical operations to the effect loop

* `isTunerActive()`
* `isDelayTimeEqualToReverbRoomSize()`
* `isToneSuitableToPlayPrideInTheNameOfLove()`

note:
* This category of operations is nonsensical to a lot of Effect implementations.
* Adding methods to the Effect interface would just pollute the API.

---

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

* quite verbose
* box primitives
* intrusive: each Effect implementation needs an `accept` method for each Visitor it accepts
* elements we're traversing need a common supertype.

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
In fact, its enclosing class would probably contain more static methods like this, such as `isTunerActive` for example.

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

* No need for the Visitor pattern or a common supertype
* A single expression instead of many assignments
* Less error-prone (in adding cases)
* More concise
* Safer - the compiler can check for missing cases

---

<!-- .slide: data-auto-animate" -->

### What happens if `effect` is `null`?

<pre data-id="null-in-switch"><code class="java" data-trim data-line-numbers>
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

---

<!-- .slide: data-auto-animate" -->

### What happens if `effect` is `null`?

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

---

<!-- .slide: data-auto-animate" -->

### Solution #1: defensive testing

<pre data-id="null-in-switch"><code class="java" data-trim data-line-numbers="2-4">
static String apply(Effect effect) {
    if (effect == null) {
        return "Malfunctioning effect active.";
    }

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
Doesn't feel right. We now have to repeat the 'return' keyword. :(
Also, it take a while to grasp why the null case is handled separately.

---

<!-- .slide: data-auto-animate" -->

### Solution #2: integrate null check in switch

<pre data-id="null-in-switch"><code class="java" data-trim data-line-numbers="3">
static String apply(Effect effect) {
    return switch(effect) {
        case null          -> return "Malfunctioning effect active.";
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
This makes me feel all warm and fuzzy inside. Great stuff!
Note that if you would forget to add the `null` case, the code would still throw a `NullPointerException`.
The `default` case will not be changed to include `null`, to maintain backwards compatibility.

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
However, it will be able to combine them, just as any other two case labels can.

---

<!-- .slide: data-auto-animate" -->

### Guarded patterns

<pre data-id="guards-animation"><code class="java" data-trim data-line-numbers=>
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

<https://openjdk.java.net/jeps/406> <!-- .element: class="attribution" -->

note:
A *guarded pattern* is the combination of a pattern and a boolean expression.
This boolean expression must additionally be true in order for the guarded pattern to match.
In this case we could use a guarded pattern to prevent unnecessary tuning...

---

<!-- .slide: data-auto-animate -->

### Guarded patterns

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
This is how you would write it if no guarded patterns were available.
We would have to use a good old switch statement instead of a switch expression.
Using a guarded pattern and a regular one, we can rewrite this logic as follows:

---

<!-- .slide: data-auto-animate -->

### Guarded patterns

<pre data-id="overloaded-guards-animation"><code class="java" data-trim data-line-numbers>
switch(effect) {
    // ...
    case Tuner tu && !tu.isInTune(guitar) -> // tuning is needed
    case Tuner tu                         -> // no tuning is needed
    // ...
}
</code></pre>

note:
And in the process it has become a switch expression again.
The second case label will match on all Tuners that return `true` for the `isInTune(guitar)` method.

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
            <td><strong>17</strong></td>
            <td>Preview</td>
            <td><a href="https://openjdk.java.net/jeps/406">JEP 406</a></td>
        </tr>
    </tbody>
</table>

<https://openjdk.java.net/jeps/406> <!-- .element: class="attribution" -->
