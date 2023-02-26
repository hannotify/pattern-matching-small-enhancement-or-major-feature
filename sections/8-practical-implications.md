<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->

# Practical implications <!--.element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

note:

**Target time: 46m**

---

### Functional programming

<pre><code class="haskell" data-trim data-line-numbers>
evalExpression :: (Num a) => (a, Char, a) -> a
evalExpression (x, '+', y) = x + y  -- Match on operator '+'
evalExpression (x, '*', y) = x * y  -- Match on operator '*'

eval [Left x] = x  -- base case: if there's only one number left, return it
eval (Left x : Right op : xs) = evalExpression (x, op, eval xs)  
-- evaluates the first subexpression and recurse on the rest

eval [Left 5, Right '*', Left 2, Right '*', Left 4, Right '+', Left 2] -- ((5 * 2) * 4) + 2 = 42
</code></pre>

<small> Note: this example does not consider precedence. </small>

---

### Functional programming

<pre><code class="java" data-trim data-line-numbers>
sealed interface Expr permits Add, Mul, Val {}

record Add(Expr e1, Expr e2) implements Expr {}
record Mul(Expr e1, Expr e2) implements Expr {}
record Val(Integer val) implements Expr {}

static Integer calc(Expr e) {
    switch (e) {
        case Add(Expr e1, Expr e2) -> { return calc(e1) + calc(e2); }
        case Mul(Expr e1, Expr e2) -> { return calc(e1) * calc(e2); }
        case Val v -> { return v.val(); }
    }
}
</code></pre>

<small> Note: this example does not consider precedence. </small>

---

<!-- .slide: data-auto-animate" -->

### Processing messages

<pre data-id="message-processing"><code class="java" data-trim line-numbers>
public static LoggableMessage fromMessage(Object message) {
    if (message instanceof EventMessage&lt;?&gt;) {
        return fromDomainEventMessage((EventMessage&lt;?&gt;) message);
    } else if (message instanceof CommandMessage&lt;?&gt;) {
        return fromCommandMessage((CommandMessage&lt;?&gt;) message);
    } else if (message instanceof org.springframework.amqp.core.Message) {
        return fromAMQPMessage((org.springframework.amqp.core.Message) message);
    } else {
        return unknownMessage(message);
    }
}
</code></pre>

---

<!-- .slide: data-auto-animate" -->

### Processing messages

<pre data-id="message-processing"><code class="java" data-trim line-numbers >
public static LoggableMessage fromMessage(Object message) {
    return switch (message) {
        case EventMessage&lt;?&gt; eventMessage -> fromDomainEventMessage(eventMessage);
        case CommandMessage&lt;?&gt; commandMessage -> fromCommandMessage(commandMessage);
        case org.springframework.amqp.core.Message amqpMessage -> fromAMQPMessage(amqpMessage);
        default -> unknownMessage(message);
    };
}
</code></pre>