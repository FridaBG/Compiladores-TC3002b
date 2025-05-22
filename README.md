# Compiladores TC3002B

# Development of advanced computer science applications (TC3002B)

## Proceso

- Se trabajó en *pair programming* por **Daniel Felipe Hurtado** y **Frida Bailleres González**.

- Se completaron las siguientes funciones, basándonos en las definiciones especificadas más adelante en este mismo README:

    ```python
    def extendedAdditiveExpression(self):
    def additiveExpression(self):
    def extendedRelationalExpression(self):
    def relationalExpression(self):
    def extendedEqualityExpression(self):
    def equalityExpression(self):
    def extendedConditionalTerm(self):
    def conditionalTerm(self):
    def extendedConditionalExpression(self):
    def conditionalExpression(self):
    def expression(self):
    def setXYStatement(self):
    def setXStatement(self):
    def setYStatement(self):
    def leftStatement(self):
    def rightStatement(self):
    def backwardStatement(self):
    def forwardStatement(self):
    def movementStatement(self):
    ```

- Durante el desarrollo hubo errores al compilar y fallos en los **tests 6 y 7 del bloque GOOD**, por lo que hicimos las siguientes correcciones:

    - **Ajustamos la función `clearStatement`** en el parser, ya que según la gramática:

        ```
        <clear-statement> ::= (CLEAR | CLS) '(' ')'
        ```

        …`CLEAR` debe ir obligatoriamente acompañado de paréntesis. La versión anterior no los validaba correctamente. El cambio aplicado fue:

        ```python
        def clearStatement(self):
            if self.token.tag == Tag.CLEAR:
                self.check(Tag.CLEAR)
                self.check(ord("("))
                self.check(ord(")"))
            else:
                self.error("expected a CLEAR statement before " + str(self.token))
        ```
    - Agregamos la palabra reservada `CIRCLE` al diccionario del lexer:

        ```python
        self.words["CIRCLE"] = Token(Tag.CIRCLE, "CIRCLE")
        ```

    - Quitamos los paréntesis al comando `HOME` del **programa 7** de GOOD, ya que según la gramática:

        ```
        <movement-statement> ::=
            <forward-statement> |
            <backward-statement> |
            <right-statement> |
            <left-statement> |
            <setx-statement> |
            <sety-statement> |
            <setxy-statement> |
            HOME
        ```

        …`HOME` debe aparecer solo, sin `()`. Por eso, en lugar de cambiar la gramática, decidimos quitar los paréntesis del test.  
Otra opción hubiera sido modificar la gramática para permitir también `HOME()`:


- Los tests **#2 y #5 del bloque BAD** aparecen como *"shouldn’t pass"*, pero actualmente **sí pasan**, porque:
	- El parser es solo sintáctico. No hace validación semántica de variables, por lo que permite usar identificadores que no han sido declarados con `VAR`.
 	- Para solucionarlo, se tendría que agregar una **tabla de símbolos** que rastree qué variables han sido declaradas y marcar error si se usan otras.


## Definition of a Logo dialect

```
<program> ::=
	<declaration-sequence>
	<statement-sequence>

<declaration-statement> ::=
  VAR <identifier> <identifier-list>

<identifier-list> ::= ',' <identifier> <identifier-list>
<identifier-list> ::= ' '

<statement-sequence> ::= <statement> <statement-sequence>
<statement-sequence> ::= ' '

<statement> ::=
  <simple-statement> |
  <structured-statement>

<simple-statement> ::=
  <assignment-statement> |
  <movement-statement> |
  <drawing-statement> |
  <text-statement>

<assigment-statement> ::= <identifier> ':''=' <expression>

<movement-statement> ::=
      <forward-statement> |
      <backward-statement> |
      <right-statement> |
      <left-statement> |
      <setx-statement> |
      <sety-statement> |
      <setxy-statement> |
      HOME

<forward-statement> ::=  (FORWARD | FD) '(' <expression> ')'

<backward-statement> ::= (BACKWARD | BK) '(' <expression> ')'

<right-statement> ::= (RIGHT | RT) '(' <expression> ')'

<left-statement> ::= (LEFT | LT) '(' <expression> ')'

<setx-statement> ::= SETX '(' <expression> ')'

<sety-statement> ::= SETY '(' <expression> ')'

<setxy-statement> ::= SETXY '(' <expression> ',' <expression> ')'

<drawing-statement> :=
  <clear-statement> |
  <circle-statement> |
  <arc-statement> |
  <penup-staement> |
  <pendown-statement> |
  <color-statement> |
  <penwidth-statement>

<clear-statement> ::= (CLEAR | CLS) '(' ')'

<circle-statement> ::= CIRCLE '(' <expression> ')'

<arc-statement> ::= ARC '(' <expression> ',' <expression> ')'

<penup-statement> ::= (PENUP | PU) '(' ')'

<pendown-statement> ::= (PENDOWN | PD) '(' ')'

<color-statement> ::= COLOR '(' <expression> ',' <expression> ',' <expression> ')'

<penwidth-statement> ::= PENWIDTH '(' <expression> ')'

<text-statement> ::= PRINT '(' <element> <element-list> ')'

element := <string> | <expression>

<element-list> := ',' <element> <element-list>
<element-list> := ' '

<structured-statement> ::=
  <repetitive-statement> |
  <conditional-statement>

<repetitive-statement> ::=
  WHILE '(' <expression> ')'  '[' <statement-sequence> ']'

conditional-statement ::=
  <if-statement> |
  <if-else-statement>

<if-statement> ::=
  IF '(' <expression> ')' '[' <statement-sequence> ']'

<if-else-statement> ::=
  IFELSE '(' <expression> ')' '[' <statement-sequence> ']' '[' <statement-sequence> ']'

<expression> ::= <conditional-expression>

<conditional-expression> ::=
	<conditional-term> <extended-conditional-expression>

<extended-conditional-expression> ::=
	OR <conditional-term> <extended-conditional-expression>
<extended-conditional-expression> ::= ' '

<conditional-term> ::=
	<equality-expression> <extended-conditional-term>

<extended-conditional-term> ::=
	AND <equality-expression> <extended-conditional-term>
<extended-boolean-term> ::= ' '

<equality-expression> ::=
	<relational-expression> <extended-equality-expression>

<extended-equality-expression> :=
	'=' <relational-expression> <extended-equality-expression>
<extended-equality-expression> :=
	'<''>' <relational-expression> <extended-equality-expression>
<extended-equality-expression> ::= ' '

<relational-expression> ::=
	<additive-expression> <extended-relational-expression>

<extended-relational-expression> :=
	'<' <additive-expression> <extended-relational-expression>
<extended-relational-expression> ::=
	'<''=' <additive-expression> <extended-relational-expression>
<extended-relational-expression> :=
	'>' <additive-expression> <extended-relational-expression>
<extended-relational-expression> ::=
	'>''=' <additive-expression> <extended-relational-expression>
<extended-relational-expression> ::= ' '

<additive-expression> ::=
	<multiplicative-expression> <extended-additive-expression>

<extended-additive-expression> ::=
	'+'  <multiplicative-expression> <extended-additive-expression>
<extended-additive-expression> ::=
	'-'  <multiplicative-expression> <extended-additive-expression>
<extended-additive-expression> ::= ' '

<multiplicative-expression> ::=
	<unary-expression> <extended-multiplicative-expression>

<extended-multiplicative-expression> ::=
	'*' <unary-expression> <extended-multiplicative-expression>
<extended-multiplicative-expression> ::=
	'/' <unary-expression> <extended-multiplicative-expression>
<extended-multiplicative-expression> ::=
	MOD <unary-expression> <extended-multiplicative-expression>
<extended-multiplicative-expression> ::= ' '

<unary-expression> ::=
	'-' <unary-expression> ||
	'!' <unary-expression> ||
	<primary-expression>

<primary-expression> ::=
	<identifier> ||
	<number> ||
	<true>	||
	<false> ||
	'(' <expression> ')'

<identifier> ::= <letter> <characters>
<characters> ::= (<letter> | <digit>) <characters>
<characters> ::= ' '

<number> ::= <integer-number> | <real-number>
<integer-number> ::= <digit-sequence>
<digit-sequence> ::= <digit> <digits>
<digits> ::= <digit> <digits>
<digits> ::= ' '

<true> ::= '#''t'
<false> ::= '#''f'

<letter> ::= ['A'-'Z'] | ['a'-'z']
<digit> ::= ['0'-'9']
<string> ::= '"' <string-character> <more-string-chracters> '"'
<<more-string-chracters> ::= <string-character> <more-string-chracters>
<more-string-chracters> ::= ' '
<string-character> ::= <any-character-except-quote>
```
