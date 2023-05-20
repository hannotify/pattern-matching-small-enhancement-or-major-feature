<!-- .slide: data-background="img/background/final-puzzle-piece.jpg" data-background-color="black" data-background-opacity="0.4" -->

# Practical implications <!--.element: class="stroke" -->

<https://pxhere.com/en/photo/752901> <!-- .element: class="attribution" -->

note:

**Target time: 47m**

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
