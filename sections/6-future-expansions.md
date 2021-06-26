<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
# Future Expansions <!-- .element: class="stroke" -->

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
Again, 'here be dragons'!

---

<!-- .slide: data-auto-animate" -->

### Pattern bind statements

<pre data-id="pattern-bind-statements-animation"><code class="java" data-trim data-line-numbers>
var reverb = new Reverb("ChamberReverb", 2);

__let Reverb(String name, int roomSize) = reverb;

// do something with name & roomSize
</code></pre>

<https://cr.openjdk.java.net/~briangoetz/amber/pattern-match.html> <!-- .element: class="attribution" -->

note:
A pattern binding statement destructures a target using a pattern.
Here, we assert that the pattern will match, so we destructure the target and bind its components to new variables. 

BTW - the "double underscore" syntax is a convention used by the Java language designers to suggest that the syntax is merely a placeholder, for purposes of exposition. 
So we can't be sure at all that `let` will be the actual keyword for pattern bind statements.

---

<!-- .slide: data-auto-animate" -->

### Pattern bind statements

<pre data-id="pattern-bind-statements-animation"><code class="java" data-trim data-line-numbers="4">
var reverb = new Reverb("ChamberReverb", 2);

__let Reverb(String name, int roomSize) = reverb;
else throw new IllegalArgumentException("not a Reverb!");

// do something with name & roomSize
</code></pre>

note:
Now if we cannot guarantee the pattern will match, we can provide an else clause like this.

---

### Other ideas

<ul>
    <li>Deconstruction patterns for all classes</li>
    <li>Enhanced array patterns<br/><small><code>String[] { [8] -> var eighthElement, [9] -> var ninthElement}</code></small></li>
    <li>AND patterns</li>
    <li>Patterns in <code>catch</code> clauses</li>
    <li>Collection patterns</li>
</ul>

<https://mail.openjdk.java.net/pipermail/amber-spec-experts/2021-January/002758.html> <!-- .element: class="attribution" -->

note:
As you can see, the details are getting murkier by the minute.
This is because these are **very new ideas** and are likely to change. 
Or to not be ever implemented at all.

---

<!---.slide: data-visibility="hidden" -->

### Other ideas

<ul>
    <li class="fragment">AND patterns:<br/><code>PatternOne&PatternTwo</code>
    <li class="fragment">Patterns in <code>catch</code> clauses:<br/><small>(will most likely complement multi-catch blocks)</small>
    <li class="fragment">Collection patterns
</ul>

<https://mail.openjdk.java.net/pipermail/amber-spec-experts/2021-January/002758.html> <!-- .element: class="attribution" -->

note:
As you can see, the details are getting murkier by the minute.
This is because these are **very new ideas** and are likely to change. 
Or to not be ever implemented at all.
