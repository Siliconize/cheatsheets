# Bash Cheatsheet Version 1

<!--
ToDo:
Logical Combinations in if statments
-->

This is a cheatsheet I've wrote for my personal use, but I think
it could be helpful for other people so I'm putting it online.

> Warning: This cheatsheet isn't complete yet! 

I'm trying to follow this styleguide by bahamas10: https://github.com/bahamas10/bash-style-guide , it's really good, check it out!

You can learn bash on this website: https://www.learnshell.org/ , my notes
are based on it.

## The sha-bang:
We but a sha-bang #! with the location of bash on top of 
the script so it can run.
Often times people use `#!/bin/bash`, but bash
isn't always located at /bin/bash so to make truly portable
scirpts we should use `#!/usr/bin/env bash` which will give us
the environments bash.

## Variables

```bash
#!/usr/bin/env bash
var_Name=5
var_2= ABC
var3=' Hello               world!' 
```

Backslash "\" is used to escape special characters.
```bash
#!/usr/bin/env bash
echo "You can't just write a \$"
```

Encapsulating a variable name with "" will preserve any white space values:

echo $var3 "with spaces: $var3"

## Substitution
Variables can be assigned with the value of a command output using
back ticks `` or using $(), I personally think the second one is less ambigious.
```bash
today=`date`
```
`echo today`

`today=$(date)`

`echo today`

#### date command, you can use date to turn one date string into another one

`somedate="10 Jan, 2000"`

`weekdayName=$(date -d "$somedate" +%A)`


## Strings
### String Operations

#### String Length

```bash
someString="bash is great!"
echo ${#someString}
```
#### Index

```bash
exampleString="AAA BBB CCC"
toMatch="A"

expr index "$exampleString" "$toMatch"
# matches the first a in the string
# output:1

toMatch="B"

expr index "$exampleString" "$toMatch"
# matches the first B in the string
# output:5

toMatch="b"

expr index "$exampleString" "$toMatch"
# there is no lower case b in our example String so the expr command
# outputs 0
# output:0
```

#### Substring Extraction
You can extract a substring of some length $l from some String $s, from the position $p onward by doing this:

```bash
${s:$p:$l}
```

As an example:
```bash
myString="I don't know what to put here"
echo ${myString:1:12}
```
output: don't know

#### Substring Replacement

Replace first occurence of substring:
```bash
myString="I don't know what to put here"
echo ${myString[@]/"don't"/totally}
```

Replace all occurences of substring:
```bash
myString="I don't know what to put here"
echo ${myString[@]//o/AAAAAA}
```

delete substring by replacing with empty 

```bash
myString="I don't know what to put here"
echo ${myString[@]//"don't"/ }
```

this works too

```bash
myString="I don't know what to put here"
echo ${myString[@]// "don't"}
```

Replace occurence of substring if at the beginning of string

```bash
myString="AAAAAAAAAAAAAA"
echo ${myString[@]/#A/B}
```

Replace occurence of substring if at the end of string


```bash
myString="AAAAAAAAAAAAAA"
echo ${myString[@]/%A/B}
```

as you can see it's just matches the first occurence/ last occurence of the string 

## Arrays
An array holds several values under one name, in bash an array is space-delimited and enclosed
in ()

### Initializing Array:

Example 1:
```bash
somearray=(val1 val2 "Some Value 3" val4)
```

Example 2:
```bash
anotherarray[0]="value 1"
anotherarray[1]="value 2"

```

### Accesing Values:
all values `${somearray[@]}`

specific value `${somearray[2]}` (third element)

number of elements: `${#my_array[@]}` eg. 4d

### Assigning:
somearray[4]="something to put here"





## Arguments
Arguments can be passed to the script by writing them as a space-delimited list
following the script name like

`yourscript.sh thisIsAnArgument thisIsAnotherArgument`

|Variable| Argument it's referencing           |
|--------|-------------------------------------|
|   $0   | File Name of the current script     |
|   $1   | First Argument                      |
|   $2   | Second Argument                     |
|   ...  | ...                                 |
|   $n   | n-th Argument                       |
|   $#   | number of arguments passed to scipt |

(n here refeferences some number, if you aren't familiar with the mathematic notation)

Example:
```bash
#!/usr/bin/env bash
echo "File Name is $0, your first argument is $1"
```



## Arithmetics
Using $((...)) you can do simple arithmetics

|Bash Notation   | Operation                            |
|----------------|--------------------------------------|
|  `$((a + b))`  | a plus b                             |
|  `$((a - b))`  | a minus b                            |
|  `$((a * b))`  | a times b                            |
|  `$((a / b))`  | a divided by b                       |
|  `$((a % b))`  | a modulo b (division with remainder) |
|  `$((a ** b))` | a to the power of b (exponentiation) |


---

Example:
```bash
#!/usr/bin/env bash

a = 42
b = 24

if ((a > b)); then
    ...
fi

```

## Functions
a shell function can take in arguments as inputs, like a shell script as discussed in
[## Arguments](#arguments) and return back an exit status, which is a 
value between 0 and 255.
If you try to return a value higher than 255 it will overflow.

Like 256 will give you 0.


Example:
```bash
#!/usr/bin/env bash
myFunction(){
    if (($1 >= 2)); then
        local returnValue=0
    else
        local returnValue=1
    fi 
    return $returnValue
}

myFunction 4

capturedReturnValue=$?

echo "Return value: $capturedReturnValue"

```


You cannot return a string or word from a bash function.
But you can just put an echo in a function as output.


```bash
#!/usr/bin/env bash
myFunction(){
    local str="$@"
    local output=str
    echo $output
}
```
now you can either just call it
```bash
myFunction "Some string"
```
or you can save the output in a variable

```bash
funcOut=$(myFunction "Some string")
```

## Control structures

### Block Statments

#### if/ else

```bash
if true; then
    ...
fi
```

Example:
```bash
#!/usr/bin/env bash
age=42
name="Max"
if [ "$name"="Max" ] && (($age>21)) ; then   # string and numerical check in the same line, more readable then [ "$age" -gt 21 ]
    echo "Max is older than 21"
elif ["$name"="Joe"] && (($age>21)); then
    echo "Joe is older than 21"
else
    echo "No Match"
fi

```
##### Numeric Comparisons

|  1. Notation  | 2. Notation      | Meaning                  |
|---------------|------------------|--------------------------|
| `((a < b))`   |  `[[ a -lt b]]`  | less then                |
| `((a > b))`   |  `[[ a -gt b]]`  | greater then             |
| `((a <= b))`  |  `[[ a -le b]]`  | less then or equal to    |
| `((a >= b))`  |  `[[ a -ge b]]`  | greater then or equal to |
| `((a = b))`   |  `[[ a -eq b]]`  | equal to                 |
| `((a != b))`  |  `[[ a -ne b]]`  | not equal to             |


##### String Comparisons

| Notation         | Meaning      |
|------------------|--------------|
| `["$a" = "$b"]`  | equal to     | 
| `["$a" == "$b"]` | equal to     |
| `["$a" != "$b"]` | not equal to |
| `[-z "$a"]`      | is empty     |

the whitespace around the = is required 

use quotes when working with string variables to avoid shell expansions of special characters


##### Examples

if [[ ]]





##### Numeric comparisons
```bash
a=42
b=24
if (($b < $a)); then 
    echo "$a is bigger"
else
    echo "$b is bigger"
fi
```

#### Case Statment

##### Syntax

```bash
case "$EXPRESSION" in

    "$PATTERN_1")
        STATMENTS
    ;;

     "$PATTERN_2" | "$EXTRAPATTERN")  # you can use multiple patterns using | to seperate them
        STATMENTS
    ;;  # must be terminated with two ;;

    ...

     "$PATTERN_N")
        STATMENTS
    ;;
esac

```

Example:
```bash
echo "[y/n/m] - yes, no, maybe"

read something

case "$something" in 
    y)
        echo "Haha yeah!"
    ;;
    n) 
        echo "No? :("
    ;;
    m)
        echo "Ok :/"
    ;;
esac

```
## Loops




### for loops
Basic Syntax
```bash
for argument in [list]; do
    commands
done
```
Example:
```bash
for f in {1..10}; do
    echo $f
done
```

### while loops
Basic Syntax
```bash
while [condition]; do
    commands
done

```

### until loop
Basic Syntax
```bash
until [condition]; do
    commands
done
```


### break and continue

#### break
Skip entire rest of loop
#### continue
Skip rest of particular loop iteration