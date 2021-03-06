# Data serialization formats

Comparison of selected data serialization formats.
* JSON - [ECMA-404](http://www.ecma-international.org/publications/files/ECMA-ST/ECMA-404.pdf)
* EDN - [github.com/edn-format/edn](https://github.com/edn-format/edn)
* CLJ - [clojure.org/reader](http://clojure.org/reader)

## Whitespace

x | JSON | EDN | CLJ
-----|-----|-----|----
whitespace character | U+0009 (tab) U+000A (newline) U+000D (return) and U+0020 (space) | :warning: specs does not specify whitespace chars, explicitly mentions just `,` | :warning: specs does not specify whitespace chars, explicitly mentions just `,`
delimiters | whitespaces, all structural (`{` `}` `:` `,` `[` `]`) and literal (`true` `false` `null`) tokens | whitespaces and `[` `]` `{` `}` `(` `)` ( :warning: additional delimiters not mentioned in specs `"` `\` `;`) | `[` `]` `{` `}` `(` `)` `"` `\` `;` `@` `^` `` ` `` `~` (:warning: no explicit mention in specs)

## Null

x | JSON | EDN | CLJ
-----|-----|-----|----
null literal | `null` | `nil` | `nil`

## Booleans

x | JSON | EDN | CLJ
-----|-----|-----|----
true literal | `true` | `true` | `true`
false literal | `false` | `false` | `false`

## Characters

x | JSON | EDN | CLJ
-----|-----|-----|----
syntax | NONE :x: | `\X` | `\X`
special characters | *n/a* | `\newline` `\tab` `\return` `\space` | `\newline` `\tab` `\return` `\space` `\backspace` `\formfeed` 
unicode escapes | *n/a* | `\uNNNN` | `\uNNNN`
octal escapes | *n/a* | NO :x: | `\o0` to `\o377`

## Strings

x | JSON | EDN | CLJ
-----|-----|-----|----
syntax | wrapped in `"` | wrapped in `"` | wrapped in `"` 
may span multiple lines | NO :x: | YES | YES
characters which must be escaped | `"` `\` and control characters (U+0000 to U+001F) | `"` `\` | `"` `\`
escape characters | `\"` `\\` `\/` `\b` `\f` `\n` `\r` `\t` | `\"` `\\` `\n` `\r` `\t` | `\"` `\\` `\'` `\b` `\f` `\n` `\r` `\t` 
unicode escapes | `\uNNNN` | NO, not mentioned in specs :x: | `\uNNNN`
octal escapes | NO :x: | NO :x: | `\0` to `\377`

## Integers

x | JSON | EDN | CLJ
-----|-----|-----|----
arbitrary precision indicator | NO :x: | `N` suffix | `N` suffix
valid signs | `-` | `-` `+` |  `-` `+`
`-0` is valid | YES | YES | YES
decimal integer | YES | YES | YES
custom radix | NO :x: | NO :x: | `NNrNNNNN`
hexa integer | NO :x: | NO :x: | `0xNNNNN`, but undocumented :warning:
octal integer | NO :x: | NO :x: | `0NNN`, but undocumented :warning:

## Non-integral numbers

x | JSON | EDN | CLJ
-----|-----|-----|----
ratios | NO :x: | NO :x: | `NN/NN`
decimals | NO :x: | `M` suffix | `M` suffix 
floats | YES | YES | YES
valid signs | `-` | `-` `+` |  `-` `+`

## Identifiers

x | JSON | EDN | CLJ
-----|-----|-----|----
symbols | NO :x: | YES | YES
symbol start character | *n/a* | alphabetic, `+` `-` `.` followed by non-numeric symbol character, `*` `!` `_` `?` `$` `%` `&` `=` `<` `>` (`/` is special, see below) | alphabetic, `+` `-` `*` `!` `_` `?` (`/` and `.` are special, see below), ( :warning: specs does not mention `$` `=` `<` `>` `%` `&`)
symbol character | *n/a* | alphanumeric, `+` `-` `.` `*` `!` `_` `?` `$` `%` `&` `=` `<` `>` `:` `#` | alphanumeric,  `+` `-` `*` `!` `_` `?` (`.` `/` and `:` are special, see below), ( :warning: specs does not mention `$` `=` `<` `>` `%` `&` `#` `'`)
special symbol character  | *n/a* | `/`, used alone or in following combinations `foo/bar` (:warning: In my opinion specs does not allow `foo//` combination, [see this ticket](https://github.com/edn-format/edn/issues/51)). First character after slash must follow rule for symbol start character | `/`, used alone or in following combinations `foo/bar` `foo//` (:warning: latter combination is undocumented). `.`, used as prefix or suffix is reserved to Clojure, used inside symbol divides namespace or package names. `:` can be used inside symbol, used as suffix is reserved to Clojure (currently it produces an error). (:warning: undocumented restrictions: symbol can not contain `::`, namespace part can not end with `:`,  first character after slash must follow rule for symbol start character)
keyword | NO :x: | prefixed with `:` | prefixed with `:`
keyword character | *n/a* | alphanumeric, `+` `-` `.` `*` `!` `_` `?` `$` `%` `&` `=` `<` `>` `:` `#` | alphanumeric,  `+` `-` `*` `!` `_` `?` (`.` `/` and `:` are special, see below), ( :warning: specs does not mention `$` `=` `<` `>` `%` `&` `#` `'`)
keyword invalid second character | *n/a* | `:` | *none special*
special keyword character  | *n/a* | `/`, used in following combinations `:foo//` `:foo/bar`. First character after slash must follow rule for symbol start character | `/`, used in following combinations `:foo/bar` `:foo//` (:warning: latter combination is undocumented). `:` used as second character resolves keyword in the current namespace, used as suffix is reserved to Clojure (currently it produces an error). (:warning: undocumented restrictions: keyword can not contain `::` other than as a prefix, namespace part can not end with `:`,  first character after slash must follow rule for symbol start character) Specs also say that keywords cannot contain '.' ( :warning: restriction meant probably only for the name part of the keyword) or name classes.

## Collections

x | JSON | EDN | CLJ
-----|-----|-----|----
list | NO :x: | `(a b c)`| `(a b c)`
vector | `[a, b, c]` | `[a b c]`| `[a b c]`
set | NO :x: | `#{a b c}` items are unique | `#{a b c}` items are unique 
map | `{string1 : val1, string2 : val2]` | `{key1 val1 key2 val2}` keys are unique | `{key1 val1 key2 val2}` keys are unique

## Special
x | JSON | EDN | CLJ
-----|-----|-----|----
comments | NO :x: | `;` till the end of the line | `;` till the end of the line
discard | NO :x: | `#_` discards next read object | `#_` discards next read object
tagged literal | NO :x: | `#` followed immediately by a symbol starting with an alphabetic character | `#` followed immediately by a symbol starting with an alphabetic character
builtin tags | *n/a* | `#inst` `#uuid` |  `#inst` `#uuid`, deftypes, defrecords and java classes constructors
regular expressions | NO :x: | NO :x: | `#"pattern"`
macros | NO :x: | NO :x: | `'` (quote), `@` (deref), `^` (metadata), `#'` (var quote), `#(% %n %&)` (anonymous function), `` ` `` (syntax quote), `~` (unquote), `~@` (unquote splicing). (:warning: undocumented macros `#=` `#!` `#<` and deprecated `#^`)

