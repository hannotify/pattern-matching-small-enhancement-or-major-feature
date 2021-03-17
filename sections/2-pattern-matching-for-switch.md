<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
# Pattern Matching for <!-- .element: class="stroke" -->
# `switch` <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

---

<!-- .slide: data-background="img/background/wall-disclaimer.jpg" data-background-color="black" data-background-opacity="0.5" -->
## Disclaimer <!-- .element: class="stroke" -->

<blockquote class="explanation">
    I can't tell you when the following features are coming to Java.<br/>
    Also: syntax and implementation specifics may still change.
</blockquote>

<https://pxhere.com/en/photo/1359311> <!-- .element: class="attribution" -->

---

<!-- .slide: data-background="img/background/stompboxes.jpg" data-background-color="black" data-background-opacity="0.4" -->
![music-store-phase-4](diagrams/music-store-phase-4.puml.png "Music store class diagram")

<https://pxhere.com/en/photo/544037> <!-- .element: class="attribution" -->

note:
Let's finalize the local music store code.
By introducing all available effects.

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
        formatted = el.getEffects().stream().map(this::apply).collect(Collectors.joining(System.lineSeparator()));
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

## Benefits

* A single expression instead of many assignments
* Less error-prone (in adding cases)
* More concise
* Safer - the compiler can check for missing cases

---

<!-- .slide: data-auto-animate" -->
### `switch` grammar

<pre data-id="switch-grammar-animation"><code class="java" data-trim data-line-numbers>
SwitchBlock:
  { SwitchRule {SwitchRule} }
  { {SwitchBlockStatementGroup} {SwitchLabel :} }

SwitchRule:
  SwitchLabel -> Expression ;
  SwitchLabel -> Block
  SwitchLabel -> ThrowStatement

SwitchBlockStatementGroup:
  SwitchLabel : {SwitchLabel :} BlockStatements

SwitchLabel:
  case CaseConstant {, CaseConstant}
  default

CaseConstant:
  ConditionalExpression
</code></pre>

<https://docs.oracle.com/javase/specs/jls/se15/html/jls-14.html#jls-14.11> <!-- .element: class="attribution" -->

note:
So what does this mean for the `switch` grammar?
Well...

---

<!-- .slide: data-auto-animate" -->
### `switch` grammar

<pre data-id="switch-grammar-animation"><code class="java" data-trim data-line-numbers="14,17-18">
SwitchBlock:
  { SwitchRule {SwitchRule} }
  { {SwitchBlockStatementGroup} {SwitchLabel :} }

SwitchRule:
  SwitchLabel -> Expression ;
  SwitchLabel -> Block
  SwitchLabel -> ThrowStatement

SwitchBlockStatementGroup:
  SwitchLabel : {SwitchLabel :} BlockStatements

SwitchLabel:
  case CaseConstant {, CaseConstant}
  default

CaseConstant:
  ConditionalExpression
</code></pre>

<https://docs.oracle.com/javase/specs/jls/se15/html/jls-14.html#jls-14.11> <!-- .element: class="attribution" -->

note:
These are the relevant bits.
And when 'Pattern Matching for switch' is available, it will probably look like...

---

<!-- .slide: data-auto-animate" -->
### `switch` grammar

<pre data-id="switch-grammar-animation"><code class="java" data-trim data-line-numbers="14,17-25">
SwitchBlock:
  { SwitchRule {SwitchRule} }
  { {SwitchBlockStatementGroup} {SwitchLabel :} }

SwitchRule:
  SwitchLabel -> Expression ;
  SwitchLabel -> Block
  SwitchLabel -> ThrowStatement

SwitchBlockStatementGroup:
  SwitchLabel : {SwitchLabel :} BlockStatements

SwitchLabel:
  case Pattern {, Pattern}
  default

Pattern:
  ConstantPattern
  TypePattern

ConstantPattern:
  ConditionalExpression

TypePattern:
  ReferenceType Identifier
</code></pre>

note:
...this.

I can't be entirely sure how this will look, but I *do* know that the case constants that are currently supported:

* `String` literals
* `enum` constants
* numeric constants

will be called 'constant patterns' in the future.
This enables the switch label to take a generic `Pattern`.

---

<!-- .slide: data-background="img/background/freddie-mercury.jpg" data-background-color="black" data-background-opacity="0.4" -->
## It's a kind of Pattern <!-- .element: class="stroke" -->

<blockquote class="explanation">
    <strong>constant pattern</strong>
    <br/>
    <code data-trim data-noescape>
    GuitarType.TELECASTER
    </code>
</blockquote>

<https://www.pexels.com/photo/gray-metal-statue-of-man-raising-hand-near-dock-825430/> <!-- .element: class="attribution" -->

note:
It can be an enum constant, as depicted here, or a `String` literal, or a numeric constant.

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
            <td>Draft</td>
            <td><a href="https://openjdk.java.net/jeps/8213076">JEP draft 8213076</a></td>
        </tr>
    </tbody>
</table>

<https://openjdk.java.net/jeps/8213076> <!-- .element: class="attribution" -->

---

<!-- .slide: data-background="img/background/joker.jpg" data-background-color="black" data-background-opacity="0.4" -->
## Why so serious?

<ul>
    <li class="fragment"><em>Surely</em> a less invasive approach exists?</li>
    <li class="fragment"><strong>Type switching</strong> has been considered.</li>
    <li class="fragment">It enables case labels to specify types, as well as constants.</li>
    <li class="fragment">But... it is suited for <code>switch</code> statements only.</li>
    <li class="fragment">And pattern matching can be useful for more language concepts!</li>
</ul>

<https://pxhere.com/en/photo/835435> <!-- .element: class="attribution" -->

note:
But why such a serious, generic solution?
"Gwhy... So... Seriousa?"
Surely a less invasive approach exists?
Yes, it does. And it has been considered by the language designers.
_Type switching_ is a proposed mechanism that enables case labels to specify types as well as constants.
Sounds useful, right?
But... it is suited for `switch` statements only.
Whereas *pattern matching* can do the same, **and** be useful for more language concepts.
Such as deconstruction!
