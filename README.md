# COMPILER DESIGN PRACTICAL

## PART 1 – PROGRAMS

### Program 1 – Lexical Analyzer with Symbol Table (q1.l)

```lex
%{
#include <stdio.h>
#include <string.h>
char symbols[100][100];
int count = 0;
int exists(char *s) {
    int i;
    for(i=0;i<count;i++)
        if(strcmp(symbols[i],s)==0) return 1;
    return 0;
}
void add_symbol(char *s) {
    if(!exists(s) && count<100)
        strcpy(symbols[count++],s);
}
%}
%%
"int"|"float"|"char"|"if"|"else"|"while"|"for"|"return"
{ printf("Keyword: %s\\n",yytext); }
[A-Za-z][A-Za-z0-9_]*
{ printf("Identifier: %s\\n",yytext); add_symbol(yytext); }
[0-9]+(\\.[0-9]+)?
{ printf("Number: %s\\n",yytext); }
"=="|"!="|"<="|">="|"++"|"--"|"+"|"-"|"*"|"/"|"="|"<"|">"
{ printf("Operator: %s\\n",yytext); }
[;,(){}]
{ printf("Special symbol: %s\\n",yytext); }
[ \\t\\n]+ ;
.
{ printf("Unknown: %s\\n",yytext); }
%%
int main() {
    int i;
    printf("Enter a program:\\n");
    yylex();
    printf("\\nSymbol Table\\n");
    for(i=0;i<count;i++)
        printf("%d. %s\\n",i+1,symbols[i]);
    return 0;
}
```

### Program 2 – Token Identification Using LEX (q2.l)

```lex
%{
#include <stdio.h>
%}
%%
"int"|"float"|"char"|"double"|"if"|"else"|"while"|"for"|"return"
{ printf("KEYWORD: %s\\n",yytext); }
[A-Za-z_][A-Za-z0-9_]*
{ printf("IDENTIFIER: %s\\n",yytext); }
[0-9]+(\\.[0-9]+)?
{ printf("CONSTANT: %s\\n",yytext); }
"=="|"!="|"<="|">="|"++"|"--"|"+"|"-"|"*"|"/"|"="|"<"|">"
{ printf("OPERATOR: %s\\n",yytext); }
[;,(){}]
{ printf("SPECIAL SYMBOL: %s\\n",yytext); }
[ \\t\\n]+ ;
.
{ printf("UNKNOWN: %s\\n",yytext); }
%%
int main() {
    printf("Enter input:\\n");
    yylex();
    return 0;
}
```

### Program 3 – Arithmetic Expression Validation

**q3.l**
```lex
%{
#include "y.tab.h"
%}
%%
[0-9]+      { return NUM; }
[+\\-*/()]   { return yytext[0]; }
[ \\t\\n]+    ;
.           { return yytext[0]; }
%%
int yywrap() { return 1; }
```

**q3.y**
```yacc
%{
#include <stdio.h>
int yylex();
void yyerror(const char *s) {
    printf("Invalid expression\\n");
}
%}
%token NUM
%left '+' '-'
%left '*' '/'
%right UMINUS
%%
input: expr {
    printf("Valid arithmetic expression\\n");
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
int main() {
    printf("Enter an arithmetic expression: ");
    yyparse();
    return 0;
}
```

### Program 4 – Valid Identifier Recognition

**q4.l**
```lex
%{
#include "y.tab.h"
%}
%%
[A-Za-z][A-Za-z0-9]* { return ID; }
[ \\t\\n]+ ;
. { return INVALID; }
%%
int yywrap() { return 1; }
```

**q4.y**
```yacc
%{
#include <stdio.h>
int yylex();
void yyerror(const char *s) {}
%}
%token ID INVALID
%%
start: ID {
    printf("Valid identifier\\n");
};
%%
int main() {
    printf("Enter an identifier: ");
    if(yyparse()!=0)
        printf("Invalid identifier\\n");
    return 0;
}
```

### Program 5 – Loop Statement Validation

**q5.l**
```lex
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
[ \\t\\n]+    ;
.           { return INVALID; }
%%
int yywrap() { return 1; }
```

**q5.y**
```yacc
%{
#include <stdio.h>
int yylex();
void yyerror(const char *s) {
    printf("Invalid loop statement\\n");
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
assignment: ID '=' NUM ;
condition: ID REL NUM ;
%%
int main() {
    printf("Enter a simple for/while loop header: ");
    if(yyparse()==0)
        printf("Valid loop structure\\n");
    return 0;
}
```

### Program 6 – Arithmetic Calculator

**q6.l**
```lex
%{
#include "y.tab.h"
#include <stdlib.h>
%}
%%
[0-9]+ {
    yylval = atoi(yytext);
    return NUM;
}
[+\\-*/()] { return yytext[0]; }
[ \\t\\n]+ ;
. { return yytext[0]; }
%%
int yywrap() { return 1; }
```

**q6.y**
```yacc
%{
#include <stdio.h>
int yylex();
void yyerror(const char *s) {
    printf("Invalid expression\\n");
}
%}
%token NUM
%left '+' '-'
%left '*' '/'
%%
input: expr {
    printf("Result = %d\\n", $1);
};
expr:
      NUM { $$ = $1; }
    | expr '+' expr { $$ = $1 + $3; }
    | expr '-' expr { $$ = $1 - $3; }
    | expr '*' expr { $$ = $1 * $3; }
    | expr '/' expr {
        if($3==0) {
            yyerror("Division by zero");
            YYERROR;
        }
        $$ = $1 / $3;
      }
    | '(' expr ')' { $$ = $2; }
    ;
%%
int main() {
    printf("Enter expression: ");
    return yyparse();
}
```

### Program 7 – Three Address Code Generation (q7.c)

```c
#include <stdio.h>
int main() {
    int a,b,c;
    int t1,t2,x;
    printf("Enter values of a, b and c: ");
    scanf("%d%d%d",&a,&b,&c);
    t1=b*c;
    t2=a+t1;
    x=t2;
    printf("\\nThree Address Code:\\n");
    printf("t1 = b * c\\n");
    printf("t2 = a + t1\\n");
    printf("x = t2\\n");
    printf("\\nValues:\\n");
    printf("t1 = %d\\n",t1);
    printf("t2 = %d\\n",t2);
    printf("x = %d\\n",x);
    return 0;
}
```

### Program 8 – Declaration and Assignment Checking

**q8.l**
```lex
%{
#include "y.tab.h"
%}
%%
"int"       { return INT; }
"float"     { return FLOAT; }
[A-Za-z][A-Za-z0-9_]* { return ID; }
[0-9]+(\\.[0-9]+)? { return NUM; }
"="         { return '='; }
";"         { return ';'; }
[ \\t\\n]+ ;
. { return INVALID; }
%%
int yywrap() { return 1; }
```

**q8.y**
```yacc
%{
#include <stdio.h>
int yylex();
void yyerror(const char *s) {
    printf("Invalid statement or type syntax\\n");
}
%}
%token INT FLOAT ID NUM INVALID
%%
start: declaration | assignment ;
declaration:
      INT ID ';' {
          printf("Valid integer declaration\\n");
      }
    | FLOAT ID ';' {
          printf("Valid float declaration\\n");
      }
    ;
assignment:
    ID '=' NUM ';' {
        printf("Valid simple assignment\\n");
    }
    ;
%%
int main() {
    printf("Enter one declaration or assignment: ");
    return yyparse();
}
```

### Program 9 – Code Optimization (q9.c)

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

int is_number(const char *s) {
    int i=0;
    if(s[0]=='-' || s[0]=='+') i++;
    if(s[i]=='\\0') return 0;
    for(;s[i];i++) {
        if(!isdigit((unsigned char)s[i])) return 0;
    }
    return 1;
}

int main() {
    char result[30],left[30],op,right[30];
    int a,b;
    printf("Enter statement (example: x = 10 + 20): ");
    if(scanf("%29s = %29s %c %29s",
        result,left,&op,right)!=4) {
        printf("Input format is not correct.\\n");
        return 1;
    }
    printf("\\nOriginal statement: %s = %s %c %s\\n",
        result,left,op,right);
    if(is_number(left) && is_number(right)) {
        a=atoi(left);
        b=atoi(right);
        switch(op) {
            case '+': printf("Optimized: %s = %d\\n",result,a+b); break;
            case '-': printf("Optimized: %s = %d\\n",result,a-b); break;
            case '*': printf("Optimized: %s = %d\\n",result,a*b); break;
            case '/':
                if(b==0) {
                    printf("Cannot divide by zero.\\n");
                    return 1;
                }
                printf("Optimized: %s = %d\\n",result,a/b);
                break;
            default: printf("Operator not supported.\\n");
        }
    }
    else if(op=='+' && strcmp(right,"0")==0)
        printf("Optimized: %s = %s\\n",result,left);
    else if(op=='*' && strcmp(right,"1")==0)
        printf("Optimized: %s = %s\\n",result,left);
    else if(op=='*' && strcmp(right,"0")==0)
        printf("Optimized: %s = 0\\n",result);
    else if(op=='-' && strcmp(right,"0")==0)
        printf("Optimized: %s = %s\\n",result,left);
    else if(op=='*' && strcmp(right,"2")==0)
        printf("Strength reduction: %s = %s + %s\\n",
            result,left,left);
    else
        printf("No simple optimization applied.\\n");
    return 0;
}
```

### Program 10 – Target Code Generation (q10.c)

```c
#include <stdio.h>
#include <string.h>

int main() {
    char temp[30],left[30],op,right[30];
    printf("Enter TAC (example: t1 = a + b): ");
    if(scanf("%29s = %29s %c %29s",
        temp,left,&op,right)!=4) {
        printf("Invalid input format.\\n");
        return 1;
    }
    printf("\\nGenerated 8086-style code:\\n");
    printf("MOV AX, %s\\n",left);
    switch(op) {
        case '+': printf("ADD AX, %s\\n",right); break;
        case '-': printf("SUB AX, %s\\n",right); break;
        case '*': printf("MUL %s\\n",right); break;
        case '/': printf("DIV %s\\n",right); break;
        default:
            printf("Unsupported operator\\n");
            return 1;
    }
    printf("MOV %s, AX\\n",temp);
    printf("\\nNote: This is illustrative assembly-like output.\\n");
    return 0;
}
```

## PART 2 – HOW TO SAVE AND RUN

Install:
```bash
sudo apt update
sudo apt install flex bison gcc
mkdir compiler
cd compiler
```

Save each program using nano, for example:
```bash
nano q1.l
```

LEX programs 1 and 2:
```bash
flex q1.l
gcc lex.yy.c -o q1 -lfl
./q1

flex q2.l
gcc lex.yy.c -o q2 -lfl
./q2
```

LEX and YACC programs 3, 4, 5, 6 and 8:
```bash
bison -dy q3.y
flex q3.l
gcc y.tab.c lex.yy.c -o q3 -lfl
./q3
```
Replace q3 with q4, q5, q6 or q8 for the other programs. Run Bison first.

Normal C programs 7, 9 and 10:
```bash
gcc q7.c -o q7
./q7
gcc q9.c -o q9
./q9
gcc q10.c -o q10
./q10
```

## PART 3 – COMMON ALGORITHM

1. Start the program.
2. Read and understand the given question.
3. Identify the purpose: token identification, identifier validation, expression evaluation, loop validation, code optimization or code generation.
4. Identify input elements such as keywords, identifiers, constants, operators and special symbols.
5. Divide the work into lexical and syntax analysis wherever necessary.
6. Create a LEX file with the .l extension.
7. Include required header files.
8. Write regular expressions to recognize tokens.
9. Define rules for keywords, identifiers, numbers, operators and special symbols.
10. Write actions to print, store or return recognized tokens.
11. Ignore spaces, tabs and newline characters.
12. For token identification, call yylex().
13. For syntax validation, create a YACC file with the .y extension.
14. Include header files and declare tokens.
15. Write grammar production rules.
16. Add semantic actions for calculations or code generation.
17. Define yyerror() to display errors.
18. Call yyparse() to start parsing.
19. Save all files with correct filenames.
20. Generate the YACC parser and header file first.
21. Generate the lexical analyzer using Flex.
22. Compile generated C files using GCC.
23. Run the executable.
24. Enter input and check output.
25. Test valid and invalid inputs.
26. Correct errors in filenames, tokens, grammar or commands.
27. Record successful input and output.
28. Stop the program.

## PART 4 – 40 VIVA QUESTIONS AND ANSWERS

1. **What is a compiler?**  
A compiler converts a high-level language program into machine code or target code.

2. **What are the phases of a compiler?**  
Lexical analysis, syntax analysis, semantic analysis, intermediate code generation, code optimization and code generation.

3. **What is LEX?**  
LEX is a tool used to generate a lexical analyzer.

4. **What is YACC?**  
YACC is a tool used to generate a parser.

5. **What is a token?**  
A basic unit of a program, such as a keyword, identifier or operator.

6. **What is a lexeme?**  
The actual text that matches a token.

7. **What is lexical analysis?**  
The first compiler phase; it converts input characters into tokens.

8. **What is a symbol table?**  
It stores information about identifiers used in a program.

9. **What is an identifier?**  
A name given to a variable, function or other program element.

10. **What is a keyword?**  
A reserved word with a special meaning in a programming language.

11. **What is a regular expression?**  
A pattern used to recognize a group of characters.

12. **What is a parser?**  
A parser checks whether input follows grammar rules.

13. **What is grammar?**  
A collection of rules that defines the structure of a language.

14. **What is a syntax error?**  
An error that occurs when input does not follow grammar rules.

15. **What is operator precedence?**  
It determines which operator is evaluated first. Multiplication is performed before addition.

16. **What is associativity?**  
It determines evaluation order when operators have the same precedence.

17. **What is a semantic action?**  
A piece of C code written inside a YACC grammar rule.

18. **What is yyparse()?**  
The function used to start parsing in YACC.

19. **What is yylex()?**  
The function used to identify tokens in LEX.

20. **What is yytext?**  
It stores the text matched by the current LEX rule.

21. **What is an arithmetic calculator?**  
A program that performs addition, subtraction, multiplication and division.

22. **What is a loop?**  
A statement used to execute a group of statements repeatedly.

23. **What is type checking?**  
It verifies whether data types used in a statement are compatible.

24. **What is type conversion?**  
Changing one data type into another.

25. **What is intermediate code?**  
A representation generated between source code and target code.

26. **What is three-address code?**  
Intermediate code in which an instruction contains at most three addresses or operands.

27. **Give an example of three-address code.**  
t1 = a + b

28. **Why are temporary variables used?**  
They store intermediate results during expression evaluation.

29. **What is code optimization?**  
Improving program efficiency without changing its output.

30. **What is constant folding?**  
Evaluating constant expressions during compilation. Example: x = 10 + 20 becomes x = 30.

31. **What is strength reduction?**  
Replacing a costly operation with a simpler operation. Example: a * 2 can be simplified to a + a.

32. **What is algebraic simplification?**  
Simplifying expressions using mathematical rules, such as a + 0 = a, a * 1 = a and a * 0 = 0.

33. **What is code generation?**  
Converting intermediate code into target-machine instructions.

34. **What is 8086?**  
8086 is a 16-bit microprocessor.

35. **What is AX?**  
AX is the accumulator register in the 8086 processor.

36. **What is MOV?**  
An instruction used to copy data from one location to another.

37. **What is ADD?**  
An instruction used to perform addition.

38. **What is SUB?**  
An instruction used to perform subtraction.

39. **What is the difference between LEX and YACC?**  
LEX identifies tokens. YACC checks syntax and grammar.

40. **Why should YACC be executed before LEX?**  
YACC generates the y.tab.h header file, which is included in the LEX program.
