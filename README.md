Compiler Design Practical Programs

This repository contains 10 Compiler Design practical programs using LEX, YACC, and C, along with instructions to compile and execute them, a common algorithm, and viva questions with answers.

Programs:
1. Lexical Analyzer with Symbol Table (q1.l)
2. Token Identification Using LEX (q2.l)
3. Arithmetic Expression Validation Using LEX and YACC (q3.l, q3.y)
4. Valid Identifier Recognition Using LEX and YACC (q4.l, q4.y)
5. Simple Loop Statement Validation Using LEX and YACC (q5.l, q5.y)
6. Arithmetic Calculator Using LEX and YACC (q6.l, q6.y)
7. Three Address Code Generation Using C (q7.c)
8. Basic Declaration and Assignment Checking Using LEX and YACC (q8.l, q8.y)
9. Code Optimization Using C (q9.c)
10. Target Code Generation Using C (q10.c)

Installation:
sudo apt update
sudo apt install flex bison gcc

Create folder:
mkdir compiler
cd compiler

LEX-only programs:
flex q1.l
gcc lex.yy.c -o q1 -lfl
./q1

flex q2.l
gcc lex.yy.c -o q2 -lfl
./q2

LEX and YACC programs:
bison -dy q3.y
flex q3.l
gcc y.tab.c lex.yy.c -o q3 -lfl
./q3

For other programs, replace q3 with q4, q5, q6 or q8.

C programs:
gcc q7.c -o q7
./q7

gcc q9.c -o q9
./q9

gcc q10.c -o q10
./q10

COMMON ALGORITHM:
1. Start the program.
2. Read and understand the given question.
3. Identify the purpose of the program.
4. Identify the required input elements.
5. Divide the work into lexical and syntax analysis wherever necessary.
6. Create a LEX file using the .l extension.
7. Include required header files.
8. Write regular expressions to recognize tokens.
9. Define rules for keywords, identifiers, numbers, operators and special symbols.
10. Write actions for each rule.
11. Ignore unnecessary spaces, tabs and newlines.
12. For token identification, call yylex().
13. For syntax validation, create a YACC file using the .y extension.
14. Declare the required tokens.
15. Write grammar production rules.
16. Add semantic actions when required.
17. Define yyerror() to display errors.
18. Call yyparse() to start syntax analysis.
19. Save all files with correct filenames.
20. Generate the YACC parser and header file first.
21. Generate the lexical analyzer using LEX or Flex.
22. Compile using GCC.
23. Run the executable file.
24. Enter input and check output.
25. Test valid and invalid inputs.
26. Check filenames, tokens, grammar and commands if errors occur.
27. Record input and output in the practical record.
28. Stop the program.

VIVA QUESTIONS AND ANSWERS:

1. What is a compiler?
A compiler converts a high-level language program into machine code or target code.

2. What are the phases of a compiler?
Lexical analysis, syntax analysis, semantic analysis, intermediate code generation, code optimization and code generation.

3. What is LEX?
LEX is a tool used to generate a lexical analyzer.

4. What is YACC?
YACC is a tool used to generate a parser.

5. What is a token?
A token is a basic unit of a program, such as a keyword, identifier or operator.

6. What is a lexeme?
A lexeme is the actual text that matches a token.

7. What is lexical analysis?
It is the first phase of a compiler and converts input characters into tokens.

8. What is a symbol table?
It stores information about identifiers used in a program.

9. What is an identifier?
An identifier is a name given to a variable, function or other program element.

10. What is a keyword?
A keyword is a reserved word with a special meaning in a programming language.

11. What is a regular expression?
A pattern used to recognize a group of characters.

12. What is a parser?
A parser checks whether input follows grammar rules.

13. What is grammar?
Grammar is a collection of rules used to define the structure of a language.

14. What is a syntax error?
An error that occurs when input does not follow grammar rules.

15. What is operator precedence?
It determines which operator is evaluated first. Multiplication is performed before addition.

16. What is associativity?
It determines the order of evaluation when operators have the same precedence.

17. What is a semantic action?
A piece of C code written inside a YACC grammar rule.

18. What is yyparse()?
The function used to start parsing in YACC.

19. What is yylex()?
The function used to identify tokens in LEX.

20. What is yytext?
yytext stores the text matched by the current LEX rule.

21. What is an arithmetic calculator?
A program that performs addition, subtraction, multiplication and division.

22. What is a loop?
A loop executes a group of statements repeatedly.

23. What is type checking?
It verifies whether data types used in a statement are compatible.

24. What is type conversion?
It means changing one data type into another.

25. What is intermediate code?
A representation generated between source code and target code.

26. What is three-address code?
An intermediate code in which an instruction contains at most three addresses or operands.

27. Give an example of three-address code.
t1 = a + b

28. Why are temporary variables used?
They store intermediate results during expression evaluation.

29. What is code optimization?
It improves program efficiency without changing its output.

30. What is constant folding?
It evaluates constant expressions during compilation. Example: x = 10 + 20 becomes x = 30.

31. What is strength reduction?
It replaces a costly operation with a simpler operation. Example: a * 2 can be simplified to a + a.

32. What is algebraic simplification?
It simplifies expressions using mathematical rules, such as a + 0 = a, a * 1 = a and a * 0 = 0.

33. What is code generation?
It converts intermediate code into target-machine instructions.

34. What is 8086?
8086 is a 16-bit microprocessor.

35. What is AX?
AX is the accumulator register in the 8086 processor.

36. What is MOV?
MOV copies data from one location to another.

37. What is ADD?
ADD performs addition.

38. What is SUB?
SUB performs subtraction.

39. What is the difference between LEX and YACC?
LEX identifies tokens. YACC checks syntax and grammar.

40. Why should YACC be executed before LEX?
YACC generates the y.tab.h header file, which is included in the LEX program.
