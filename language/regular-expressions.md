# Regular Expressions in JavaScript

* A regular expression is a way to describe a pattern in a string
* [Regex Tester](http://regexpal.com.s3-website-us-east-1.amazonaws.com/?_ga=2.249565263.813004562.1495485737-848749570.1493938714) - an online tool for testing regular expressions
* Regular expressions use symbols to define criteria to search text
* E.g `.` (dot) character is a symbol that will match any single character when used to search text

## Matching specific characters
* Regular expressions use a parser which compares each character in a string with each character in the regular expression pattern
* The `?` is used to specify an optional character
* It matches the character that appears directly before it zero times or one time
* E.g `toyboats?` matches with both `toyboat` and `toyboats`
* In the example above, the `s` is optional because directly after it is the `?` character
* Regular expressions are case-sensitive by default, but this can be changed using square brackets and inserting both upper and lowercase character
* The square brackets with upper and lowercase inside constitutes a **character set**
* E.g `[Tt]oy` will search for upper and lowercase `toy`
* You can add as many characters to a character set as you want and the parser will return any string that matches at least one of the characters in the character set
* Note that the character set will only match with a character in the same position of the string that the character set occupies in the regex
* A more complex example:

```
// Regex pattern
[lf]ad[yi][bn]u?g ?r?u?g?!?

ladybug
fading rug!
```

* (note: a helpful method of writing regex patterns is to line up all the string cases you want your pattern to match and write the regex column by column, as in the above example)

## Matching a range of characters
* You can match a range of characters (i.e a-z, 0-10)
* To tell the parser to look for all characters in a range, specify the range in a character set
* E.g `[A-Z]` or `[0-9]`

## Wildcards
* Wildcards are characters that match more than one character in a string
* Wildcards are patterns that can work as shorthand for longer patterns

| Characters | Wildcard |
| --------- | -------- |
| [0-9] | \d |
| [A-Za-z0-9_] (alphanumberic plus underscore) | \w |
| [\t\r\n\f] (whitespace characters) | \s |
| Any character | . |

* Wildcards can be included inside character sets

## Repeating characters
* To match repeating characters use the `*` and `+` characters
* `*` is used to match zero or more characters, `+` is used to match one or more characters
* E.g if you want to match any word that begins with 'toy' you can use the asterisk character:

```
toy\w*

toy
toyboat
toycar
```

* The regex at the top of the example will match any string that starts with toy, even just the word 'toy'
* This is because the asterisk used after the `\w` character tells the regex parser to keep on matching any other alphanumeric characters in the string
* To exclude 'toy' from the match and only match strings that contain 'toy' but also contain more characters, the `+` character can be used:

```
toy\w+

toy
toyboat
toycar
```

* This example will not match 'toy' but will match the other two words
* `+` character requires that at least one character follow
* An exact number of repetitions can be specified using `{numberOfReps}` syntax
* A limit for the number of reptitions can also be specified using `{numberOfReps, limit}`
* A minimum number of repetitions can be specified using `{numberOfReps,}`
* Simply adding a comma means that the regex will match with **atleast** as many repetitions specified, or more; but not less

```
{3}                                     three reps
{3,}                                 three or more
{3,5}                       between three and five
```

## Excluding characters
* Excluding characters is useful if you are searching for text between delimiters
* Comma-separated data for instance `1997,Ford,E350`
* If you want to get the data but leave out the commas you need to exclude
* To exclude any number of characters, use a a character set that begins with a `^` caret and then specify the characters to be excluded
* E.g `[^@]` will match any character except `@`
* E.g `[^@.]` will match any character except `@` and `.`
* (note: the `.` character stands for any character outside a character set, but inside a character set it stands for a literal dot)
* Specific kinds of characters can be excluded by capitalizing a wildcard, negating it
* E.g:

```
\d digit                    \D not digit
\w alphanumeric             \W not alphanumeric
\s whitespace               \S not whitespace
```

## Alternation
* The alternation character tells the regex to match either one pattern or another
* Use `regex1|regex2` syntax
* E.g

```
toy|sail

toy
sail
steam
tug
```

* In the above example, `toy` and `sail` will match but not the others
* `|` characters can be chained together with as many regexes as you want

### Groups
* Patterns can be grouped together in parentheses so they can be worked with as a unit
* This can be useful when using alternations to avoid repetition
* E.g

```
toy boat|toy bear|toy ball
```

vs

```
toy (boat|bear|ball)
```

* A more complex example:

```
img_\w{2}(_|\.)(\w{3}|\d{2})(\.\w{3})?

img_sm_01.jpg
img_01.jpg
img_sm_02.jpg
img_02.jpg
img_sm_03.jpg
img_03.jpg
img_sm_04.png
img_04.png

// This won't match
img.gif
```

## Beginning and ending of strings
* It is possible for the beginning or ending of a string to not match a regex pattern, but some other part in the string to match, and this will cause a partial match  
* Often you do not want partial matches; you only want full matches
* E.g `(www\.)?google\.(come|net)` will partially match `wwwgoogle.com`, but you would not want it to match because there is no `.` between after `www`
* So you would use `^(www\.)?google\.(come|net)` and this would tell the parser to begin matching only at the beginning of the string and not find partial matches in the middle
* The `^` and `$` characters tell the regex parser to start matching from the beginning, or finish at the end, respectively
* On the other hand, the string `www.google.commmmm` would match the pattern above, even though you probably don't want it to. You can tell the parser to stop looking for matches at the end using `^(www\.)?google\.(come|net)$`

```
^                   beginning of a string
$                   end of a string
```

* `^` tells the parser to reject strings that don't match from the start, and `$` tells the parser to reject strings that don't match the end
* These characters refer to position only, not to particular characters
* These characters are used to exclude strings that contain a partial match of your pattern, but taken as a whole are not valid


## Form validation
* Regular expressions are very useful for validating user input in websites and applications
* Validation is the process of guarding against invalid or bad data
* Two JavaScript methods are useful in validation: `test()` and `replace()`
* `test()` is used to test whether a string matches a pattern
* `replace()` is used to replace input with input that matches a certain pattern

### Implementing regex in JavaScript
* You must use the following syntax: `/regexString/`
* E.g a regex literal:
```js
const regexObject = /^word$/;
 ```

* A regex can also be created by creating a new instance of the regex object:
```js
const regexObject = new RegExp("/^word$/");
```

* JavaScript contains regular expressions in objects that can be used in a number of ways
* Using the regex constructor is a good way to create regular expressions dynamically i.e user input for a website search function
* Literal syntax is appropriate for non-dynamic regexes when you already know the pattern you need

### `test()` and `replace()`
* Use the syntax `regex.test(testString)`
* Returns `true` on pass, `false` on fail
* To return a new string that matches the regex pattern use `string.replace(regex, replacementString)`
* E.g

```js
const regex = /^corn/;
const testString = 'corndog';

testString.replace(regex, 'hot');
"hotdog"
```
* The string `corndog` is checked against the regular expression, and the part of it that matches is replaced with the string `hot`

### Flags
* Flags modify the way a regular expression behaves
* Overrides a default behaviour: similar to command line options
* `i` - Case-insensitive (ignore case)
* `g` - Global (find all matches contained in a string rather than stopping at the first match)
* `m` - Multiline (treat linebreaks like new lines rather than treating them as part of the string)
* Syntax: `/regex/flag`
* Flags can be combined in any order

## Reformating input
* Parentheses are not only for specifying groups but also can be used to capture the parts of the strings they match
* This allows you to replace patterns in a string
* Unlike standard indexing of iterables in JavaScript, indexes for captured strings in regular expressions start from 1 rather than 0
* To write a regular expression that captures substrings from its matches, surround the part of the pattern you wish to capture in parentheses `/(\w)(\w)(\w)/` will capture three alphanumeric characters
* The characters or substrings are then accessible by their index preceded by a `$` character
* E.g:

```
let string = 'abc';

string.replace(/(\w)(\w)(\w)/, '$3 $2 $1');
"c b a"
```

* (note: to use a literal `$` character in a replacement string you need to use `$$` which will render as a single `$`)

* More complex example:


```js
const formatTelephone = text => {
    const regex = /^\D*(\d{3})\D*(\d{3})\D*(\d{4})\D*$/;
    return text.replace(regex, `($1) $2-$3`);
}

const reformatted = formatTelephone("5555555555");
console.log(reformatted);
"(555) 555-5555"
```

* (note: text editors often allow you to search your codebase using regular expressions. This is potentially very useful!)