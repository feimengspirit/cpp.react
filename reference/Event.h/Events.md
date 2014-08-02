---
layout: default
title: Events
type_tag: class
groups: 
 - {name: Home, url: ''}
 - {name: Reference , url: 'reference/'}
 - {name: Event.h, url: 'reference/Event.h/'}
---
An instance of this class acts as a proxy to an event stream node.
It takes shared ownership of the node, so while it exists, the node will not be destroyed.
Copy, move and assignment semantics are similar to `std::shared_ptr`.

## Synopsis
{% highlight C++ %}
template
<
    typename D,
    typename E = Token 
>
class Events
{
public:
    using DomainT = D;
    using ValueT = E;

    // Constructor
    Events();
    Events(const Events&);  // Copy
    Events(Events&&);       // Move

    // Assignemnt
    Events& operator=(const Events&);   // Copy
    Events& operator=(Events&& other);  // Move

    // Tests if two instances link to the same node
    bool Equals(const Events& other) const;

    // Tests if this instance is linked to a node
    bool IsValid() const;

    // Sets weight override for linked node
    void SetWeightHint(WeightHint hint);

    // Equivalent to react::Merge(*this, args ...)
    TempEvents<D,E,/*unspecified*/>
        Merge(const Events<D,TValues>& ... args) const;

    // Equivalent to react::Filter(*this, f)
    TempEvents<D,E,/*unspecified*/>
        Filter(F&& f) const;

    // Equivalent to react::Transform(*this, f)
    TempEvents<D,T,/*unspecified*/>
        Transform(F&& f) const;

    // Equivalent to react::Tokenize(*this)
    TempEvents<D,Token,/*unspecified*/>
        Tokenize() const;
};
{% endhighlight %}

## Template parameters
<table class="wide_table">
    <tr>
        <td class="descriptor_cell">D</td>
        <td>The domain this event stream belongs to. Aliases as member type <code>DomainT</code>.</td>
    </tr>
    <tr>
        <td class="descriptor_cell">E</td>
        <td>
            Event value type. Aliased as member type <code>ValueT</code>. If this parameter is omitted, <code>Token</code> is used as the default.<br/>
            Should be <code>DefaultConstructible</code>, <code>CopyConstructible</code> and <code>CopyAssignable</code>.<br/>
            Can be <code>MoveConstructible</code> and <code>MoveAssignable</code> to avoid copying when possible.
        </td>
    </tr>
</table>

-----

<h1>Constructor <span class="type_tag">member function</span></h1>

## Syntax
{% highlight C++ %}
Events();                    // (1)
Events(const Events& other); // (2)
Events(Events&& other);      // (3)
{% endhighlight %}

## Semantics
(1) Creates an invalid event stream that is not linked to an event node.

(2) Creates an event stream that links to the same event node as `other`.

(3) Creates an event stream that moves shared ownership of the event node from `other` to `this`.
As a result, `other` becomes invalid.

**Note:** The default constructor creates an invalid proxy, which is equivalent to `std::shared_ptr(nullptr)`.

-----

<h1>operator= <span class="type_tag">member function</span></h1>

## Syntax
{% highlight C++ %}
Events& operator=(const Events&);   // (1)
Events& operator=(Events&& other);  // (2)
{% endhighlight %}

## Semantics
(1) Links `this` to the same node as `other`. If `this` was already linked to another node, it releases its previous ownership.

(2) Transfers shared ownership of the linked node from `other` to `this`.
If `this` was already linked to another node, it release its previous ownership.
As a result, `other` becomes invalid.

-----

<h1>Equals <span class="type_tag">member function</span></h1>

## Syntax
{% highlight C++ %}
bool Equals(const Events& other) const;
{% endhighlight %}

## Semantics
Returns true, if both `this` and `other` link to the same event node.
Since some reactive types use `==` as a combination operator, this function is used for unambiguous comparison.

-----

<h1>IsValid <span class="type_tag">member function</span></h1>

## Syntax
{% highlight C++ %}
bool IsValid() const;
{% endhighlight %}

## Semantics
Returns true, if `this` is linked to an event node.

-----

<h1>Merge, Filter, Transform, Tokenize <span class="type_tag">member function</span></h1>

## Syntax
{% highlight C++ %}
template <typename ... TValues>
TempEvents<D,E,/*unspecified*/> Merge(const Events<D,TValues>& ... args) const;

template <typename F>
TempEvents<D,E,/*unspecified*/> Filter(F&& f) const;

template <typename F>
TempEvents<D,T,/*unspecified*/> Transform(F&& f) const;

TempEvents<D,Token,/*unspecified*/> Tokenize() const;
{% endhighlight %}

## Semantics
Semantically equivalent to the respective free functions in namespace `react`.