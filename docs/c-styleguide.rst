
indent and space
================

- No literal tabs. Expand tabs to 4 spaces.
- Indentation is 4 spaces.
- Make sure that your editor does not leave space at the end of the line.

type
====

- Avoid using int, char, short, long. Instead use int8_t uint8_t, int16_t,
  uint16_t, int32_t, uint32_t, int64_t, uint64_t, which are available in
  <stdint.h>. However,

  when interfacing with system calls and libraries you cannot get away from
  using int and char.  for example::

    int r = memcmp(a, b, n);
    int ret = pthread_join(job->thread, NULL);

- Use bool for boolean variables. You have to include <stdbool.h>
- Avoid using a bool as type for struct member names. Instead use unsigned
  1-bit bit field. Eg::

      struct foo {
          unsigned is_bar:1;
      };

- Always use size_t type when dealing with **sizes of objects or memory ranges**.
- Your code should be 64-bit and 32-bit friendly. Bear in mind problems
  of printing, comparisons, and structure alignment. You have to include
  <intyptes.h> to get generic format specifier macros for printing.

- not use snprintf(use nc_scnprintf)

misc
====

- No more than 3 levels of indentation, otherwise you should think about
  refactoring your code.
- Use one statement per line.
- snake_case for variable, function and file names.
- Use your own judgment when naming variables and functions. Be as Spartan
  as possible. Eg: Using name like this_variable_is_a_temporary_counter
  will usually be frowned upon.
- Don’t use local variables or parameters that shadow global identifiers.
  GCC’s ‘-Wshadow’ option can help you to detect this problem.

- 80 column line limit.
- If you have to wrap a long statement (> 80 column), put the operator at the
  end of the line and indent the next line at the same column as the arguments
  in the previous column. Eg:
    while (cnt < 20 && this_variable_name_is_too_long &&
           ep != NULL) {
        z = a + really + long + statement + that + needs + three + lines +
            gets + indented + on + the + same + column + as + the +
            previous + column
    }

    and:

    int a = function(param_a, param_b, param_c, param_d, param_e, param_f,
                     param_g, param_h, param_i, param_j, param_k, param_l);

- Always use braces for all conditional blocks (if, switch, for, while, do).
  This holds good even for single statement conditional blocks. Eg:
    if (cond) {
        stmt;
    }
- Placement of braces for non-function statement blocks - put opening brace
  last on the line and closing brace first. Eg:
    if (x is true) {
        we do y
    }
- Placement of brace for functions - put the opening brace at the beginning
  of the next line and closing brace first. This is useful because several
  tools look for opening brace in column one to find beginning of C
  functions. Eg:
int
function(int x)
{
    body of the function
}

- Closing brace is empty on a line of its own, except in cases where it is
  followed by a continuation of the same statement, i.e. a "while" in a
  do-statement or an "else" in an if-statement, like this:
    do {
        body of do-loop
    } while (condition);

    and,

    if (x == y) {
        ..
    } else if (x > y) {
        ...
    } else {
        ....
    }

- Column align switch keyword and the corresponding case/default keyword. Eg:
    switch (alphabet) {
    case 'a':
    case 'b':
        printf("I am a or b\n");
        break;

    default:
        break;
    }

- Forever loops are done with for, and not while. Eg:
    for (;;) {
        stmt;
    }

- Don't use a space after a function name.
- Do not needlessly surround the return expression with parentheses.
- Use space after keywords. Exceptions are sizeof, typeof, alignof and
  __attribute__, which look like functions.
- Do not add spaces around (inside) parenthesized expressions.
    s = sizeof( sizeof(*p)) ); /* bad example */
    s = sizeof(sizeof(*p));    /* good example */
- Casts should not be followed by space. Eg:
  int q = *(int *)&p
- There is no need to type cast when assigning a void pointer to a non-void
  pointer, or vice versa.
- Avoid using goto statements. However there are some exceptions to this rule
  when a single goto label within a function and one or more goto statements
  come in handy when a function exits from multiple locations and some common
  work such as cleanup has to be done. Eg:
int
fun(void)
{
    int result = 0;
    char *buffer;

    buffer = malloc(1024);
    if (buffer == NULL) {
        return -1;
    }

    if (condition1) {
        while (loop1) {
            ...
        }
        result = 1;
        goto out;
    }

    ...
out:
    free(buffer);
    return result;
}
- When declaring pointer data, use '*' adjacent to the data name and not
  adjacent to the type name. Eg:
    int
    function(int *p)
    {
        char *p;
        <body of the function>
    }
- Use one space around (on each side of) most binary and ternary operators,
  such as any of these:
    =  +  -  <  >  *  /  %  |  &  ^  <=  >=  ==  !=  ?  :
  but no space after unary operators:
    &  *  +  -  ~  !  sizeof typeof alignof  __attribute__  defined
  no space before the postfix increment & decrement unary operators:
    ++  --
  and no space around the '.' and "->" structure member operators.

- 0 and NULL; use 0 for integers, 0.0 for doubles, NULL for pointers, and
  '\0' for chars.
- Test pointers against NULL. E.g, use:
    if (p == NULL)

    not:

    !(p)

- Do not use ! for tests unless it is a boolean. E.g. use:
    if (*p == '\0')

    not:

    if (!*p)

- Don't use assignments inside if or while-conditions. E.g, use:

    struct foo *foo;
    foo = malloc(sizeof(*foo));
    if (foo == NULL) {
        return -1
    }

    not:

    struct foo *foo;
    if ((foo = malloc(sizeof(*foo))) == NULL) {
        return -1;
    }

- Don't ever use typedef for structure types. Typedefs are problematic
  because they do not properly hide their underlying type; for example you
  need to know if the typedef is the structure itself or a pointer to the
  structure. In addition they must be declared exactly once, whereas an
  incomplete structure type can be mentioned as many times as necessary.
  Typedefs are difficult to use in stand-alone header files: the header
  that defines the typedef must be included before the header that uses it,
  or by the header that uses it (which causes namespace pollution), or
  there must be a back-door mechanism for obtaining the typedef.
- The only exception for using a typedef is when you are defining a type
  for a function pointer or a type for an enum. Eg:

  typedef void (*foo_handler_t)(int, void *);

  or:

  typedef enum types {
      TYPE_1,
      TYPE_2
  } types_t;

- Use just one variable declaration per line when variables are part of a
  struct. This leaves you room for a small comment on each item, explaining
  its use. Declarations should also be aligned. Eg, use:

  struct foo {
    int      *foo_a;   /* comment for foo_a */
    int      foo_b;    /* comment for foo_b */
    unsigned foo_c:1;  /* comment for foo_c */
  };

  and not:

  struct foo {
    int *foo_a, foo_b;
    unsigned foo_c:1;
  };

- For variable declaration outside a struct, either collect all the
  declarations of the same type on a single line, or use one variable
  per line if the variables purpose needs to be commented. Eg:
  char *a, *b, c;

  or:

  char *a, *b;
  char c;       /* comments for c */

- Avoid magic numbers because no-one has a clue (including the author) of
  what it means after a month.

- Function definitions should start the name of the function in column
  one. This is useful because it makes searching for function definitions
  fairly trivial. Eg:
static char *
concat(char *s1, char *s2)
{
  body of the function
}

- Function and variables local to a file should be static.
- Separate two successive functions with one blank line.
- Include parameter names with their datypes in function declaration. Eg:
void function(int param);

- Functions should be short and sweet, and do just one thing. They should
  fit on one or two screenfuls of text (80 x 24 screen size), and do one
  thing and do that well.
  The maximum length of a function is inversely proportional to the
  complexity and indentation level of that function. So, if you have a
  conceptually simple function that is just one long (but simple)
  case-statement, where you have to do lots of small things for a lot of
  different cases, it's OK to have a longer function.
  Another measure of the function is the number of local variables. They
  shouldn't exceed 5-10, or you're doing something wrong. Re-think the
  function, and split it into smaller pieces. A human brain can
  generally easily keep track of about 7 different things, anything more
  and it gets confused. You know you're brilliant, but maybe you'd like
  to understand what you did 2 weeks from now.
- Use const for function parameters passed by reference, if the passed
  pointer has no side effect.

- C style comments only. Don't use // for single line comments. Instead
  use /* ... */ style.
- For multi-line comments use the following style
    /*
     * This is the preferred style for multi-line
     * comments in the Linux kernel source code.
     * Please use it consistently.
     *
     * Description:  A column of asterisks on the left side,
     * with beginning and ending almost-blank lines.
     */

- To comment out block of code spanning several lines use preprocessor
  directive "#ifdef 0 ... #endif"

- Please write a brief comment at the start of each source file, with the
  file name and a line or two about the overall purpose of the file.

- All major functions should have comments describing what they do at the
  head of the function. Avoid putting comments in the function body unless
  absolutely needed. If possible, add a comment on what sorts of arguments
  the function gets, and what the possible values of arguments mean and
  what they are used for and the significance of return value if there is
  one. It is not necessary to duplicate in words the meaning of the C
  argument declarations, if a C type is being used in its customary fashion.
  If there is anything nonstandard about its use (such as an argument of
  type char * which is really the address of the second character of a
  string, not the first), or any possible values that would not work the
  way one would expect (such as, that strings containing newlines are not
  guaranteed to work), be sure to say so. Eg:

/*
 * Try to acquire a physical address lock while a pmap is locked. If we
 * fail to trylock we unlock and lock the pmap directly and cache the
 * locked pa in *locked. The caller should then restart their loop in case
 * the virtual to physical mapping has changed.
 *
 * Returns 0 on success and -1 on failure.
 */
int
vm_page_pa_tryrelock(pmap_t pmap, vm_paddr_t pa, vm_paddr_t *locked)
{
    ...

- The comment on a function is much clearer if you use the argument names
  to speak about the argument values. The variable name itself should be
  lower case, but write it in upper case when you are speaking about the
  value rather than the variable itself. Thus, “the inode number NODE_NUM”
  rather than “an inode”.

- Every struct definition should have an accompanying comment that
  describes what it is for and how it should be used.

- Finally, while comments are absolutely important to keep the code readable,
  remember that the best code is self-documenting. Giving sensible names to
  types and variables is much better than using obscure names that you must
  then explain through comments.

- Recommend using UPPERCASE for macro names. However, sometimes using
  lowercase for macro names makes sense when macros masquerade as well-known
  function calls. Eg, it makes sense to write the wrapper for the
  standard free() function in lowercase to keep the readability
  consistent:

#define my_free(_p) do {    \
    free(_p);               \
    (_p) = NULL;            \
} while (0)

- Use enums when defining more than one related constants. All enumeration
  values are in UPPERCASE.
- Avoid macros as much as possible and use inline functions, enums and const
  variables wherever you can.
- For macros encapsulating compound statements, right justify the backslashes
  and enclose it in do { ... } while (0)
- For parameterized macros, all the parameters used in the macro body must
  be surrounded by parentheses. Eg:
  #define ADD_1(_x) ((_x) + 1)

- Use sizeof(varname) instead of sizeof(type) whenever possible. Eg:
  char *p;
  p = malloc(sizeof(*p));   /* good example */
  p = malloc(sizeof(char)); /* bad example */

- All variables should be declared at the beginning of a scope block {..}.
  It is even preferred to declare all variables at the beginning of the
  function so that all the local variable declarations is in one place and
  we can see the comprehensive list in one glance.
- Global structs should be declared at the top of the file in which they
  are used, or in separate header files if they are used in multiple
  source files.
- Declarations of external functions and functions to appear later in the
  source file should all go in one place near the beginning of the file,
  somewhere before the first function definition in the file or else
  should go in a header file.
- Use of extern should be considered as evil, if it is used in header files
  to reference global variables.
- Don’t put extern declarations inside functions.

- Usually every *.c file should have an associated *.h file. There are some
  exceptions to this rule, such as unit tests and small *.c files containing
  just the main() function.
- Every header file in the source code must have preprocessor conditional
  to prevent the header file from being scanned multiple times and avoiding
  mutual dependency cycles. Alternatively you can use #pragma once directive,
  as it avoids name clashes and increases the compile speed. Eg, for a
  header file named foo.h, the entire contents of the header file must be
  between the guard macros as follows:

#ifndef _FOO_H_
#define _FOO_H_
...
#endif /* _FOO_H_ */

Or,

#pragma once
#ifndef _FOO_H_
#define _FOO_H_
...
#endif /* _FOO_H_ */

- Don't use #include when a forward declaration would suffice.
- Functions defined in header files should be static inline.

- Don’t make the program ugly just to placate GCC when extra warnings options
  such as ‘-Wconversion’ or ‘-Wundef’ are used. These options can help in
  finding bugs, but they can also generate so many false alarms that that
  it hurts readability to silence them with unnecessary casts, wrappers, and
  other complications.

- Conditional compilation: when supporting configuration options already
  known when building your program we prefer using if (... ) over conditional
  compilation, as in the former case the compiler is able to perform more
  extensive checking of all possible code paths. Eg, use:

  if (HAS_FOO)
    ...
  else
    ...

instead of:

  #ifdef HAS_FOO
    ...
  #else
    ...
  #endif

  A modern compiler such as GCC will generate exactly the same code in both
  cases and of course, the former method assumes that HAS_FOO is defined as
  either 0 or 1.

- Finally, rules are rules. Sometimes they are sensible and sometimes not
  and regardless of your preference, we would like you to follow them.
  A project is easier to follow if all project contributors follow the style
  rules so that they can all read and understand everyone's code easily. But
  remember, like all good rules, they are exceptions where it makes sense not
  to be too rigid on the grounds of common sense and consistency!
