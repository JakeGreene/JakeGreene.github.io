---
layout: "default"
icon: "&#9749;"
---
Java Geometry
============
During my exam break I started to play with lwjgl (Light Weight Java Graphics Library) so that I can use it this summer for my various graphics-oriented projects. It didn't take me very long to realize that I need a geometry library in order to do anything more complicated than a "hello world" program in OpenGL.

The Search
----------
After some searching I found that the geometry libraries for Java currently fall under two flavours:

* A General Vector/Point Class. This class represents all dimensions and accepts/returns Vector/Point in its methods.
* Separate VectorXD and PointXD Classes. These classes represent the 2D and 3D flavours and only accept/return similar types in their methods.

The Problem
-----------
The General Vector has its advantages; all vectors have the same implementation and therefore the same code base and interface. However, some vector operations are dimension specific (eg. cross product for 2D and 3D vectors have different return types) and some methods should only work on a vector with the same dimension (eg. 3D vector cannot perform dot product with 2D vector without making assumptions about the default z-component). This leads to either vectors with lower dimensions having their missing components filled in with default values or exceptions being thrown when the dimensions do not match.
The Separate Vectors solve the problem of dimension specific operations but the drawbacks are worse. Most (if not all) libraries that take this approach do not have Vector2D share an interface or parent class with Vector3D (same with Points) and so it is not possible to have a list of any Vector or have a generic class work with 2D or 3D objects depending on how it is initialized.

The Solution
------------
Ideally I want something in between the two; I need common interfaces so that I can define classes to work with either one depending on the application (ie I do not have to write separate classes for 2D and 3D) but I also want to be able to limit the dimension of the points I am using and have errors appear at compile time (ie difference in dimension is a syntax error and not a logic error).

I've chosen to create my own library with the following design in mind:

* A common interface for points/vectors of differing dimension
* Concrete subclasses that provide dimension specific functionality
* Dimension errors are syntax errors
* Immutable. I am a huge fan of immutability when it comes to geometric primitives (something most libraries seem to disagree with) because it enhances readability and provides thread safety
* Factory Methods for creation. I almost always prefer factory methods over constructors. Factory methods provide readability (eg create(x, y, z) and createFromAngles(incline, azimuth, length)) while also giving the library designer (ie. me) a lot more flexibility with how the thing is created in the back-end.
The solution is pretty simple: use Java Generics to specify dimensionality.
{% highlight java %}
Point<D2> point = Points.new2DPoint(x, y)
{% endhighlight %}
I would love if Java allowed numbers at the beginning of a class name so that D2 could be 2D. Oh well. Using generics gives me the freedom to create classes that can work with either 2D or 3D points/vectors

{% highlight java %}
public class MyClass<D extends Dimension> {
    Point<D> point;
    Vector<D> vector;
}
{% endhighlight %}
Many of the methods only accept a Point/Vector of the same dimension. Trying to do otherwise is a syntax error.

{% highlight java %}
Vector<D2> vector ...
Vector<D2> other ...
Vector<D2> result = vector.dot(other); // syntactically valid
Vector<D3> badOther ...
vector.dot(badOther); // syntactically invalid
{% endhighlight %}

Concrete classes exist which provide dimension specific functionality while also implementing the generic interface.

{% highlight java %}
Vector3D vector = Vector3D.newVector(x, y, z);
double x = vector.getX();
Vector3D cross = vector.cross(other3DVector);
{% endhighlight %}

I am pretty happy with the results. One thing to note is the underlying primitive for the point/vector components is Double; I considered making the underlying data-type a generic parameter as well but it clutters the interface and doesn't give much in return. I plan on expanding upon this idea all summer. The source is apart of my java utility project on GitHub.
