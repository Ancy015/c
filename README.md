Program 1 – Lexical Analyzer with Symbol Table (LEX)
File name: q1.l
%{
#include <stdio.h>
#include <string.h>

char symbols[100][100];
int count = 0;

int exists(char *s)
{
    int i;
    for(i = 0; i < count; i++)
        if(strcmp(symbols[i], s) == 0)
            return 1;
    return 0;
}

void add_symbol(char *s)
{
    if(!exists(s) && count < 100)
        strcpy(symbols[count++], s);
}
%}

%%
"int"|"float"|"char"|"if"|"else"|"while"|"for"|"return"
{
    printf("Keyword: %s\n", yytext);
}

[A-Za-z][A-Za-z0-9_]*
{
    printf("Identifier: %s\n", yytext);
    add_symbol(yytext);
}

[0-9]+(\.[0-9]+)?
{
    printf("Number: %s\n", yytext);
}

"=="|"!="|"<="|">="|"++"|"--"|"+"|"-"|"*"|"/"|"="|"<"|">"
{
    printf("Operator: %s\n", yytext);
}

[;,(){}]
{
    printf("Special symbol: %s\n", yytext);
}

[ \t\n]+ ;

.
{
    printf("Unknown: %s\n", yytext);
}
%%

int main()
{
    int i;

    printf("Enter a program:\n");
    yylex();

    printf("\nSymbol Table\n");

    for(i = 0; i < count; i++)
        printf("%d. %s\n", i+1, symbols[i]);

    return 0;
}
Program 2 – Token Identification Using LEX
File name: q2.l
%{
#include <stdio.h>
%}

%%
"int"|"float"|"char"|"double"|"if"|"else"|"while"|"for"|"return"
{
    printf("KEYWORD: %s\n", yytext);
}

[A-Za-z_][A-Za-z0-9_]*
{
    printf("IDENTIFIER: %s\n", yytext);
}

[0-9]+(\.[0-9]+)?
{
    printf("CONSTANT: %s\n", yytext);
}

"=="|"!="|"<="|">="|"++"|"--"|"+"|"-"|"*"|"/"|"="|"<"|">"
{
    printf("OPERATOR: %s\n", yytext);
}

[;,(){}]
{
    printf("SPECIAL SYMBOL: %s\n", yytext);
}

[ \t\n]+ ;

.
{
    printf("UNKNOWN: %s\n", yytext);
}
%%

int main()
{
    printf("Enter input:\n");
    yylex();
    return 0;
}
Program 3 – Arithmetic Expression Validation Using LEX and YACC
Scanner file: q3.l
%{
#include "y.tab.h"
%}

%%
[0-9]+      { return NUM; }
[+\-*/()]   { return yytext[0]; }
[ \t\n]+    ;
.           { return yytext[0]; }
%%

int yywrap()
{
    return 1;
}
parser file :93.y
%{
#include <stdio.h>

int yylex();

void yyerror(const char *s)
{
    printf("Invalid expression\n");
}
%}

%token NUM

%left '+' '-'
%left '*' '/'
%right UMINUS

%%
input: expr
{
    printf("Valid arithmetic expression\n");
};

expr: NUM
    | expr '+' expr
    | expr '-' expr
    | expr '*' expr
    | expr '/' expr
    | '(' expr ')'
    | '-' expr %prec UMINUS
    ;
%%

int main()
{
    printf("Enter an arithmetic expression: ");
    yyparse();
    return 0;
}
Program 4 – Valid Identifier Recognition Using LEX and YACC
Scanner file: q4.l
%{
#include "y.tab.h"
%}

%%
[A-Za-z][A-Za-z0-9]* { return ID; }

[ \t\n]+ ;

. { return INVALID; }
%%

int yywrap()
{
    return 1;
}
Parser file: q4.y
%{
#include <stdio.h>

int yylex();

void yyerror(const char *s)
{
}
%}

%token ID INVALID

%%
start: ID
{
    printf("Valid identifier\n");
};
%%

int main()
{
    printf("Enter an identifier: ");

    if(yyparse() != 0)
        printf("Invalid identifier\n");

    return 0;
}Program 5 – Simple Loop Statement Validation Using LEX and YACC
Scanner file: q5.l
%{
#include "y.tab.h"
%}

%%
"for"       { return FOR; }
"while"     { return WHILE; }
"("         { return '('; }
")"         { return ')'; }
";"         { return ';'; }
"="         { return '='; }
"<"|">"|"==" { return REL; }
[0-9]+      { return NUM; }
[A-Za-z][A-Za-z0-9_]* { return ID; }
[+*-]       { return yytext[0]; }
[ \t\n]+    ;
.           { return INVALID; }
%%

int yywrap()
{
    return 1;
}
parser file: q5.y
%{
#include <stdio.h>

int yylex();

void yyerror(const char *s)
{
    printf("Invalid loop statement\n");
}
%}

%token FOR WHILE ID NUM REL INVALID

%%
start: for_loop | while_loop ;

for_loop:
    FOR '(' assignment ';' condition ';' assignment ')'
    ;

while_loop:
    WHILE '(' condition ')'
    ;

assignment:
    ID '=' NUM
    ;

condition:
    ID REL NUM
    ;
%%

int main()
{
    printf("Enter a simple for/while loop header: ");

    if(yyparse() == 0)
        printf("Valid loop structure\n");

    return 0;
}
Program 6 – Arithmetic Calculator Using LEX and YACC
Scanner file: q6.l
%{
#include "y.tab.h"
#include <stdlib.h>
%}

%%
[0-9]+
{
    yylval = atoi(yytext);
    return NUM;
}

[+\-*/()] { return yytext[0]; }

[ \t\n]+ ;

. { return yytext[0]; }
%%

int yywrap()
{
    return 1;
}
Parser file: q6.y
%{
#include <stdio.h>

int yylex();

void yyerror(const char *s)
{
    printf("Invalid expression\n");
}
%}

%token NUM

%left '+' '-'
%left '*' '/'

%%
input:
    expr
    {
        printf("Result = %d\n", $1);
    }
    ;

expr:
      NUM
      {
          $$ = $1;
      }

    | expr '+' expr
      {
          $$ = $1 + $3;
      }

    | expr '-' expr
      {
          $$ = $1 - $3;
      }

    | expr '*' expr
      {
          $$ = $1 * $3;
      }

    | expr '/' expr
      {
          if($3 == 0)
          {
              yyerror("Division by zero");
              YYERROR;
          }

          $$ = $1 / $3;
      }

    | '(' expr ')'
      {
          $$ = $2;
      }
    ;
%%

int main()
{
    printf("Enter expression: ");
    return yyparse();
}
Program 7 – Three Address Code Generation Using C
File name: q7.c
#include <stdio.h>

int main()
{
    int a, b, c;
    int t1, t2, x;

    printf("Enter values of a, b and c: ");
    scanf("%d%d%d", &a, &b, &c);

    t1 = b * c;
    t2 = a + t1;
    x = t2;

    printf("\nThree Address Code:\n");

    printf("t1 = b * c\n");
    printf("t2 = a + t1\n");
    printf("x = t2\n");

    printf("\nValues:\n");

    printf("t1 = %d\n", t1);
    printf("t2 = %d\n", t2);
    printf("x = %d\n", x);

    return 0;
}
Program 8 – Basic Declaration and Assignment Checking Using LEX and YACC
Scanner file: q8.l
%{
#include "y.tab.h"
%}

%%
"int"       { return INT; }
"float"     { return FLOAT; }

[A-Za-z][A-Za-z0-9_]*
{
    return ID;
}

[0-9]+(\.[0-9]+)?
{
    return NUM;
}

"="         { return '='; }
";"         { return ';'; }

[ \t\n]+ ;

. { return INVALID; }
%%

int yywrap()
{
    return 1;
}
parser file : q8.y 
%{
#include <stdio.h>

int yylex();

void yyerror(const char *s)
{
    printf("Invalid statement or type syntax\n");
}
%}

%token INT FLOAT ID NUM INVALID

%%
start:
      declaration
    | assignment
    ;

declaration:
      INT ID ';'
      {
          printf("Valid integer declaration\n");
      }

    | FLOAT ID ';'
      {
          printf("Valid float declaration\n");
      }
    ;

assignment:
    ID '=' NUM ';'
    {
        printf("Valid simple assignment\n");
    }
    ;
%%

int main()
{
    printf("Enter one declaration or assignment: ");
    return yyparse();
}
Program 9 – Code Optimization Using C
File name: q9.c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

int is_number(const char *s)
{
    int i = 0;

    if(s[0] == '-' || s[0] == '+')
        i++;

    if(s[i] == '\0')
        return 0;

    for(; s[i]; i++)
    {
        if(!isdigit((unsigned char)s[i]))
            return 0;
    }

    return 1;
}

int main()
{
    char result[30], left[30], op, right[30];
    int a, b;

    printf("Enter statement (example: x = 10 + 20): ");

    if(scanf("%29s = %29s %c %29s",
             result, left, &op, right) != 4)
    {
        printf("Input format is not correct.\n");
        return 1;
    }

    printf("\nOriginal statement: %s = %s %c %s\n",
           result, left, op, right);

    if(is_number(left) && is_number(right))
    {
        a = atoi(left);
        b = atoi(right);

        switch(op)
        {
            case '+':
                printf("Optimized: %s = %d\n", result, a+b);
                break;

            case '-':
                printf("Optimized: %s = %d\n", result, a-b);
                break;

            case '*':
                printf("Optimized: %s = %d\n", result, a*b);
                break;

            case '/':
                if(b == 0)
                {
                    printf("Cannot divide by zero.\n");
                    return 1;
                }

                printf("Optimized: %s = %d\n", result, a/b);
                break;

            default:
                printf("Operator not supported.\n");
        }
    }

    else if(op == '+' && strcmp(right, "0") == 0)
    {
        printf("Optimized: %s = %s\n", result, left);
    }

    else if(op == '*' && strcmp(right, "1") == 0)
    {
        printf("Optimized: %s = %s\n", result, left);
    }

    else if(op == '*' && strcmp(right, "0") == 0)
    {
        printf("Optimized: %s = 0\n", result);
    }

    else if(op == '-' && strcmp(right, "0") == 0)
    {
        printf("Optimized: %s = %s\n", result, left);
    }

    else if(op == '*' && strcmp(right, "2") == 0)
    {
        printf("Strength reduction: %s = %s + %s\n",
               result, left, left);
    }

    else
    {
        printf("No simple optimization applied.\n");
    }

    return 0;
}
Program 10 – Target Code Generation Using C
File name: q10.c
#include <stdio.h>
#include <string.h>

int main()
{
    char temp[30], left[30], op, right[30];

    printf("Enter TAC (example: t1 = a + b): ");

    if(scanf("%29s = %29s %c %29s",
             temp, left, &op, right) != 4)
    {
        printf("Invalid input format.\n");
        return 1;
    }

    printf("\nGenerated 8086-style code:\n");

    printf("MOV AX, %s\n", left);

    switch(op)
    {
        case '+':
            printf("ADD AX, %s\n", right);
            break;

        case '-':
            printf("SUB AX, %s\n", right);
            break;

        case '*':
            printf("MUL %s\n", right);
            break;

        case '/':
            printf("DIV %s\n", right);
            break;

        default:
            printf("Unsupported operator\n");
            return 1;
    }

    printf("MOV %s, AX\n", temp);

    printf("\nNote: This is illustrative assembly-like output.\n");

    return 0;
}
PART 2 – HOW TO SAVE AND RUN THE PROGRAMS
Step 1 – Install required software
Open the Linux Terminal and type:
sudo apt update
sudo apt install flex bison gcc


Step 2 – Create a folder
mkdir compiler
cd compiler


Step 3 – Save the program files
For example, to save Program 1:
nano q1.l


- Type or paste the Program 1 code.
- Press Ctrl + O to save.
- Press Enter.
- Press Ctrl + X to exit.
Repeat for the other programs using their filenames.
Step 4 – Run LEX-only programs (1 and 2)
For Program 1:
flex q1.l
gcc lex.yy.c -o q1 -lfl
./q1


For Program 2:
flex q2.l
gcc lex.yy.c -o q2 -lfl
./q2


Step 5 – Run LEX and YACC programs (3, 4, 5, 6 and 8)
Example for Program 3:
bison -dy q3.y
flex q3.l
gcc y.tab.c lex.yy.c -o q3 -lfl
./q3


For other programs, replace q3 with q4, q5, q6 or q8.

Important: Run Bison/YACC first because it generates the y.tab.h file used by the LEX program.
Step 6 – Run normal C programs (7, 9 and 10)
Example for Program 7:
gcc q7.c -o q7
./q7


For Program 9:
gcc q9.c -o q9
./q9


For Program 10:
gcc q10.c -o q10
./q10



PART 3 – ONE COMMON ALGORITHM
Algorithm: General Procedure for Compiler Design Practical Programs
1. Start the program.
2. Read and understand the given question carefully.
3. Identify the main purpose of the program, such as token identification, identifier validation, arithmetic expression evaluation, loop validation, code optimization or code generation.
4. Identify the input elements required for the program. These may include keywords, identifiers, constants, operators, brackets and special symbols.
5. Divide the work into lexical analysis and syntax analysis wherever necessary.
6. Create a LEX file using the .l extension.
7. Include the required header files at the beginning of the LEX program.
8. Write regular expressions to recognize different tokens.
9. Define rules for keywords, identifiers, numbers, operators and special symbols.
10. Write suitable actions for each rule to print, store or return the recognized tokens.
11. Ignore unnecessary spaces, tabs and newline characters.
12. If only token identification is required, write the main function and call yylex().
13. If syntax validation is required, create a YACC file using the .y extension.
14. Include the required header files and declare the tokens used in the program.
15. Write grammar production rules to describe the valid structure of the input.
16. Add semantic actions to perform calculations, display messages or generate intermediate code when required.
17. Define the yyerror() function to display an error message when invalid input is given.
18. Write the main function and call yyparse() to start syntax analysis.
19. Save all files with the correct filenames in the same folder.
20. Generate the YACC parser and header file first.
21. Generate the lexical analyzer using the LEX or Flex command.
22. Compile the generated C files using the GCC compiler.
23. Run the executable file.
24. Enter the required input and check the output.
25. Test the program with both valid and invalid inputs.
26. If an error occurs, check the filenames, token declarations, grammar rules and compilation commands.
27. After successful execution, record the input and output in the practical record.
28. Stop the program.

PART 4 – VIVA QUESTIONS AND ANSWERS
Basic Compiler Questions
1. What is a compiler?

A compiler is a software program that converts a high-level language program into machine code or target code.

2. What are the phases of a compiler?
1. Lexical analysis
2. Syntax analysis
3. Semantic analysis
4. Intermediate code generation
5. Code optimization
6. Code generation
3. What is LEX?

LEX is a tool used to generate a lexical analyzer.

4. What is YACC?

YACC is a tool used to generate a parser.

5. What is a token?

A token is a basic unit of a program, such as a keyword, identifier or operator.

6. What is a lexeme?

A lexeme is the actual text that matches a token.

7. What is lexical analysis?

Lexical analysis is the first phase of a compiler. It converts the input characters into tokens.

8. What is a symbol table?

A symbol table stores information about identifiers used in a program.

9. What is an identifier?

An identifier is a name given to a variable, function or other program element.

10. What is a keyword?

A keyword is a reserved word that has a special meaning in a programming language.
LEX and YACC Questions
11. What is a regular expression?

A regular expression is a pattern used to recognize a group of characters.

12. What is a parser?

A parser checks whether the input follows the grammar rules.

13. What is grammar?

Grammar is a collection of rules used to define the structure of a language.

14. What is a syntax error?

A syntax error occurs when the input does not follow the grammar rules.

15. What is operator precedence?

Operator precedence determines which operator is evaluated first.

Example: Multiplication is performed before addition.

16. What is associativity?

Associativity determines the order of evaluation when operators have the same precedence.

17. What is a semantic action?

A semantic action is a piece of C code written inside a YACC grammar rule.

18. What is yyparse()?

yyparse() is the function used to start parsing in YACC.

19. What is yylex()?

yylex() is the function used to identify tokens in LEX.

20. What is yytext?

yytext stores the text matched by the current LEX rule.
Program-Based Questions
21. What is an arithmetic calculator?

It is a program that performs arithmetic operations such as addition, subtraction, multiplication and division.

22. What is a loop?

A loop is used to execute a group of statements repeatedly.

23. What is type checking?

Type checking verifies whether the data types used in a statement are compatible.

24. What is type conversion?

Type conversion means changing one data type into another.

25. What is intermediate code?

Intermediate code is a representation generated between source code and target code.

26. What is three-address code?

Three-address code is an intermediate code in which an instruction contains at most three addresses or operands.

27. Give an example of three-address code.
t1 = a + b


28. Why are temporary variables used?

Temporary variables store intermediate results during expression evaluation.

29. What is code optimization?

Code optimization improves the efficiency of a program without changing its output.

30. What is constant folding?

Constant folding evaluates constant expressions during compilation.

Example:
x = 10 + 20


Optimized form:
x = 30


31. What is strength reduction?

Strength reduction replaces a costly operation with a simpler operation.

Example:
x = a * 2


Can be simplified to:
x = a + a


32. What is algebraic simplification?

It simplifies expressions using mathematical rules.

Examples:
a + 0 = a
a * 1 = a
a * 0 = 0


33. What is code generation?

Code generation converts intermediate code into target-machine instructions.

34. What is 8086?

8086 is a 16-bit microprocessor.

35. What is AX?

AX is the accumulator register in the 8086 processor.

36. What is MOV?

MOV is an instruction used to copy data from one location to another.

37. What is ADD?

ADD is an instruction used to perform addition.

38. What is SUB?

SUB is an instruction used to perform subtraction.

39. What is the difference between LEX and YACC?
- LEX identifies tokens.
- YACC checks the syntax and grammar of the input.
40. Why should YACC be executed before LEX?

YACC generates the y.tab.h header file, which is included in the LEX program.
