# EcmaScript Proposal: Regular Expression Verbose Flag

## Status

Champion(s): Currently none

Stage: -1

## Motivation

In most languages, a programmer can use both whitespace to visually group related logic together, and comments to label sections or explain something that's not otherwise obvious. Regular expression syntax does not allow the use of whitespace or comments, making it much more difficult to write understandable regular expressions. This proposal seeks to add a verbose flag (`x`) to regular expressions, which causes whitespace and comments to be ignored inside the regular expression.

## Description

When the `x` flag is used on a regular expression, all whitespace will be ignored (including spaces, tabs, carriage returns, and line feeds), as will as anything that comes after the comment delimiter (`#`)

```javascript
const regex = / ^ [0-9]{3} \+ [0-9]{3} $ # This is ignored /x
regex.test('123+456') // true
```

Comments aren't that useful within a regular expression literal. In order to take full advantage of the verbose flag, we can use a multiline regular expression.

```javascript
const regex = new RegExp(String.raw`
  ^
    [0-9]{3} # matches three numbers
    \+
    [0-9]{3} # matches three numbers
  $
`, 'x')
```

If spaces or the `#` character are needed, they can be escaped.

```javascript
/\#\ \#/x.test('# #') // true
```

## Better multiline support (add-on feature)

While not strictly required, the verbose flag's usability would be greatly improved if better support was provided for making multiline regular expressions. This can easily be achieved with a tagged template literal as follows:

```javascript
const regex = RegExp.from('x')`
  ^
    [0-9]{3} # matches three numbers
    \+
    [0-9]{3} # matches three numbers
  $
`
```

The `RegExp.from()` method takes a string containing different flags and returns a template tag that can be used to construct a multiline regular expression.

There's been talk of allowing `RegExp.from()` to support variable interpolation with other regular expressions. This opens up a number of issues dealing with how to best incorporate the flags of the interpolated regular expressions with the new one, but it's a discussion that we can continue to have if there's interest in that kind of behavior.

## Comparison

The inspiration for a verbose flag is largely draw from Python. Here's an example of their implementation, pulled from their doc site:

```python
a = re.compile(r"""\d +  # the integral part
                   \.    # the decimal point
                   \d *  # some fractional digits""", re.X)
```

Coffeescript has supported multiline regular expressions with comments and whitespace support for a long time now. Here's an example pulled from their website:

```coffeescript
NUMBER     = ///
  ^ 0b[01]+    |              # binary
  ^ 0o[0-7]+   |              # octal
  ^ 0x[\da-f]+ |              # hex
  ^ \d*\.?\d+ (?:e[+-]?\d+)?  # decimal
///i
```

The main difference between this proposal and CoffeeScript's implementation is that CoffeeScript has a dedicated syntax for multiline regular expressions. The reason dedicated syntax is not being proposed is because Javascript's template tags are just as expressive as a dedicated multiline syntax, and the best choice for dedicated syntax (`///`) can't be used, as it already has a different meaning in Javascript (`///` is a comment).

Another example of prior art is the third-party Javascript library [xregexp](https://xregexp.com/). It supports the "x" flag too, which provides whitespace support and comments via the "#" character. Here's an example from their webpage:

```javascript
const date = XRegExp(`(?<year>  [0-9]{4} ) -?  # year
                      (?<month> [0-9]{2} ) -?  # month
                      (?<day>   [0-9]{2} )     # day`, 'x');
```
