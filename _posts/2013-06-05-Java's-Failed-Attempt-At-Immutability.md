---
layout: default
---
Java's Failed Attempt At Immutability
====================================
I have recently been reading over my old "Algorithms in a Nutshell" handbook and I have decided I would like to implement many - if not all - of the algorithms outlined in the book. In order to implement most of these I need to have a graph library: something powerful enough to work with the many different concepts within the book (directed edges, source/sink vertices, edge capacities, etc) and yet simple enough to not clutter my code. The obvious choice is JGraphT, but looking through the interfaces I was reminded of some major complaints I have had with how immutability is handled within Java libraries.

The Dreaded ImmutableX Class
---------------------------
You see it all the time: a library defines a root interface that is mutable, has classes implementing this interface, and then each class has an immutable flavour to cater to the functional crowd.
{% highlight java %}
public interface Graph<V, E> {
    public void add(V vertex);
    public boolean contains(V vertex);
    ...
}

public class SimpleGraph<V, E> implements Graph<V, E> {
    ...
}

// Similar to the class in JGraphT
public class ImmutableGraph<V, E> implements Graph<V, E> {
    @Override
    public void add(V vertex) {
        throw new UnsupportedOperationException();   
    } 
    ...
}
{% endhighlight %}
This is pretty typical and at first glance might seem perfectly acceptable. But consider the following code:
{% highlight java %}
public void doSomething() {
    Graph<String, String> graph = buildGraph();
    graph.add("New Vertex"); // Is this safe?
}

public Graph<String, String> buildGraph() {
    return new ImmutableGraph<String, String>();
}
{% endhighlight %}
Syntactically this is perfectly valid. Realistically this will explode the moment you hit the run button; attempting to add "New Vertex" to the ImmutableGraph will throw an UnsupportedOperationException.

Formally, I would say this is breaking the [Liskov Substitution Principle](http://en.wikipedia.org/wiki/Liskov_substitution_principle) - the [L in SOLID][1] - which states that:
[1]: http://en.wikipedia.org/wiki/SOLID_(object-oriented_design)
> If S is a subtype of T, then objects of type T may be replaced with objects of type S without altering any of the desirable properties of that program (correctness, task performed, etc.)
Introducing a class that will explode when a particular method is called adds uncertainty to the interface - any user of the Graph interface cannot be certain they can actually use the add method, which is an obvious code smell. The only way around this uncertainty is to create a copy of the graph in the subclass of your choice, something that is not always possible (is my Graph a DirectedSimpleGraph or a WeightedMultiGraph?).

I am a major proponent of immutability in my code but I will never use an ImmutableX class because of the uncertainty and complexity it adds to my projects.

Immutability First
-----------------
A much better way to handle this is to start with immutability and then introduce mutability later
{% highlight java %}
public interface Graph<V, E> {
    public boolean contains(V vertex);
    ...
}

public class SimpleGraph<V, E> implements Graph<V, E> {
    ...
}

public interface MutableGraph<V, E> extends Graph<V, E> {
    public void add(V vertex);
}
{% endhighlight %}
Adding mutability at a different layer lets us have immutable classes while also having full confidence in the interface we are using. "But how do we seed immutable classes with data?" you might ask. I'll address this issue in my next post.
