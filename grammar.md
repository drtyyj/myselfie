Copyright (c) the Selfie Project authors. All rights reserved. Please see the AUTHORS file for details. Use of this source code is governed by a BSD license that can be found in the LICENSE file.

Selfie is a project of the Computational Systems Group at the Department of Computer Sciences of the University of Salzburg in Austria. For further information and code please refer to:

http://selfie.cs.uni-salzburg.at

This is the grammar of the C Star (C\*) programming language.

C\* is a tiny subset of the programming language C. C\* features global variable declarations with optional initialization as well as procedures with parameters and local variables. C\* has five statements (assignment, while loop, if-then-else, procedure call, and return) and standard arithmetic (`+`, `-`, `*`, `/`, `%`) and comparison (`==`, `!=`, `<`, `<=`, `>`, `>=`) operators. C\* includes the unary `*` operator for dereferencing pointers hence the name but excludes data types other than `uint64_t` and `uint64_t*`, bitwise and Boolean operators, and many other features. The C\* grammar is LL(1) with 6 keywords and 22 symbols. Whitespace as well as single-line (`//`) and multi-line (`/*` to `*/`) comments are ignored.

C\* Keywords: `uint64_t`, `struct`, `void`, `if`, `else`, `while`, `for`, `return`

C\* Symbols: `integer_literal`, `character_literal`, `string_literal`, `identifier`, `array_identifier`, `struct`, `,`, `;`, `(`, `)`, `{`, `}`, `+`, `-`, `*`, `/`, `%`, `=`, `==`, `!=`, `<`, `<=`, `>`, `>=`, `...`, `<<`, `>>`, `&`, `|`, `~`

with:

```
integer_literal   = ( digit { digit } ) | ( "0" "x" hex_digit { hex_digit } ) .

character_literal = "'" printable_character "'" .

string_literal    = """ { printable_character } """ .

identifier        = letter { letter | digit | "_" } .

array_identifier  = letter { letter | digit | "_" } { "[" identifier | integer_literal | shift_expression "]" } .
```

and:

```
hex_digit 	= "0" | ... | "9" | "A" | ... | "F" .

digit  		= "0" | ... | "9" .

letter 		= "a" | ... | "z" | "A" | ... | "Z" .
```

C\* Grammar:

```
//grammar.md changes already implemented in previous assignment
cstar             = { type ( identifier | array_identifier )
                      [ "=" [ cast ] [ "-" ] ( integer_literal | character_literal ) ] ";" |
                    ( "void" | type ) identifier procedure } | struct_def |.

type              = "uint64_t" [ "*" ] | "struct".

cast              = "(" type ")" .

procedure         = "(" [ variable { "," variable } [ "," "..." ] ] ")" ( ";" |
                    "{" { variable ";" } { statement } "}" ) .

variable          = type ( identifier | array_identifier ) | "struct" identifier "*" identifier .

struct_type_def	  = "struct" identifier "{" { variable } "}" .

statement         = ( [ "*" ] identifier { "->" identifier } | array_identifier | "*" "(" expression ")" ) "=" expression ";" |
                    call ";" | while | if | return ";" .

call              = identifier "(" [ expression { "," expression } ] ")" .

expression 		  = and_expression { "|"  and_expression } .

and_expression	  = comp_expression  { "&" comp_expression } .

comp_expression   = shift_expression
                    [ ( "==" | "!=" | "<" | ">" | "<=" | ">=" ) shift_expression ] .
					
shift_expression  = simple_expression { ( "<<" | ">>" ) simple_expression } .

simple_expression = term { ( "+" | "-" ) term } .

term              = factor { ( "*" | "/" | "%" ) factor } .

factor            = [ cast ] [ "~" ] [ "-" ] [ "*" ]
                    ( integer_literal | character_literal | string_literal |
                      identifier { "->" identifier } | array_identifier | call | "(" expression ")" ) .
					  
for				  = "for" "(" identifier "=" integer_literal ";" comp_expression ";"  statement ")" "{"
				    { statement } "}"

while             = "while" "(" expression ")"
                      ( statement | "{" { statement } "}" ) .

if                = "if" "(" expression ")"
                      ( statement | "{" { statement } "}" )
                    [ "else"
                      ( statement | "{" { statement } "}" ) ] .

return            = "return" [ expression ] .
```