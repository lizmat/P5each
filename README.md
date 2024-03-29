[![Actions Status](https://github.com/lizmat/P5each/workflows/test/badge.svg)](https://github.com/lizmat/P5each/actions)

NAME
====

Raku port of Perl's each() built-in

SYNOPSIS
========

    use P5each;

DESCRIPTION
===========

This module tries to mimic the behaviour of the Perl's `each` built-in as closely as possible in the Raku Programming Language.

ORIGINAL PERL 5 DOCUMENTATION
=============================

    each HASH
    each ARRAY
    each EXPR
            When called on a hash in list context, returns a 2-element list
            consisting of the key and value for the next element of a hash. In
            Perl 5.12 and later only, it will also return the index and value
            for the next element of an array so that you can iterate over it;
            older Perls consider this a syntax error. When called in scalar
            context, returns only the key (not the value) in a hash, or the
            index in an array.

            Hash entries are returned in an apparently random order. The
            actual random order is specific to a given hash; the exact same
            series of operations on two hashes may result in a different order
            for each hash. Any insertion into the hash may change the order,
            as will any deletion, with the exception that the most recent key
            returned by "each" or "keys" may be deleted without changing the
            order. So long as a given hash is unmodified you may rely on
            "keys", "values" and "each" to repeatedly return the same order as
            each other. See "Algorithmic Complexity Attacks" in perlsec for
            details on why hash order is randomized. Aside from the guarantees
            provided here the exact details of Perl's hash algorithm and the
            hash traversal order are subject to change in any release of Perl.

            After "each" has returned all entries from the hash or array, the
            next call to "each" returns the empty list in list context and
            "undef" in scalar context; the next call following that one
            restarts iteration. Each hash or array has its own internal
            iterator, accessed by "each", "keys", and "values". The iterator
            is implicitly reset when "each" has reached the end as just
            described; it can be explicitly reset by calling "keys" or
            "values" on the hash or array. If you add or delete a hash's
            elements while iterating over it, the effect on the iterator is
            unspecified; for example, entries may be skipped or duplicated--so
            don't do that. Exception: It is always safe to delete the item
            most recently returned by "each()", so the following code works
            properly:

                    while (($key, $value) = each %hash) {
                      print $key, "\n";
                      delete $hash{$key};   # This is safe
                    }

            This prints out your environment like the printenv(1) program, but
            in a different order:

                while (($key,$value) = each %ENV) {
                    print "$key=$value\n";
                }

            Starting with Perl 5.14, "each" can take a scalar EXPR, which must
            hold reference to an unblessed hash or array. The argument will be
            dereferenced automatically. This aspect of "each" is considered
            highly experimental. The exact behaviour may change in a future
            version of Perl.

                while (($key,$value) = each $hashref) { ... }

            As of Perl 5.18 you can use a bare "each" in a "while" loop, which
            will set $_ on every iteration.

                while(each %ENV) {
                    print "$_=$ENV{$_}\n";
                }

            To avoid confusing would-be users of your code who are running
            earlier versions of Perl with mysterious syntax errors, put this
            sort of thing at the top of your file to signal that your code
            will work only on Perls of a recent vintage:

                use 5.012;  # so keys/values/each work on arrays
                use 5.014;  # so keys/values/each work on scalars (experimental)
                use 5.018;  # so each assigns to $_ in a lone while test

            See also "keys", "values", and "sort".

PORTING CAVEATS
===============

Using list assignments in `while` loops will not work, because the assignment will happen anyway even if an empty list is returned, so that this:

    while (($key, $value) = each %hash) { }

will loop forever. There is unfortunately no way to fix this in Raku module space at the moment. But a slightly different syntax, will work as expected:

    while each(%hash) -> ($key,$value) { }

Also, this will alias the values in the list, so you don't actually need to define `$key` and `$value` outside of the `while` loop to make this work.

AUTHOR
======

Elizabeth Mattijsen <liz@raku.rocks>

Source can be located at: https://github.com/lizmat/P5each . Comments and Pull Requests are welcome.

COPYRIGHT AND LICENSE
=====================

Copyright 2018, 2019, 2020, 2021 Elizabeth Mattijsen

Re-imagined from Perl as part of the CPAN Butterfly Plan.

This library is free software; you can redistribute it and/or modify it under the Artistic License 2.0.

