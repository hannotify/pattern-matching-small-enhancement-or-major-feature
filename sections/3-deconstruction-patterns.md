<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
# Deconstruction Patterns <!-- .element: class="stroke" -->

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

...

---

<!-- .slide: data-auto-animate" -->

### Deconstruction patterns

<pre data-id="pattern-definition-animation"><code class="java" data-trim data-line-numbers>
public class Overdrive implements Effect {
    private final int gain;

    public Overdrive(int gain) {
        this.gain = gain;
    }
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Deconstruction patterns

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

---

...

---

---

<!-- .slide: data-auto-animate" -->

### Deconstruction patterns

<pre data-id="deconstruction-patterns-animation"><code class="java" data-trim data-line-numbers>
void apply(Effect effect) {
    String applyResult = switch(effect) {
        case Delay de -> String.format("Delay(timeInMs=%d)", de.timeInMs());
        case Reverb re -> String.format("Reverb(name=%s, roomSize=%s)", re.name(), re.roomSize());
        case Overdrive ov -> String.format("Overdrive(gain=%d)", ov.gain());
        case Tremolo tr -> String.format("Tremolo(depth=%d, rate=%d)", tr.depth(), tr.rate());
        case Tuner tu -> String.format("Tuner(pitchInHz=%d)", tu.pitchInHz());
        case EffectLoop el -> el.getEffects().forEach(this::apply);
        default -> String.format("Unknown Effect(%s)", effect);
    };
    System.out.println(applyResult);
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Deconstruction patterns

<pre data-id="deconstruction-patterns-animation"><code class="java" data-trim data-line-numbers="5">
void apply(Effect effect) {
    String applyResult = switch(effect) {
        case Delay de -> String.format("Delay(timeInMs=%d)", de.timeInMs());
        case Reverb re -> String.format("Reverb(name=%s, roomSize=%s)", re.name(), re.roomSize());
        case Overdrive(int gain) -> String.format("Overdrive(gain=%d)", gain);
        case Tremolo tr -> String.format("Tremolo(depth=%d, rate=%d)", tr.depth(), tr.rate());
        case Tuner tu -> String.format("Tuner(pitchInHz=%d)", tu.pitchInHz());
        case EffectLoop el -> el.getEffects().forEach(this::apply);
        default -> String.format("Unknown Effect(%s)", effect);
    };
    System.out.println(applyResult);
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Deconstruction patterns

<pre data-id="deconstruction-patterns-animation"><code class="java" data-trim data-line-numbers>
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

...

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

