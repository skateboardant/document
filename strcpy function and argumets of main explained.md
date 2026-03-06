solve question listed in [coding in c until my program is unsafe](https://www.youtube.com/watch?v=oTEiQx88B2U)

## 1. strcpy() function

### What `strcpy()` actually expects

- The prototype is:

``` c
char *strcpy(char *dest, const char *src);
```

- `dest` → pointer to writable memory
- `src` → pointer to a string (read-only is fine) 

So the function needs **two `char *` pointers**.



## 2. arguments of main() function

In `main`:

``` c
int main(int argc, char *argv[])
```

`argv` is essentially:

``` c
char **argv
```

Meaning: argv → pointer to array of char pointers

Example layout:
											    argv
 		 									     ↓

|argv[0]|argv[1]|argv[2]|
|:--:|:--:|:--:|
|↓|↓|↓|
|"./prog"|"abc"|"xyz"|

So: argv[1]  → char *, which is a **C string pointer**.



## 3. The real reason the question fails



### A. using `argv[1]` when it doesn't exist

If the program runs without arguments:

``` cmd
./program
```

then: argc = 1, argv[1] = invalid



### B. destination too small

``` c
char buf[4];
strcpy(buf, argv[1]);   // overflow possible
```

`strcpy()` does **no bounds checking**, which can overwrite memory. 



### C.  destination is not allocated (Hiden mistake)

```c
char *buf;
strcpy(buf, argv[1]);   // WRONG
```

`buf` points nowhere → segmentation fault.



## 4. Key concept (important for C understanding)

**Array name ≠ pointer**

But in most expressions:

```
char arr[10];
```

`arr` → **decays to `char `** (pointer to the first element)



correcxt example for reference: 

Key idea: **strings must be compared using `strcmp()`**, not `==`.

```c
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[])
{
    if (argc > 1)
    {
        if (strcmp(argv[1], "CAT") == 0)
        {
            printf("Meow\n");
        }
        else if (strcmp(argv[1], "DOG") == 0)
        {
            printf("Woof\n");
        }
        else
        {
            printf("Unknown animal\n");
        }
    }
    else
    {
        printf("Please provide an argument.\n");
    }

    return 0;
}
```

### Why we use `strcmp()`

This is **wrong** in C:

```c
if (argv[1] == "CAT")
```

because it compares **pointer addresses**, not the *characters*.

Correct comparison:

```c
strcmp(argv[1], "CAT") == 0
```