# NAME [![Test in a Raku container](https://github.com/raku-community-modules/Test-Output/actions/workflows/test.yaml/badge.svg)](https://github.com/raku-community-modules/Test-Output/actions/workflows/test.yaml)

Test::Output - Test the output to STDOUT and STDERR your program generates

# TABLE OF CONTENTS

- [NAME](#name)
- [SYNOPSIS](#synopsis)
- [DESCRIPTION](#description)
- [EXPORTED SUBROUTINES](#exported-subroutines)
    - [`is` Tests](#is-tests)
        - [`output-is`](#output-is)
        - [`stdout-is`](#stdout-is)
        - [`stderr-is`](#stderr-is)
    - [`like` Tests](#like-tests)
        - [`output-like`](#output-like)
        - [`stdout-like`](#stdout-like)
        - [`stderr-like`](#stderr-like)
    - [Output Capture](#output-capture)
        - [`output-from`](#output-from)
        - [`stdout-from`](#stdout-from)
        - [`stderr-from`](#stderr-from)
- [REPOSITORY](#repository)
- [BUGS](#bugs)
- [AUTHOR](#author)
- [LICENSE](#license)

# SYNOPSIS

```raku
    use v6.d;
    use Test;
    use Test::Output;

    my &test-code = sub {
        say 42;
        note 'warning!';
        say "After warning";
    };

    # Test code's output using exact match ('is')
    output-is   &test-code, "42\nwarning!\nAfter warning\n", 'testing output';
    stdout-is   &test-code, "42\nAfter warning\n",  'testing stdout';
    stderr-is   &test-code, "warning!\n", 'testing stderr';

    # Test code's output using regex ('like')
    output-like &test-code, /42.+warning.+After/, 'testing output (regex)';
    stdout-like &test-code, /42/, 'testing stdout (regex)';
    stderr-like &test-code, /^ "warning!\n" $/, 'testing stderr (regex)';

    # Just capture code's output and do whatever you want with it
    is output-from( &test-code ), "42\nwarning!\nAfter warning\n";
    is stdout-from( &test-code ), "42\nAfter warning\n";
    is stderr-from( &test-code ), "warning!\n";

```

# DESCRIPTION

This module allows you to capture the output (STDOUT/STDERR/BOTH) of a
piece of code and evaluate it for some criteria. It needs version 6.d
of the language, since it's following specs that were deployed for
that version. If you need to go with 6.c, download 1.001001
from
[here](https://github.com/raku-community-modules/Test-Output/releases) or
via  `git clone`+

    git checkout v1.001001
    zef install .

# EXPORTED SUBROUTINES

## `is` Tests

### `output-is`

![][sub-signature]
```raku
    sub output-is (&code, Str $expected, Str $desc? );
```

![][sub-usage-example]
```raku
    output-is { say 42; note 43; say 44 }, "42\n43\n44\n",
        'Merged output from STDOUT/STDERR looks fine!';
```

Uses `is` function from `Test` module to test whether the combined
STDERR/STDOUT output from a piece of code matches the given string. Takes
an **optional** test description.

----

### `stdout-is`

![][sub-signature]
```raku
    sub stdout-is (&code, Str $expected, Str $desc? );
```

![][sub-usage-example]
```raku
    stdout-is { say 42; note 43; say 44 }, "42\n44\n", 'STDOUT looks fine!';
```

Same as [`output-is`](#output-is), except tests STDOUT only.

----

### `stderr-is`

![][sub-signature]
```raku
    sub stderr-is (&code, Str $expected, Str $desc? );
```

![][sub-usage-example]
```raku
    stderr-is { say 42; note 43; say 44 }, "43\n", 'STDERR looks fine!';
```

Same as [`output-is`](#output-is), except tests STDERR only.

----

## `like` Tests

### `output-like`

![][sub-signature]
```raku
    sub output-like (&code, Regex $expected, Str $desc? );
```

![][sub-usage-example]
```raku
    output-like { say 42; note 43; say 44 }, /42 .+ 43 .+ 44/,
        'Merged output from STDOUT/STDERR matches the regex!';
```

Uses `like` function from `Test` module to test whether the combined
STDERR/STDOUT output from a piece of code matches the given `Regex`. Takes
an **optional** test description.

----

### `stdout-like`

![][sub-signature]
```raku
    sub stdout-like (&code, Regex $expected, Str $desc? );
```

![][sub-usage-example]
```raku
    stdout-like { say 42; note 43; say 44 }, /42 \n 44/,
        'STDOUT matches the regex!';
```

Same as [`output-like`](#output-like), except tests STDOUT only.

----

### `stderr-like`

![][sub-signature]
```raku
    sub stderr-like (&code, Regex $expected, Str $desc? );
```

![][sub-usage-example]
```raku
    stderr-like { say 42; note 43; say 44 }, /^ 43\n $/,
        'STDERR matches the regex!';
```

Same as [`output-like`](#output-like), except tests STDERR only.

----

## Output Capture

### `output-from`

![][sub-signature]
```raku
    sub output-from (&code) returns Str;
```

![][sub-usage-example]
```raku
    my $output = output-from { say 42; note 43; say 44 };
    say "Captured $output from our program!";

    is $output, "42\nwarning!\nAfter warning\n",
        'captured merged STDOUT/STDERR look fine';
```

Captures and returns merged STDOUT/STDERR output from the given piece of code.

----

### `stdout-from`

![][sub-signature]
```raku
    sub stdout-from (&code) returns Str;
```

![][sub-usage-example]
```raku
    my $stdout = stdout-from { say 42; note 43; say 44 };
    say "Captured $stdout from our program!";

    is $stdout, "42\nAfter warning\n", 'captured STDOUT looks fine';
```

Same as [`output-from`](#output-from), except captures STDOUT only.

----

### `stderr-from`

![][sub-signature]
```raku
    sub stderr-from (&code) returns Str;
```

![][sub-usage-example]
```raku
    my $stderr = stderr-from { say 42; note 43; say 44 };
    say "Captured $stderr from our program!";

    is $stderr, "warning\n", 'captured STDERR looks fine';
```

Same as [`output-from`](#output-from), except captures STDERR only.

----

### `test-output-verbosity`

![][sub-signature]
```raku
    sub test-output-verbosity (Bool :$on, Bool :$off) returns Str;
```

![][sub-usage-example]
```raku
    # turn verbosity on
    test-output-verbosity(:on);
    
    my $output = output-from { do-something-interactive() };
    # test output will now displayed during the test
    
    # turn verbosity off
    test-output-verbosity(:off);
```

Display the code's output while the test code is executed. This can be
very useful for author tests that require you to enter input based on
the output.

----

# REPOSITORY

Fork this module on GitHub:
https://github.com/raku-community-modules/Test-Output

# BUGS

To report bugs or request features, please use
https://github.com/raku-community-modules/Test-Output/issues

# ORIGINAL AUTHOR

Zoffix Znet (http://zoffix.com/)

# LICENSE

You can use and distribute this module under the terms of the
The Artistic License 2.0. See the `LICENSE` file included in this
distribution for complete details.

[sub-signature]: _chromatin/sub-signature.png

[sub-usage-example]: _chromatin/sub-usage-example.png
