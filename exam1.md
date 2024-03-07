# String Values

Strings are used to store text data (i.e., they contain text). String values are (must be) surrounded by (enclosed by) single quotation marks `'` or double quotation marks `"`. The enclosing quotation marks must match; a string value can not start with a single quotation mark and end with a double quotation mark. The text of a string value may not span multiple lines (i.e., the enclosing quotation marks must be on the same line).

The following are string values:
* `''`
* `""`
* `"hello"`

The following are not string values:
* `hello`
* `string`
* `'"`
* `"'`
* `"`
* <pre>'<br>'</pre> 

If single quotation marks are used as the enclosing quotation marks then double quotation marks can be freely used as part of the text. If single quotation marks are used as the enclosing quotation marks then single quotation marks that are intended to part of the text must be escaped (i.e., proceeded by a backslash). Escaping can also be used to include other text in a string that would be difficult to represent literally (e.g., newlines). All of this is also true for the inverse (e.g., double vs single, instead of single vs double).

The following are string values:
* `"''"`
* `'""'`
* `"'String'"`
* `'"a"'`
* `"\""`
* `'\''`
* `"\n"`

The content of a string is always text. 

That means that the following **are string values**:

* `"true"`
* `'false'`
* `"0"`
* `"Infinity"`

Operations that consume or produce strings consume or produce string values. 

* `"atrue".substring(1)` evaluates to `"true"` (a string) not `true` (a boolean)
* `["a", "b", "c"][0]` evaluates to `"a"`, not `a`

When logged via `console.log`, the content of a string is what is printed to the console (i.e., the enclosing quotation marks are not printed).
`console.log("hello")` prints `hello` not `"hello"`.

**If you forget quotes around a string value, then it is not a string value. It is just text.**

# Terminology

You need to learn ALL of the follow words.

* Week 1 Vocabulary
  * Type
  * Value
  * Variable
  * Declaration
  * Definition
  * Initialization
  * Assignment
  * Conditional
  * Function
    * "Call" - This verb is only appropriate for when you use a function (with parentheses)
* Week 2 Vocabulary
  * Array
  * Loop
* Week 3 Vocabulary
  * Object
    * Instance
  * Class
  * Member Variable
    * Field
    * Property
    * Attribute
  * Constructor
* Week 4 Vocabulary
  * Method
