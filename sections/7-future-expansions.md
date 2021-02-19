<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
# Future Expansions <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

---

...

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

