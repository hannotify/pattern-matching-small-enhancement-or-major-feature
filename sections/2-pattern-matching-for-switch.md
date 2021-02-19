<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
# Pattern Matching for <!-- .element: class="stroke" -->
# `switch` <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

---

...

---

<!-- .slide: data-auto-animate" -->

### Switch expression

<pre data-id="switch-expression-animation"><code class="java" data-trim data-line-numbers>
void apply(Effect effect) {
    String applyResult = switch(effect) {
        default -> String.format("Unknown Effect(%s)", effect);
    };
    System.out.println(applyResult);
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Switch expression

<pre data-id="switch-expression-animation"><code class="java" data-trim data-line-numbers="3-4">
void apply(Effect effect) {
    String applyResult = switch(effect) {
        case Delay de -> String.format("Delay(timeInMs=%d)", de.timeInMs());
        case Reverb re -> String.format("Reverb(name=%s, roomSize=%s)", re.name(), re.roomSize());
        default -> String.format("Unknown Effect(%s)", effect);
    };
    System.out.println(applyResult);
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Switch expression

<pre data-id="switch-expression-animation"><code class="java" data-trim data-line-numbers="5-8">
void apply(Effect effect) {
    String applyResult = switch(effect) {
        case Delay de -> String.format("Delay(timeInMs=%d)", de.timeInMs());
        case Reverb re -> String.format("Reverb(name=%s, roomSize=%s)", re.name(), re.roomSize());
        case Overdrive ov -> String.format("Overdrive(gain=%d)", ov.gain());
        case Tremolo tr -> String.format("Tremolo(depth=%d, rate=%d)", tr.depth(), tr.rate());
        case Tuner tu -> String.format("Tuner(pitchInHz=%d)", tu.pitchInHz());
        default -> String.format("Unknown Effect(%s)", effect);
    };
    System.out.println(applyResult);
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Switch expression

<pre data-id="switch-expression-animation"><code class="java" data-trim data-line-numbers="9">
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

<!-- .slide: data-background="img/background/freddie-mercury.jpg" data-background-color="black" data-background-opacity="0.4" -->
## It's a kind of Pattern <!-- .element: class="stroke" -->

<blockquote class="explanation">
    <strong>constant pattern</strong>
    <br/>
    <code data-trim data-noescape>
    Day.MONDAY
    </code>
</blockquote>
<https://www.pexels.com/photo/gray-metal-statue-of-man-raising-hand-near-dock-825430/> <!-- .element: class="attribution" -->

---

## Feature Status

...

