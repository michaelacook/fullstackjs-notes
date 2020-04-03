# Pseudo-code Reference

Pseudocode does not have a strict syntax but there are common ways of explaining different coding concepts like input, variables, conditional statements, loops and functions.


## Input
Input could be from a keyboard, database, mouse, and so on. You want to indicate that data is coming into the program from some external source.

```
get name from user
```
```
get user_record from employee_database
```

## Variables
Simple variable
Create a variable named points and give it the value of two:

```
set points to 2
```

**Collection of values like an array or list**
```
set names to 'Dave', 'Reggie', 'Hope', 'Colleen'
```

## Conditional Statements

**Single condition**
```
If the points variable is more than 100, print "High Score!"

if points is greater than 100 
    print “High Score”
endif
```
Indent code inside of conditional statements to make clear the steps the program follows if the condition is true.

**Multiple conditions**
```
if points is less than 0 
     print “You lose!”
elseif points is greater than 100
    print "High Score"
else
    print "You won, but didn't get the high score"
endif
```

## Loops
Looping though a collection of items like an array or list

```
for each name in names 
     print name
endfor
```

Looping a set number of times:

```
for every number from 1 to 100
    print number
endfor
```

Loop while a condition is true:

```
while user_input is not 'Exit'
   ...do something here
endwhile
```

## Functions
**Create a function**

Create a function that prints out a message to the console:

```
function print_message 
    pass in message
    print message
endfunction
```

**Call a function**

```
call print_message with "Howdy!"
```

A function that returns a value:

```
function calculate_tax
    pass in amount and tax_rate
    return amount * tax_rate
endfunction

set tax to call calculate_tax with 100 and .08
print 'Your tax is'
print tax
```

# Credits
This cheat sheet was taken from https://teamtreehouse.com/library/pseudocode-reference and translated into markdown. It is not an original work.