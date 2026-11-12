# Compiler Design Lab -- README

This README contains all 10 Compiler Design lab experiments in one
place, with their aims, source code, sample inputs, and expected
outputs.

> **Note:** Experiments 2--5 use separate LEX (`.l`) and YACC (`.y`)
> files. Experiments 6--10 are simplified C demonstrations. Some later
> experiments are illustrative and do not implement a complete compiler
> optimization or machine-code backend.

## General LEX/YACC compilation

For a LEX/YACC experiment named `name`:

``` bash
yacc -d name.y
lex name.l
gcc y.tab.c lex.yy.c -ll -o output
./output
```

Depending on your system, use `-ly` or `-lfl` instead of `-ll`. If using
Flex and Bison, the commands may be:

``` bash
bison -d name.y
flex name.l
gcc name.tab.c lex.yy.c -lfl -o output
./output
```

For the Bison version, update the LEX include from `name.tab.h` only if
that is the generated header name.

------------------------------------------------------------------------

## Experiment 1 -- Lexical Analyzer and Symbol Table

**Aim:** To identify tokens such as identifiers, constants, comments,
and operators using LEX.

**Program (`lex.l`):**

``` c
%{
#include <stdio.h>
%}
%%
[0-9]+          printf("Constant: %s\\n", yytext);
[a-zA-Z]+       printf("Identifier: %s\\n", yytext);
"+"|"-"|"*"|"/" printf("Operator: %s\\n", yytext);
";"             printf("Punctuation: %s\\n", yytext);
[ \\t\\n]         ;
.               ;
%%
int main()
{
    printf("Enter input:\\n");
    yylex();
    return 0;
}
int yywrap()
{
    return 1;
}
```

**Sample input:**

``` c
int a = 5;
```

**Expected output:**

``` text
Identifier: int
Identifier: a
Constant: 5
Punctuation: ;
```

The simple rules above do not include `=` as an operator, so it is
ignored by the catch-all rule. To print it as an operator, add `"="` to
the operator rule. This is a basic lexer demonstration, not a complete
symbol-table implementation.

------------------------------------------------------------------------

## Experiment 2 -- Arithmetic Expression Recognition

**Aim:** To recognize a valid arithmetic expression using LEX and YACC.

**LEX (`valid.l`):**

``` c
%{
#include "valid.tab.h"
%}
%%
[a-zA-Z]+  return VARIABLE;
[0-9]+     return NUMBER;
[ \\t]      ;
\\n         return 0;
.          return yytext[0];
%%
int yywrap(){ return 1; }
```

**YACC (`valid.y`):**

``` c
%{
#include <stdio.h>
int yylex();
void yyerror(char *s);
%}
%token NUMBER VARIABLE
%left '+' '-'
%left '*' '/'
%%
S: E { printf("Valid Expression"); };
E: E '+' E
 | E '-' E
 | E '*' E
 | E '/' E
 | '(' E ')'
 | NUMBER
 | VARIABLE
 ;
%%
int main()
{
    printf("Enter expression: ");
    yyparse();
    return 0;
}
void yyerror(char *s)
{
    printf("Invalid Expression");
}
```

**Sample input:** `a+b*5`

**Expected output:** `Valid Expression`

------------------------------------------------------------------------

## Experiment 3 -- Valid Identifier Recognition

**Aim:** To check whether an identifier starts with a letter followed by
letters or digits.

**LEX (`id.l`):**

``` c
%{
#include "id.tab.h"
%}
%%
[a-zA-Z][a-zA-Z0-9]* return ID;
. return INVALID;
\\n return 0;
%%
int yywrap(){ return 1; }
```

**YACC (`id.y`):**

``` c
%{
#include <stdio.h>
int yylex();
void yyerror(char *s);
%}
%token ID INVALID
%%
S: ID { printf("Valid Identifier"); }
 | INVALID { printf("Invalid Identifier"); }
 ;
%%
int main()
{
    printf("Enter identifier: ");
    yyparse();
    return 0;
}
void yyerror(char *s)
{
    printf("Invalid Identifier");
}
```

**Sample input:** `student123`

**Expected output:** `Valid Identifier`

------------------------------------------------------------------------

## Experiment 4 -- While Loop Recognition

**Aim:** To recognize a valid while loop using LEX and YACC.

**LEX (`wh.l`):**

``` c
%{
#include "wh.tab.h"
%}
%%
"while" return WHILE;
"(" return LP;
")" return RP;
"{" return LB;
"}" return RB;
[a-zA-Z][a-zA-Z0-9]* return ID;
[0-9]+ return NUM;
"<"|">"|"==" return OP;
"=" return ASSIGN;
";" return ';';
[ \\t\\n] ;
. return yytext[0];
%%
int yywrap(){ return 1; }
```

**YACC (`wh.y`):**

``` c
%{
#include <stdio.h>
int yylex();
void yyerror(char *s);
%}
%token WHILE LP RP LB RB ID NUM OP ASSIGN
%%
S: WHILE LP ID OP NUM RP LB ID ASSIGN NUM ';' RB
  { printf("Valid While Loop"); }
 ;
%%
int main()
{
    printf("Enter while loop: ");
    yyparse();
    return 0;
}
void yyerror(char *s)
{
    printf("Invalid While Loop");
}
```

**Sample input:**

``` c
while(a<10){a=5;}
```

**Expected output:** `Valid While Loop`

------------------------------------------------------------------------

## Experiment 5 -- Calculator Using LEX and YACC

**Aim:** To implement a calculator using LEX and YACC.

**LEX (`calc.l`):**

``` c
%{
#include "calc.tab.h"
#include <stdlib.h>
%}
%%
[0-9]+ { yylval = atoi(yytext); return NUM; }
"+" return '+';
"-" return '-';
"*" return '*';
"/" return '/';
\\n return 0;
[ \\t] ;
%%
int yywrap(){ return 1; }
```

**YACC (`calc.y`):**

``` c
%{
#include <stdio.h>
int yylex();
void yyerror(char *s);
%}
%token NUM
%left '+' '-'
%left '*' '/'
%%
S: E { printf("Result = %d", $1); };
E: E '+' E { $$ = $1 + $3; }
 | E '-' E { $$ = $1 - $3; }
 | E '*' E { $$ = $1 * $3; }
 | E '/' E { $$ = $1 / $3; }
 | NUM { $$ = $1; }
 ;
%%
int main()
{
    printf("Enter expression: ");
    yyparse();
    return 0;
}
void yyerror(char *s)
{
    printf("Invalid Expression");
}
```

**Sample input:** `10+5*2`

**Expected output:** `Result = 20`

------------------------------------------------------------------------

## Experiment 6 -- Three Address Code Generation

**Aim:** To demonstrate generation of Three Address Code (TAC).

**Program (`tac.c`):**

``` c
#include <stdio.h>

int main()
{
    int a, b, c, t1, t2;

    printf("Enter a, b and c: ");
    scanf("%d%d%d", &a, &b, &c);

    t1 = b * c;
    t2 = a + t1;

    printf("t1 = b * c\\n");
    printf("t2 = a + t1\\n");
    printf("Result = %d\\n", t2);

    return 0;
}
```

**Sample input:**

``` text
2 3 4
```

**Expected output:**

``` text
t1 = b * c
t2 = a + t1
Result = 14
```

------------------------------------------------------------------------

## Experiment 7 -- Type Checking

**Aim:** To demonstrate basic type selection and checking using a C
program.

**Program (`typecheck.c`):**

``` c
#include <stdio.h>

int main()
{
    char type;
    int a, b;

    printf("Enter datatype (i/f): ");
    scanf(" %c", &type);

    printf("Enter two numbers: ");
    scanf("%d%d", &a, &b);

    if (type == 'i')
        printf("Integer type is valid");
    else if (type == 'f')
        printf("Float type is valid");
    else
        printf("Type mismatch");

    return 0;
}
```

**Sample input:**

``` text
i
10 20
```

**Expected output:** `Integer type is valid`

> This is a simplified demonstration. It does not compare declared
> operand types or perform full compiler type checking.

------------------------------------------------------------------------

## Experiment 8 -- Dead Code Elimination

**Aim:** To illustrate the idea of removing statements whose results are
not used.

**Program (`deadcode.c`):**

``` c
#include <stdio.h>

int main()
{
    int a = 10, b = 20, c;

    c = a + b;

    printf("Optimized Code:\\n");
    printf("a = 10\\n");
    printf("b = 20\\n");
    printf("c = a + b\\n");

    return 0;
}
```

**Expected output:**

``` text
Optimized Code:
a = 10
b = 20
c = a + b
```

> This prints an example of optimized code; it does not implement a
> general dead-code elimination algorithm.

------------------------------------------------------------------------

## Experiment 9 -- Strength Reduction

**Aim:** To demonstrate replacing a multiplication operation with an
equivalent addition operation.

**Program (`strength.c`):**

``` c
#include <stdio.h>

int main()
{
    int i, s;

    printf("Strength Reduction:\\n");

    for (i = 1; i <= 10; i++)
    {
        s = i + i;
        printf("%d ", s);
    }

    return 0;
}
```

**Expected output:**

``` text
Strength Reduction:
2 4 6 8 10 12 14 16 18 20
```

> The example uses addition to calculate twice the value of `i`,
> illustrating the basic idea of strength reduction.

------------------------------------------------------------------------

## Experiment 10 -- Compiler Backend / Target Code Generation

**Aim:** To demonstrate generating simple target-like instructions from
an arithmetic assignment.

**Program (`backend.c`):**

``` c
#include <stdio.h>

int main()
{
    char a, b, c, op;

    printf("Enter expression (a=b+c): ");
    scanf(" %c=%c%c%c", &a, &b, &op, &c);

    printf("\\nTarget Code:\\n");
    printf("MOV R0,%c\\n", b);

    if (op == '+') printf("ADD R0,%c\\n", c);
    if (op == '-') printf("SUB R0,%c\\n", c);
    if (op == '*') printf("MUL R0,%c\\n", c);
    if (op == '/') printf("DIV R0,%c\\n", c);

    printf("MOV %c,R0\\n", a);

    return 0;
}
```

**Sample input:** `a=b+c`

**Expected output:**

``` text
Target Code:
MOV R0,b
ADD R0,c
MOV a,R0
```

> These are illustrative register instructions, not verified 8086
> assembly syntax.

------------------------------------------------------------------------

## Quick Experiment Index

    No. Experiment                          Main tool / language
  ----- ----------------------------------- ----------------------
      1 Lexical Analyzer                    LEX
      2 Arithmetic Expression Recognition   LEX + YACC
      3 Identifier Recognition              LEX + YACC
      4 While Loop Recognition              LEX + YACC
      5 Calculator                          LEX + YACC
      6 Three Address Code                  C
      7 Type Checking                       C
      8 Dead Code Elimination               C demonstration
      9 Strength Reduction                  C demonstration
     10 Compiler Backend                    C demonstration

## End of README

Compile and run each experiment separately. Keep each LEX and YACC pair
in matching files with the same base name, and use the appropriate
generated header name for your installed tools.
