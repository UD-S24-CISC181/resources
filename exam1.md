# References

For the three builtin basic types in Typescript (`number`, `string`, and `boolean`), a value is stored in the _variable_.  If you _assign_, _compare_, or _pass into a function_ any variables of these types, then you are assigning, comparing, or passing in the _value_ of that variable. For all other types, including _instances_ of a _class_ (i.e., an object) and _arrays_, you are assigning, comparing or passing in a _reference_ to that object or array!

What this means is that for everything except the three builtin basic types, you must consider that this **reference** is a reference to the same physical object in memory.  

```typescript
let p: Point = new Point(10,10);
let q: Point = p;
p.x = 100;
```

In this case, the value of `q.x` is `100`. Since `p` is a reference, setting `q = p` is just copying the reference to the same object.  We must explicitly create a method (like `clone`) if we want to actually make a distinct copy of the object.

```typescript
let p: Point = new Point(10,10);
let q: Point = p.clone();
p.x = 100;
```

In this case, the value of `q.x` is `10` since we did not copy the reference, but rather created a new instance and deep copied the original instance into the new instance, creating two distinct instances.  Variables `p` and `q` are still references, but now they are references to different physical objects in memory.

For arrays, the variable also stores a reference to the array.  We cannot automatically deep copy an array, but we can shallow copy the array using the spread operator (`...`).  While this creates a new instance of the array, if the array contains references as its elements, then it is necessary to deep copy each element to get a truly distinct array.

```typescript
let points: Point[] = [new Point(1,1), new Point(2,2)]
let points2: Point[] = points;

// Just alises, so this changes both
points2.push(new Point(3,3));
console.log(points.length); // 3
```

In this case, `points2` and `points` both reference the same array in memory, and the elements are each references to the same objects. Both a `push` onto one array or changing `points[0]` will change `points2`.

```typescript
let points: Point[] = [new Point(1,1), new Point(2,2)]
let points2: Point[] = [...points];

// Separate arrays, so the array length is not changed
points2.push(new Point(3, 3));
console.log(points.length); // 2

// But the copy was shallow, so modifying the contents of points2 affects points
points2[0].x = 100
console.log(points[0].x); // 100
```

In this case, `points2` and `points` now reference distinct arrays, but the elements of the arrays are still references to the same object. Changing `points[1]` changes `points2[1]`, but pushing onto `points` does not affect `points2`.

```typescript
let points: Point[] = [new Point(1,1), new Point(2,2)]
let points2: Point[] = [];
for (let point of points) {
    points2.push(point.clone);
}

// Deep copy, so now points2 is completely independent of points
points2.push(new Point(3, 3));
console.log(points.length); // 2
points2[0].x = 100
console.log(points[0].x); // 1
```

In this case, points2 and points reference distinct arrays, and the elements in each array are completely distinct.  They are still references, but now they refer to different physical objects in memory.

## Equality of Arrays

Typescript can test the equality of anything without causing an error. Its rules for numbers, strings, and booleans are the same as in Python. However, the rules for arrays and objects are slightly different - two arrays (and two objects) are only equal if they *have the same references* (as opposed to the same contents).

```typescript
let scores = [1, 2, 3];
let scores2 = [1, 2, 3];

console.log(scores === scores2); // false

let scoresAlias = scores;
console.log(scores === scoresAlias); // true
```

# Const

Variables can be declared using either the keyword `let` or the keyword `const`. Variables that are declared using `const` can not be reassigned. For example, the following is not possible:

```typescript
const x: string = "hello";
x = "world"; // ERROR: x can not be reassigned
```

However, `const` is not transitive. It only prevents the variable from being reassigned. If the variable is a reference to an array or object, const does not prevent modifications to the array or object. For example:

```typescript
const arr = ["a", "b", "c"];
arr = ["a", "b", "c"]; // ERROR: the variable arr can not be reassigned
arr.push("d"); // no error, arr is not reassigned
arr[0] = ""; // no error, arr is not reassigned
```

The same rules apply to objects:

```typescript
class Point {
    constructor(public x: number, public y: number) {}
}

const p1 = new Point(0, 0);
p1 = new Point(10, 10); // ERROR: the variable p1 can not be reassigned
p1.x = 10; // no error: p1 is not reassigned
```

# Parameters, References, Const

The rules for assigning to local variables are the same as the rules for passing arguments to parameters. However, folks sometimes get confused because it feels different.

Recall that a function call takes a sequence of values (**arguments**) and assigns them to variables defined in the header of the function (**parameters**). In the following code, the `action` function will have three variables (`alpha`, `beta`, and `gamma`) with the corresponding values `5`, `"Banana"`, and `true`.

```typescript
function action(alpha: number, beta: string, gamma: boolean): void {
    console.log(alpha, beta, gamma);
}

action(5, "Banana", true);
```

Modifications to variables holding primitive values (numbers, strings, and booleans) will not affect variables in the outer scope. This is true even if there is a variable with the same name in the outer scope, and even if you use the `const` keyword:

```typescript
function action(alpha: number, beta: string, gamma: boolean): void {
    alpha = 100;
    beta = "Apple"; // no error, this beta is not constant, unlike the other one
    gamma = false;
    console.log(alpha, beta, gamma); // 100 Apple false
}

let alpha = 5, gamma = true;
const beta = "Banana";
action(alpha, beta, gamma);
console.log(alpha, beta, gamma); // 5 Banana true
```

Non-primitive values (aka references, like arrays and objects) will be assigned to new *variables* but will be the same *reference*. So changes to the *variable* inside of the function will not have any impact on the outer variables, but modifications to the array or object inside *will* have an impact.

```typescript
class Student {
    constructor(public name: string) {}
}

function action(ada: Student, foods: string[], babbage: Student, scores: number[]) {
    // Modifying contents, affects outside
    ada.name = "Ms. Ada Bart";
    foods[0] = "orange";
    // Modifying variable, does not affect outside
    babbage = new Student("Mr. Babbage Bart");
    scores = [2, 9, 1];
    
    console.log(ada); // Student: { "name": "Ms. Ada Bart" }
    console.log(foods); // ["orange", "banana", "pear"]
    console.log(babbage); // Student: { "name": "Mr. Babbage Bart" }
    console.log(scores); // [2, 9, 1]
}

let ada = new Student("Ada Bart");
let foods = ["apple", "banana", "pear"];
let babbage = new Student("Babbage Bart");
const scores = [7, 3, 4];
action(ada, foods, babbage, scores);
console.log(ada); // Student { "name": "Ms. Ada Bart" }
console.log(foods); // ["orange", "banana", "pear"]
console.log(babbage); // Student: { "name": "Mr. Babbage Bart" }
console.log(scores); // [2, 9, 1]
```

You must recognize when a modification affects a variable or a mutable reference:

* Assignment statements mutate a reference when they involve attributes (`object.field`) or array indices (`array[0]`) on the left-hand side of the `=` sign.
    * Assignment statements modify a variable ("rebind" a variable) otherwise, and do not modify any other variables
    * Parameter assignment follows the exact same rules as regular assignment statements - since they do not involve attributes or array indices, they create new variables (which may have the same references).
* Most array methods will mutate the array (`push`, `pop`) although some do not (`slice`). You must always check documentation or store it in your brain.
* String methods will *never* mutate a string (`concat`, `slice`, `replace`) because strings are *immutable*
* The `new` keyword is NECESSARY for creating a new separate instance
* The spread syntax (`...`) can be used to create a copy of an array; if the array has mutable element types (e.g., array of objects or array of arrays), then the copy will be shallow.

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

# Slicing

Slicing rules are consistent between Python and Typescript. The only difference is that `slice` is a method in Typescript, instead of an operator syntax.

The first number represents the starting position of the subscript, and the second number indicates the ending position of the subscript. If you use negative numbers in subscripts, you can work from the back of the array or string. If you use `-1`, then you get the last character. You can combine positive and negative in a range. To go from the start or the end, simply leave the second argument missing. In the example shown here, the first line indexes the last character (`"r"`). The second line subscripts all the characters after the first up to but not including the last character (`"amste"`). The third line subscripts the last three characters at the end of the string (`"ter"`). The fourth line subscripts the first three characters at the start of the string (`"ham"`).

```typescript
word = "hamster"

console.log(word.slice(-1]);    // r
console.log(word.slice(1, -1)); // amste
console.log(word.slice(-3, ))   // ter
console.log(word.slice(, 3))    // hame
```

Figuring out subscripts can be quite tricky. If you try to stare and count the indexes by hand, you are almost certainly going to make a mistake. Instead, take out a piece of paper and draw boxes around each character of the string. Then, number the boxes. Put indexes directly BELOW the boxes, and subscripts directly BETWEEN the boxes. You can then quickly check what character a given index corresponds to, or what range of characters are sandwiched between two subscripts.

![image](https://github.com/UD-S24-CISC181/resources/assets/897227/693566aa-bb0e-4e61-9f4a-b66f043f3265)

# If/Else Control Flow

Two `if` statements are NOT the same as an `if` followed by an `else if`:

```typescript
let alpha = 5;
if (alpha > 4) {
    console.log("This will be printed");
} else if (alpha > 2) {
    console.log("This will NOT be printed");
}

// vs.

if (alpha > 4) {
    console.log("This will be printed");
}
if (alpha > 2) {
    console.log("This WILL be printed");
}
```

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
  * Parameter
  * Argument
* Week 2 Vocabulary
  * Array
  * Loop
  * Mutate
  * Immutable
  * Index
  * Slice
  * Subscript
* Week 3 Vocabulary
  * Object
    * Instance
  * Class
  * Member Variable
    * Field
    * Property
    * Attribute
  * Constructor
  * Access
  * Composition
* Week 4 Vocabulary
  * Method
* Week 5 Vocabulary
  * Inheritance
  * Extends
  * Is-A vs Has-A
