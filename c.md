# The C Programming Language


## Strings 

Strings are just character arrays. 
declare with:

```c

char * yourString="Hello World"; 

# or

char yourString[]="Hello World";

```

Getting the Lenght of a String.

There are two ways. 

With this one:
```
sizeof(yourString)-1;
```
you grab the lenght of the Array at compile time, this is okay if 
the String does not change during runtime. And you have to subtract 1 because every
String has a null operator `\0` at it's end.

Or you just do it like that:
```c
#include <string.h>

strlen(yourString);
```
This will also reflect changes at runtime to the String. 
But you have to include the extra header.


