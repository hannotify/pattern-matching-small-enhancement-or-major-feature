<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
# Pattern Matching Plays Nice With Sealed Types and Records <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

---

...

---

<!-- .slide: data-auto-animate" -->

### Exhaustiveness

<pre data-id="exhaustiveness-animation"><code class="java" data-trim data-line-numbers>
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

### Exhaustiveness

<pre data-id="exhaustiveness-animation"><code class="java" data-trim data-line-numbers>
void apply(Effect effect) {
    String applyResult = switch(effect) {
        case Delay(int timeInMs) -> String.format("Delay(timeInMs=%d)", timeInMs);
        case Reverb(String name, int roomSize) -> String.format("Reverb(name=%s, roomSize=%s)", name, roomSize);
        case Overdrive(int gain) -> String.format("Overdrive(gain=%d)", gain);
        case Tremolo(int depth, int rate) -> String.format("Tremolo(depth=%d, rate=%d)", depth, rate);
        case Tuner(int pitchInHz) -> String.format("Tuner(pitchInHz=%d)", pitchInHz);
        case EffectLoop(Set&lt;Effect&gt; effects) -> effects.forEach(this::apply);
    };
    System.out.println(applyResult);
}
</code></pre>