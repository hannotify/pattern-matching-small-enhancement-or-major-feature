<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->
# Pattern Kinds and Contexts <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

---

### Pattern Kinds

<table style="font-size: 70%">
    <thead>
        <tr>
            <th>Pattern kind</th>
            <th>Example</th>
            <th>Purpose</th>
        </tr>
    </thead>
    <tbody>
        <tr class="fragment">
            <td><em>type pattern</em></td>
            <td><code>Guitar lesPaul</code></td>
            <td>Perform an instanceof test, cast the target, and bind it to a pattern variable.</td>
        </tr>
        <tr class="fragment">
            <td><em>constant pattern</em></td>
            <td><code>GuitarType.TELECASTER</code></td>
            <td>Test the target for equality with a constant.</td>
        </tr>
        <tr class="fragment">
            <td><em>deconstruction pattern</em></td>
            <td><code>Delay(int timeInMs)</code></td>
            <td>Perform an instanceof test, cast the target, destructure the target and recursively match the components to subpatterns.</td>
        </tr>
        <tr class="fragment">
            <td><em>var pattern</em></td>
            <td><code>var depth</code></td>
            <td>Match anything and bind its target.</td>
        </tr>
        <tr class="fragment">
            <td><em>any pattern</em></td>
            <td><code>_</code></td>
            <td>Match anything, but bind nothing.</td>
        </tr>
    </tbody>
</table>

---

### Pattern Contexts

<table style="font-size: 70%">
    <thead>
        <tr>
            <th>Pattern context</th>
            <th>Example</th>
            <th>Purpose</th>
        </tr>
    </thead>
    <tbody>
        <tr class="fragment">
            <td><em>instanceof predicate</em></td>
            <td><code>product instanceof Guitar guitar</code></td>
            <td>Test if target matches the indicated pattern.</td>
        </tr>
        <tr class="fragment">
            <td><em>switch statement or expression</em></td>
            <td><code>switch (effect) {<br/>&nbsp;&nbsp;&nbsp;&nbsp;case Delay d -><br/>}</code></td>
            <td>Test if target matches one (or more) of the indicated patterns.</td>
        </tr>
        <tr class="fragment">
            <td><em>bind statement</em></td>
            <td><code>__let Reverb(var name, var roomSize) = reverb;</code></td>
            <td>Destructure a target using a pattern.</td>
        </tr>
    </tbody>
</table>
