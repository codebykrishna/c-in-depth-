<h3>Explain setjmp() and longjmp() Functions in Standard C Library with Examples</h3><br>

<h3> Question: What are setjmp() and longjmp() Functions in Standard C Library. Explain with Examples When to Use them </h3>

Answer: ‘setjmp()’ and ‘longjmp()’ functions provide a powerful mechanism similar to ‘goto’ but not restricted to just current function. This mechanism is useful in situations where there’s a deeply nested function calls and when error gets detected, in some lower-level function, then control immediately transfers to top-level function without having to return and check for error code by intermediate functions. These functions work in pairs as follows:

1. setjmp(jmp_buf resume_here) must be called first. This initializes ‘resume_here’ with program state information viz. value of pointer to the top of the stack, value of program counter and some other info. It returns ZERO when returns first time.

2. longjmp(jmp_buf resume_here, int i) is then called. This says go back to place where to resume from defined by ‘resume_here’. This makes it look like you’re returning from original ‘setjmp()’ but return value of integer ‘i’ to let code tell when you actually got back here via ‘longjmp()’.

3. After longjmp()’ed, contents of ‘jmp_buf’ variable ‘resume_here’ destroys.

Let’s take a C program to learn how to use them.

/* setjmp_longjmp.c -- program handles error through 'setjmp()' */
/* and longjmp() */
#include <stdio.h>
#include <stdlib.h>
#include <setjmp.h>
 
/* declare variable of type jmp_buf */
jmp_buf resume_here;
 
void hello(void);
 
int main(void)
{
    int ret_val;
 
    /* Initialize 'resume_here' by calling setjmp() */
    if (setjmp(resume_here)) {
        printf("After \'longjump()\', back in \'main()\'\n");
        printf("\'jump buffer variable \'resume_here\'\' becomes "
                  "INVALID!\n");
    }
    else {
        printf("\'setjmp()\' returns first time\n");
        hello();
    }
 
    return 0;
}
 
void hello(void)
{
    printf("Hey, I'm in \'hello()\'\n");
    longjmp(resume_here, 1);
 
    /* other code */
    printf("can't be reached here because I did longjmp!\n");
}
Output is written below

'setjmp()' returns first time
Hey, I'm in 'hello()'
After 'longjump()', back in 'main()'
'jump buffer variable 'resume_here'' becomes INVALID!
Notice that when ‘setjmp()’ called first time, returned value ZERO and hence ‘else clause’ executed. Within there, after printing the msg “‘setjmp()’ returns first time” called function ‘hello()’. In ‘hello()’ function, ‘longjmp()’ called which caused control immediately transferred back to ‘setjmp()’ in the ‘main()’. This time, ‘setjmp()’ returned value 1 passed to it by ‘longjmp()’ and so ‘if clause’ executed.

A ‘goto’ can’t jump out of current function while we can ‘longjmp’ long way away, even to a function in a different file.
