<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->

# Pattern Contexts <!-- .element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

note:

**Target time: --m**

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
            <td><em>enhanced for</em></td>
            <td><code>for (Delay(int timeInMs) : delays) {<br/>}</code></td>
            <td>Perform a iteration for each target that matches the indicated pattern.</td>
        </tr>        
        <!-- <tr class="fragment">
            <td><em>bind statement</em></td>
            <td><code>__let Reverb(var name, var roomSize) = reverb;</code></td>
            <td>Destructure a target using a pattern.</td>
        </tr> -->
    </tbody>
</table>
