<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
# Future Expansions <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

---

<!-- .slide: data-auto-animate" -->

### Pattern bind statements

<pre data-id="pattern-bind-statements-animation"><code class="java" data-trim data-line-numbers>
var reverb = new Reverb("ChamberReverb", 2);

__let Reverb(String name, int roomSize) = reverb;

// do something with name & roomSize
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Pattern bind statements

<pre data-id="pattern-bind-statements-animation"><code class="java" data-trim data-line-numbers="4">
var reverb = new Reverb("ChamberReverb", 2);

__let Reverb(String name, int roomSize) = reverb;
else throw new IllegalArgumentException("not a Reverb!");

// do something with name & roomSize
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Guards

<pre data-id="guards-animation"><code class="java" data-trim data-line-numbers>
void apply(Effect effect, Guitar guitar) {
    String applyResult = switch(effect) {
        // (...)
        case Tremolo tr -> String.format("Tremolo is active! Depth=%d, rate=%d.", tr.depth(), tr.rate());
        case Tuner tu -> String.format("Tuning in progress... PitchInHz=%d.", tu.pitchInHz());
        case EffectLoop el -> el.getEffects().forEach(this::apply);
        default -> String.format("Unknown Effect(%s)", effect);
    };
    System.out.println(applyResult);
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Guards

<pre data-id="guards-animation"><code class="java" data-trim data-line-numbers="5">
void apply(Effect effect, Guitar guitar) {
    String applyResult = switch(effect) {
        // (...)
        case Tremolo tr -> String.format("Tremolo is active! Depth=%d, rate=%d.", tr.depth(), tr.rate());
        case Tuner tu && !tu.isInTune(guitar) -> String.format("Tuning in progress... PitchInHz=%d.", tu.pitchInHz());
        case EffectLoop el -> el.getEffects().forEach(this::apply);
        default -> String.format("Unknown Effect(%s)", effect);
    };
    System.out.println(applyResult);
}
</code></pre>

---

## More future expansions

<span class="fragment">
* Array patterns
</span>

<span class="fragment">
* Record patterns
</span>

<span class="fragment">
* Guard patterns
</span>

<span class="fragment">
* AND patterns
</span>

(...)
