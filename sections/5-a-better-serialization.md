<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
# A Better Serialization? <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

---

...

---

<!-- .slide: data-auto-animate" -->

### Serialization

<pre data-id="serialization-animation"><code class="java" data-trim data-line-numbers>
public class EffectLoop implements Effect {
    private String name;
    private Set&lt;Effect&gt; effects;

    public EffectLoop(String name) {
        this.name = name;
        this.effects = new HashSet&lt;&gt;();
    }
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Serialization

<pre data-id="serialization-animation"><code class="java" data-trim data-line-numbers="10-13">
public class EffectLoop implements Effect {
    private String name;
    private Set&lt;Effect&gt; effects;

    public EffectLoop(String name) {
        this.name = name;
        this.effects = new HashSet&lt;&gt;();
    }

    public pattern EffectLoop(String name, Effect[] effects) {
        name = this.name;
        effects = effects.toArray();
    }
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Serialization

<pre data-id="serialization-animation"><code class="java" data-trim data-line-numbers="10-15">
public class EffectLoop implements Effect {
    private String name;
    private Set&lt;Effect&gt; effects;

    public EffectLoop(String name) {
        this.name = name;
        this.effects = new HashSet&lt;&gt;();
    }

    public static Effectloop deserialize(String name, Effect[] effects) {
        EffectLoop effectLoop = new EffectLoop(name);
        for (Effect effect : effects) {
            this.effects.add(effect);
        }
    }

    public pattern EffectLoop(String name, Effect[] effects) {
        name = this.name;
        effects = effects.toArray();
    }
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Serialization

<pre data-id="serialization-animation"><code class="java" data-trim data-line-numbers>
public class EffectLoop implements Effect {
    private String name;
    private Set&lt;Effect&gt; effects;

    public EffectLoop(String name) {
        this.name = name;
        this.effects = new HashSet&lt;&gt;();
    }

    @Deserializer
    public static Effectloop deserialize(String name, Effect[] effects) {
        EffectLoop effectLoop = new EffectLoop(name);
        for (Effect effect : effects) {
            this.effects.add(effect);
        }
    }

    @Serializer
    public pattern EffectLoop(String name, Effect[] effects) {
        name = this.name;
        effects = effects.toArray();
    }
}
</code></pre>
