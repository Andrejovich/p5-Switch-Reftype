# NAME

Switch::Reftype - Execute code based on which type of reference is given.

# VERSION

version 0.001

# SYNOPSIS

    # switch-like statement on the reftype of a given variable:
    use Switch::Reftype;              # switch_reftype is imported by default.
    $result = switch_reftype $foo,
      SCALAR      => sub {...},       # Run when $foo is a SCALAR reference
      ARRAY       => sub {...},       # Run when $foo is an ARRAY reference
      HASH        => sub {...},       # Run when $foo is a HASH reference
      CODE        => sub {...},       # Run when $foo is a CODE reference
      REF         => sub {...},       # Run when $foo is a REF reference
      GLOB        => sub {...},       # Run when $foo is a GLOB reference
      LVALUE      => sub {...},       # Run when $foo is an LVALUE reference
      FORMAT      => sub {...},       # Run when $foo is a FORMAT reference
      IO          => sub {...},       # Run when $foo is an IO reference
      VSTRING     => sub {...},       # Run when $foo is a VSTRING reference
      REGEXP      => sub {...},       # Run when $foo is a Regexp reference
      scalar      => sub {...},       # Run when $foo isn't a reference
      undef       => sub {...},       # Run when not defined $foo
      default     => sub {...},       # Run when the reftype of $foo isn't given
    ;
    
    # map-like functions. $foo is aliassed to $_ inside the BLOCK:
    use Switch::Reftype ':all';       # Import all functions.
    $result = if_SCALAR {...} $foo;
    $result = if_ARRAY {...} $foo;
    $result = if_HASH {...} $foo;
    $result = if_CODE {...} $foo;
    $result = if_REF {...} $foo;
    $result = if_GLOB {...} $foo;
    $result = if_LVALUE {...} $foo;
    $result = if_FORMAT {...} $foo;
    $result = if_IO {...} $foo;
    $result = if_VSTRING {...} $foo;
    $result = if_REGEXP {...} $foo;

# DESCRIPTION

Sometimes, you want your subroutine or method to perform a specific action based
on the type of argument given. For example, your subroutine might accept either
a simple scalar, an array reference, or a hash reference. Depending on which
type of argument your subroutine got, it has to act slightly differently. The
Switch::Reftype family of functions help you to easily codify these differences.

Switch::Reftype relies heavily on ["reftype" in Scalar::Util](https://metacpan.org/pod/Scalar::Util#reftype) from
[Scalar::Util](https://metacpan.org/pod/Scalar::Util) module.

# FUNCTIONS

## switch\_reftype

    switch_reftype $reference, %reftypes

The keys of `%reftypes` should correspond with the possible return values of
["reftype" in Scalar::Util](https://metacpan.org/pod/Scalar::Util#reftype) (for a complete list, see the ["SYNOPSIS"](#synopsis)).

If `$reference` isn't a reference but just another scalar, `switch_reftype`
will look for the element with key `'scalar'`. Likewise, if `$reference`
isn't defined, it will look for key `'undef'`. If `$referencer` is both
defined and a reference, `switch_reftype` will use Scalar::Util's
`reftype $reference` to determine which key to look for.

If no suitable key/value pair was given (e.g. `$reference` is an `ARRAY`
reference but no `ARRAY => sub {...}` pair exists), `switch_reftype` will
look for the key `'default'`.

If, at this point, still absolutely no appropriate key/value pair was found,
`switch_reftype` gives up and returns undef (in scalar context) or the empty
list (in list context).

Otherwise, it calls the found subref and returns whatever that returns. Inside
the subref, `$reference` is aliassed to `$_`.

## if\_SCALAR

    if_SCALAR {...} $reference;

Syntactic sugar for

    switch_ref $reference
      SCALAR => sub {...},
      default => sub { $reference }
    ;

In other words, it calls the subroutine and returns whatever that returns if
`$reference` is a reference to a scalar, or else it just returns `$reference`.

## if\_ARRAY

    if_ARRAY {...} $reference

Like ["if\_SCALAR"](#if_scalar), but for ARRAY references.

## if\_HASH

    if_HASH {...} $reference

Like ["if\_SCALAR"](#if_scalar), but for HASH references.

## if\_CODE

    if_CODE {...} $reference

Like ["if\_SCALAR"](#if_scalar), but for CODE references.

## if\_REF

    if_REF {...} $reference

Like ["if\_SCALAR"](#if_scalar), but for REF references.

That is, references to references, as in:

    $ref = \"foo";    # Normal SCALAR reference
    $refref = \\$ref; # REF reference

## if\_GLOB

    if_GLOB {...} $reference

Like ["if\_SCALAR"](#if_scalar), but for GLOB references (i.e. `\*foo`)

## if\_LVALUE

    if_LVALUE {...} $reference

Like ["if\_SCALAR"](#if_scalar), but for LVALUE references.

LVALUE references really get into the guts of what you can do with references.
It's beyond the scope of this document to explain them, but see [ref](https://metacpan.org/pod/ref). Mostly
included for completeness' sake.

## if\_FORMAT

    if_FORMAT {...} $reference

Like ["if\_SCALAR"](#if_scalar), but for FORMAT references (see [format](https://metacpan.org/pod/format)). Mostly
included for completeness' sake.

## if\_IO {...} 

    if_IO {...} $reference

Like ["if\_SCALAR"](#if_scalar), but for IO references (i.e. `*STDIN{IO}`). Mostly
included for completeness' sake.

## if\_VSTRING

    if_VSTRING {...} $reference

Like ["if\_SCALAR"](#if_scalar), but for VSTRING references (i.e. `\v127.0.0.1`).

## if\_REGEXP

    if_REGEXP {...} $reference

Like ["if\_SCALAR"](#if_scalar), but for Regexp references (i.e. `qr/.../`).

# AUTHOR

P. Ramakers &lt;pramakers@cpan.org>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2015 by P. Ramakers.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
