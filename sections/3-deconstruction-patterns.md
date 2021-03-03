<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
## Deconstruction <!-- .element: class="stroke" -->
# Patterns <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

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
We've introduced a deconstruction pattern for the `Overdrive` case.
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
It is called a *pattern definition*.
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
If we add a pattern definition to every implementor of the Effect interface...

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
...the code could look like this.
I'm too lazy to fix the arrow alignment here, sorry about that.

But we can see that the benefits are quickly increasing!
For example with `Reverb` and `Tremolo`: we no longer have to call each getter separately.
We just perform one pattern match and we have all data available that we need.

It is a bit like going to the supermarket.
Do you enter and exit the supermarket again for each item you want to buy?
Or do you enter once, get all the stuff you need and then exit?

---

<!-- .slide: data-auto-animate" -->
### Composition

<pre data-id="composition-animation"><code class="java" data-trim data-line-numbers>
static boolean containsReverbAndDelayWithEqualProperties(EffectLoop effectLoop) {
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

---

<!-- .slide: data-auto-animate" -->
### Composition

<pre data-id="composition-animation"><code class="java" data-trim data-line-numbers>
static boolean containsReverbAndDelayWithEqualProperties(EffectLoop effectLoop) {
    if (effectLoop instanceof EffectLoop(Delay(int timeInMs), Reverb(String name, int roomSize))) {
        return timeInMs == roomSize;
    }
    return false;
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Optimization

<pre data-id="optimization-animation"><code class="java" data-trim data-line-numbers>
void apply(Effect effect) {
    String applyResult = switch(effect) {
        case Delay(int timeInMs) -> String.format("Delay(timeInMs=%d)", timeInMs);
        case Reverb(String name, int roomSize) -> String.format("Reverb(name=%s, roomSize=%s)", name, roomSize);
        case Overdrive(int gain) -> String.format("Overdrive(gain=%d)", gain);
        case Tremolo(int depth, int rate) -> String.format("Tremolo(depth=%d, rate=%d)", depth, rate);
        case Tuner(int pitchInHz) -> String.format("Tuner(pitchInHz=%d)", pitchInHz);
        case EffectLoop(Set&lt;Effect&gt; effects) -> effects.forEach(this::apply);
        default -> String.format("Unknown Effect(%s)", effect);
    };
    System.out.println(applyResult);
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Optimization

<pre data-id="optimization-animation"><code class="java" data-trim data-line-numbers="3">
void apply(Effect effect) {
    String applyResult = switch(effect) {
        case EffectLoop(Set&lt;Effect&gt; effects) -> effects.forEach(this::apply);
        default -> String.format("Unknown Effect(%s)", effect);
    };
    System.out.println(applyResult);
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Optimization

<pre data-id="optimization-animation"><code class="java" data-trim data-line-numbers="3-4">
void apply(Effect effect) {
    String applyResult = switch(effect) {
        case EffectLoop(Tuner tuner, _),
             EffectLoop(_, Tuner tuner) -> "Tuner is active, all effects are muted.";
        case EffectLoop(Set&lt;Effect&gt; effects) -> effects.forEach(this::apply);
        default -> String.format("Unknown Effect(%s)", effect);
    };
    System.out.println(applyResult);
}
</code></pre>

---

---

## Benefits

* Better encapsulation - a case branch only receives data that it actually references.
* ...

---

<!-- .slide: data-background="img/background/freddie-mercury.jpg" data-background-color="black" data-background-opacity="0.4" -->
## It's a kind of Pattern <!-- .element: class="stroke" -->

<blockquote class="explanation">
    <strong>deconstruction pattern</strong>
    <br/>
    <code data-trim data-noescape>
    Delay(int timeInMs)
    </code>
</blockquote>

<https://www.pexels.com/photo/gray-metal-statue-of-man-raising-hand-near-dock-825430/> <!-- .element: class="attribution" -->

---

<!-- .slide: data-background="img/background/freddie-mercury.jpg" data-background-color="black" data-background-opacity="0.4" -->
## It's a kind of Pattern <!-- .element: class="stroke" -->

<blockquote class="explanation">
    <strong>var pattern</strong>
    <br/>
    <code data-trim data-noescape>
    var depth
    </code>
</blockquote>

<https://www.pexels.com/photo/gray-metal-statue-of-man-raising-hand-near-dock-825430/> <!-- .element: class="attribution" -->

---

<!-- .slide: data-background="img/background/freddie-mercury.jpg" data-background-color="black" data-background-opacity="0.4" -->
## It's a kind of Pattern <!-- .element: class="stroke" -->

<blockquote class="explanation">
    <strong>any pattern</strong>
    <br/>
    <code data-trim data-noescape>
    _
    </code>
</blockquote>

<https://www.pexels.com/photo/gray-metal-statue-of-man-raising-hand-near-dock-825430/> <!-- .element: class="attribution" -->
