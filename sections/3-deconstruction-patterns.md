<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->

# Deconstruction Patterns <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

note:

**Target time: 25m**

**Switch keyboard layout naar Qwerty Hanno!!**

---

<!-- .slide: data-background="img/background/wall-disclaimer.jpg" data-background-color="black" data-background-opacity="0.5" -->

## Disclaimer <!-- .element: class="stroke" -->

<blockquote class="explanation">
    We can't tell you when the following features are coming to Java.
    Also: syntax and implementation specifics may still change.
</blockquote>

<https://pxhere.com/en/photo/1359311> <!-- .element: class="attribution" -->

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
Let's get back to our switch expression and introduce a deconstruction pattern.

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
This looks nice, but it makes me wonder what changes the `Overdrive` class needs to support this.

---

<!-- .slide: data-auto-animate" -->

### Pattern definition

<pre data-id="pattern-definition-animation"><code class="java" data-trim data-line-numbers>
public class Overdrive implements Effect {
    private final int gain;

    public Overdrive(int gain) {
        this.gain = gain;
    }
}
</code></pre>

note:
This is the `Overdrive` class right now...

---

<!-- .slide: data-auto-animate" -->

### Pattern definition

<pre data-id="pattern-definition-animation"><code class="java" data-trim data-line-numbers="8-10">
public class Overdrive implements Effect {
    private final int gain;

    public Overdrive(int gain) {
        this.gain = gain;
    }

    public pattern Overdrive(int gain) {
        gain = this.gain;
    }
}
</code></pre>

note:
...and this is wat we need to add to support a deconstruction pattern.
It is called a _pattern definition_.
And it sort of works like a reverse constructor.

---

### Deconstruction patterns

<pre data-id="deconstruction-patterns-2-animation"><code class="java" data-trim data-line-numbers="5">
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
Well, we want to see that in action!

---

<!-- .slide: data-background="img/background/binary-code.jpg" data-background-color="black" data-background-opacity="0.3" -->

## Demo

- Deconstruction with Records

<https://pxhere.com/en/photo/1458897> <!-- .element: class="attribution" -->

note:
Now, if we add a pattern definition to every implementor of the Effect interface...

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
...the switch expression could look like this.
(I'm too lazy to fix the arrow alignment here, sorry about that.)

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
I first tried an imperative approach.
Well, it didn't even make the slide.
Go figure!

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

### Var and any patterns

<pre data-id="type-inference-animation"><code class="java" data-trim data-line-numbers>
// Pre-Java 10
Guitar telecaster = new Guitar("Fender Telecaster Baritone Blacktop", GuitarType.TELECASTER);

// Java 10
var telecaster = new Guitar("Fender Telecaster Baritone Blacktop", GuitarType.TELECASTER);
</code></pre>

<small><a href="https://openjdk.java.net/jeps/286">https://openjdk.java.net/jeps/286</a>

note:
Do you remember 'Local-Variable Type Inference' that became available in Java 10?
I really like to use this feature in places where you would otherwise repeat the type.
Well, in the future you can do the same with patterns.
You can use `var` instead of specifying an explicit type.

---

### Var and any patterns

<pre data-id="type-inference-animation"><code class="java" data-trim data-line-numbers>
static boolean isDelayTimeEqualToReverbRoomSize(EffectLoop effectLoop) {
    if (effectLoop instanceof EffectLoop(Delay(int timeInMs), Reverb(String name, int roomSize))) {
        return timeInMs == roomSize;
    }
    return false;
}
</code></pre>

note:
Let's return to our pattern composition example and use a few var patterns.

---

### Var and any patterns

<pre data-id="type-inference-animation"><code class="java" data-trim data-line-numbers="2">
static boolean isDelayTimeEqualToReverbRoomSize(EffectLoop effectLoop) {
    if (effectLoop instanceof EffectLoop(Delay(var timeInMs), Reverb(var name, var roomSize))) {
        return timeInMs == roomSize;
    }
    return false;
}
</code></pre>

note:
The compiler can infer the needed types from the pattern definitions in the `Delay` and `Reverb` class.
Although it seems a bit of a waste to map the `name` variable from the Reverb class.
Because we don't do anything with it.
In this case we could choose to apply an 'any pattern'.

---

<!-- .slide: data-background="https://media.giphy.com/media/26BGIqWh2R1fi6JDa/giphy.gif" data-background-size="contain" data-background-opacity="0.8" data-background-color="black"-->

<http://gph.is/2lFlHIK> <!-- .element: class="attribution" style="color: white !important;" -->

note:
Which sounds a bit like the 'any key' that Homer is still trying to find.

---

### Var and any patterns

<pre data-id="type-inference-animation"><code class="java" data-trim data-line-numbers>
static boolean isDelayTimeEqualToReverbRoomSize(EffectLoop effectLoop) {
    if (effectLoop instanceof EffectLoop(Delay(var timeInMs), Reverb(_, var roomSize))) {
        return timeInMs == roomSize;
    }
    return false;
}
</code></pre>

note:
So an 'any pattern' is like a var pattern: it doesn't specify a type and so the type is inferred from the pattern definition.
But there is a big difference: an any pattern _doesn't bind a value to a variable_.
That's why there is no variable name, and just an underscore character to denote the any pattern.
It's like you tell the compiler: "Compiler, I don't care about the first value at all, just make sure to map the second value (which is `roomSize`) to a variable.

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
Another use case for any patterns is optimization of a specific case branch.
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
This is where any patterns come in.
We could define a pattern definition that checks for the presence of a tuner.
Using an any pattern we can tell the compiler that we don't care about any other effects.
Now, if there is a Tuner present in the effect loop, the top case branch will be executed.
If no Tuner is present, the 'regular' case branch will be executed as before.

---

## Benefits

<ul>
    <li>Better encapsulation<br/><small>a case branch only receives data that it actually references.</small>
    <li>More elegant logic<br/><small>by using pattern composition</small>
    <li>Optimization<br/><small>through the use of any patterns</small>
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
            <td> any pattern </td>
            <td><code>_</code></td>
        </tr>
    </tbody>
</table>

<https://thumbs.gfycat.com/DefiantElasticGadwall.webp> <!-- .element: class="attribution" -->

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
            <td><strong>19</strong></td>
            <td>Preview (composition of record and type patterns only)</td>
            <td><a href="https://openjdk.java.net/jeps/405">JEP 405</a></td>
        </tr>
    </tbody>
</table>

note:

It is impossible to put a release version on deconstruction patterns as a whole.
Because it will be delivered in parts.
The first part adds support for record and type patterns only.
It is in 'Preview' status in Java 19.
More on record patterns in a moment.
