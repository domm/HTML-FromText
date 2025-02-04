# NAME

HTML::FromText - converts plain text to HTML

# VERSION

version 2.08

# SYNOPSIS

    use HTML::FromText;
    text2html( $text, %options );

    # or

    use HTML::FromText ();
    my $t2h  = HTML::FromText->new( \%options );
    my $html = $t2h->parse( $html );

# DESCRIPTION

`HTML::FromText` converts plain text to HTML. There are a handful of
options that shape the conversion. There is a utility function,
`text2html`, that's exported by default. This function is simply a short-
cut to the Object Oriented interface described in detail below.

# METHODS

## new

    my $t2h = HTML::FromText->new({
        paras      => 1,
        blockcode  => 1,
        tables     => 1,
        bullets    => 1,
        numbers    => 1,
        urls       => 1,
        email      => 1,
        bold       => 1,
        underline  => 1,
    });

Constructs a new `HTML::FromText` object using the given
configuration. The resulting object can parse lots of objects using the
`parse` method.

Options to `new` are passed by name, with the value being either true
or false. If true, the option will be turned on. If false, it will be
turned off.  The following outlines all the options.

#### Decorators

- metachars

    This option is on by default.

    All characters that are unsafe for HTML display will be encoded using
    `HTML::Entities::encode_entities()`.

- urls

    This option is off by default.

    Replaces URLs with links.

    Pass additonal attribs that should be set on the `<<a href`>> element
    as a string in option `href_attribs`. For example:

        HTML::FromText->new({
            urls         => 1,
            href_attribs => 'rel="nofollow"'
        });

- email

    This option is off by default.

    Replaces email addresses with `mailto:` links.

- bold

    This option is off by default.

    Replaces text surrounded by asterisks (`*`) with the same text
    surrounded by `strong` tags.

- underline

    This option is off by default.

    Replaces text surrownded by underscores (`_`) with the same text
    surrounded by `span` tags with an underline style.

#### Output Modes

The following are three output modes and the options associated with
them. They are listed in order of precidence. If none of these modes are
supplied, the basic decorators are applied to the text in whole.

- **pre**

    This option is off by default.

    Wraps the entire text in `pre` tags.

- **lines**

    This option is off by default.

    Preserves line breaks by inserting `br` tags at the end of each line.

    This mode has further options.

    - spaces

        This option is off by default.

        All spaces are HTML encoded.

- **paras**

    This option is off by default.

    Preserves paragraphs by wrapping them in `p` tags.

    This mode has further options.

    - bullets

        This option is off by default.

        Convert bulleted lists into unordered lists (`ul`). Bullets can be
        either an asterisk (`*`) or a hyphen (`-`). Lists can be nested.

    - numbers

        This option is off by default.

        Convert numbered lists into ordered lists (`ol`). Numbered lists are
        identified by numerals. Lists may be nested.

    - headings

        This option is off by default.

        Convert paragraphs identified as headings into HTML headings at
        the appropriate level. The heading `1. Top` would be heading
        level one (`h1`). The heading `2.5.1. Blah` would be heading
        level three (`h3`).

    - title

        This option is off by default.

        Convert the first paragraph to a heading level one (`h1`).

    - tables

        This option is off by default.

        Convert paragraphs identified as tables to HTML tables. Tables are two
        or more rows and two or more columns. Columns should be separated by two
        or more spaces.

    The following options apply specifically to indented paragraphs. They
    are listed in order of precidence.

    - blockparas

        This option is off by default.

        Convert indented paragraphs to block quotes using the `blockquote` tag.

    - blockquotes

        Convert indented paragraphs as `blockparas` would, but also preserving
        line breaks.

    - blockcode

        Convert indented paragraphs as `blockquotes` would, but also preserving
        spaces using `pre` tags.

## parse

    my $html = $t2h->parse( $text );

Parses text supplied as a single scalar string and returns the HTML as a
single scalar string.  All the tabs in your text will be expanded using
`Text::Tabs::expand()`.

# FUNCTIONS

## text2html

    my $html = text2html(
                         $text,
                         urls  => 1,
                         email => 1,
                        );

Functional interface that just wraps the OO interface. This function is
exported by default. If you don't want it you can `require` the module
or `use` it with an empty list.

    require HTML::FromText;
    # or ...
    use HTML::FromText ();

## Subclassing

**Note:** At the time of this release, the internals of `HTML::FromText`
are in a state of development and cannot be expected to stay the same
from release to release. I expect that release version **3.00** will be
analogous to a `1.00` release of other software. This is because the
current maintainer has rewritten this distribution from the ground up
for the `2.x` series.  You have been warned.

The following methods may be used for subclassing `HTML::FromText`
to create your own text to HTML conversions. Each of these methods
is passed just one argument, the object (`$self`), unless
otherwise stated.

The structure of `$self` is as follows for this release.

    {
     options => {
                 option_name => $value,
                 ...
                },
     text    => $text, # as passed to parse(), with tabs expanded
     html    => $html, # the HTML that will be returned from parse()
    }

### pre

Used when `pre` mode is specified.

Should set `$self->{html}`.

Return value is ignored.

### lines

Used when `lines` mode is specified.

Implements the `spaces` option internally when the option is set to a
true value.

Should set `$self->{html}`.

Return value is ignored.

### paras

Used when the `paras` mode is specified.

Splits `$self->{text}` into paragraphs internally and sets up
`$self->{paras}` as follows.

    paras => {
              0 => {
                    text => $text, # paragraph text
                    html => $html, # paragraph html
                   },
              ... # and so on for all paragraphs
             },

Implements the `title` option internally when the option is turned on.

Converts any normal paragraphs to HTML paragraphs (surrounded by `p`
tags) internally.

Should set `$self->{html}`.

Return value is ignored.

### headings

Used to format headings when the `headings` option is turned on.

Return value is ignored.

### bullets

Format bulleted lists when the `bullets` option is turned on.

Return value is ignored.

### numbers

Format numbered lists when the `numbers` option is turned on.

Return value is ignored.

### tables

Format tables when the `tables` option is turned on.

Return value is ignored.

### blockparas

Used when the `blockparas` option is turned on.

Return value is ignored.

### blockquotes

Used when the `blockquotes` option is turned on.

Return value is ignored.

### blockcode

Used when the `blockcode` option is turned on.

Return value is ignored.

### urls

Turn urls into links when `urls` option is turned on.

Should operate on ` $self-`{html} >.

Return value is ignored.

### email

Turn email addresses into `mailto:` links when `email` option is
turned on.

Should operate on `$self->{html}`.

Return value is ignored.

### underline

Underline things between \_underscores\_ when `underline` option is
turned on.

Should operate on `$self->{html}`.

Return value is ignored.

### bold

Bold things between \*asterisks\* when `bold` option is turned on.

Should operate on `$self->{html}`.

Return value is ignored.

### metachars

Encode meta characters when `metachars` option is turned on.

Should operate on `$self->{html}`.

Return value is ignored.

## Output

The output from `HTML::FromText` has been updated to pass XHTML 1.1
validation. Every HTML tag that should have a CSS class name does. They
are prefixed with `hft-` and correspond to the names of the options to
`new()` (or `text2html()`). For example `hft-lines`, `hft-paras`,
and `hft-urls`.

One important note is the output for `underline`. Because the &lt;u> tag
is deprecated in this specification a `span` is used with a style
attribute of `text-decoration: underline`. The class is `hft-
underline`. If you want to override the `text-decoration` style in the
CSS class you'll need to do so like this.

    text-decoration: none !important;

# SEE ALSO

[text2html(1)](http://man.he.net/man1/text2html).

# THANKS

- Thanks to [Ctrl O](http://www.ctrlo.com/) for funding the
development of the "additional attribs to href" feature .

# AUTHORS

- Thomas Klausner <domm@plix.at>
- Ricardo SIGNES <rjbs@cpan.org>
- Casey West <casey@geeknest.com>
- Gareth Rees <garethr@cre.canon.co.uk>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2003 - 2022 by Casey West.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
