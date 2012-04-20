SAV - memory model and arrays
=====================

From lecture on Friday 20th April.

Notation
--------

Object:
    identifier/name/reference
Value:
    data

(Note: nothing like C objects since we sematically copy values everywhere.)


Arrays
-------

Modelling assignments:

    a[i] = 5 → a = a(i := 5)

i.e. whole object is copied.

Weakest precondition:

    wp( a(i) = y, a(i) > 4 ∧ a(j) > 2 )
        =
        ( i==j ∧ y>4 ∧ y>2 ) ∨ ( i!=j ∧ y>4 ∧ a(j)>2 )

Note that this introduces a disjunction. An alternate way of writing this,
hiding (but not actually removing) the disjunction, is:

    a(i := y)(i) > 4
    a(i := y)(j) > 2    // contains disjunction


Array bounds checks
-----------------

Transform each array access to include bounds checks:

    a[i] → assert( i≥0 ∧ i≤a.size ); a[i]

What is `a.size`? A field, as below. What is `a[i]`? Actually, access to
another field:

    // x = a[i]
    assert( 0≤i ∧ i<size(a) )
    x = data(a,i);
    
    // a[i] = y
    assert ...
    data = data( (a,i) := y )


Fields
-----

Do a vertical split:

    x = a.foo

can be translated to:

    assert( a != null )
    x = foo( a )

where `foo` is a map returning the value stored. Assignment:

    // a.foo = y
    assert( a != null )
    foo = foo( a := y )


New values
----------

We can assume all identifiers always exist, but aren't always allocated. When
we introduce a new value (`x := new C()`) we need to tell the prover that `x`
is not the same object as any previously allocated value. Use a set `alloc`:

    // x = new C();
    havoc(x);    // new value
    assume (x ∉ alloc);
    alloc = alloc ∪ {x};
