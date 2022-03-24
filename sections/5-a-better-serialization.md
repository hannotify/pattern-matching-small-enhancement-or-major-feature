<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
# A Better Serialization? <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

note:

**Target time: 29m**

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

## Opposites

### Deconstruction pattern

* transforms an object into a set of typed fields

### Constructor

* transforms a set of typed fields into an object

note:

They can be seen as opposites.
And this is an important observation when thinking about serialization.

---

## Serialization

* very important feature
* but many people hate its current implementation

### Drawbacks

* it undermines the accessibility model <!-- .element: class="fragment" -->
* serialization logic is not 'readable code' <!-- .element: class="fragment" -->
* it bypasses constructors and data validation <!-- .element: class="fragment" -->

note:

Serialization is a very important feature; it helped Java gain popularity in the late 90's due to its application in RMI.
But many people really hate its current implementation.
According to Java architect Brian Goetz: "even Gollum hates serialization".

* it undermines the accessibility model (field scraping through reflection magic)
* serialization logic is not 'readable code'
* it bypasses constructors and data validation

Not to worry!
Using patterns could improve the situation dramatically.

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
        effects = this.effects.toArray();
    }
}
</code></pre>

note:

We add a pattern definition to serialize our object. 
Let's assume that the serialized representation of an `EffectLoop` is a `String` and an `Effect` array.

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
        return effectLoop;
    }

    public pattern EffectLoop(String name, Effect[] effects) {
        name = this.name;
        effects = this.effects.toArray();
    }
}
</code></pre>

note:
Here, we add a factory method to deserialize our object. (we could also have used an overloaded constructor)
It converts a `String` and `Effect` array back to an `EffectLoop` object.
If the constructor had any data validation, it would be called here.

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
    public static EffectLoop deserialize(String name, Effect[] effects) {
        EffectLoop effectLoop = new EffectLoop(name);
        for (Effect effect : effects) {
            this.effects.add(effect);
        }
    }

    @Serializer
    public pattern EffectLoop(String name, Effect[] effects) {
        name = this.name;
        effects = this.effects.toArray();
    }
}
</code></pre>

note:

And to make the intent of the code even more clear, we use annotations.

We really improved on the drawbacks we indicated earlier!

* the accessibility model is no longer undermined
* serialization logic has become readable code!
* it no longer bypasses constructors and data validation

---

### Some challenges remain

**Q:** How to support multiple versions of one class?

<span class="fragment">
    <strong>A:</strong>  <code>@Serializer</code> and <code>@Deserializer</code> annotations could get a property <code>version</code> in the future.
</span>

note:

Of course, not *all* drawbacks are solved with this.
How will you support multiple versions of one class, for example?
Well, `@Serializer` and `@Deserializer` annotations could get a property `version` in the future.
So that you would be able to annotate multiple methods with these annotations to support multiple versions.

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
            <td><strong>n/a</strong></td>
            <td>Exploratory document</td>
            <td><a href="https://cr.openjdk.java.net/~briangoetz/amber/serialization.html">Towards Better Serialization</a></td>
        </tr>
    </tbody>
</table>

<br/>
<small>
<a href="https://cr.openjdk.java.net/~briangoetz/amber/serialization.html">https://cr.openjdk.java.net/~briangoetz/amber/serialization.html</a>
</small>

note:
Well, I said 'in the future', but actually this entire feature is still very much in the future.
 