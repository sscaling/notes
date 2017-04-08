# Goyacc

Goyacc overview, for generating lexer/parsers. Goyacc is a transliteration of yacc - so all the hooks are in Go rather than C.

Relies on a user supplied low-level input routine for lexing (Lexical Analyzer) the tokens from the input stream. The grammar defines
the order for the valid tokens.

## Grammar files (.y)

Grammar files define a BNF grammar. This is inter-weaved with some Go code and is passed through goyacc to generate a valid .go parser.

Based on the rules defined in the grammar, errors are detected in a fail-fast error (scanning is left-to-right) and some error-recovery
is allowed by skipping over bad data.

### Syntax / Specifications

A basic file consists of 3 parts:

	declarations / imports (headers)
        %%
	grammar rules
	%%
	programs (typically externalised in another file)

Comments can be defined with ```/* comment */``` syntax.

Variables / Additional code can be placed in the declarations section, i.e. ```{% var counter int = 0 %}```

#### Non-terminal symbols

Not defined in the declarations section and appear at-least once on the left-side of a rule

#### Terminal symbols / tokens

Represents a sequence of zero or more names and literals

#### Literals

Literals can be defined in grammar files, and are single-quotes containing a single character

	'\n'	// newline
	'\xxx'	// octal etc..

#### Tokens

In the declarations section:

	%token name1 name2 ....

#### Start symbol

The first / largest / most-general structure defined by the grammar. By default, the start symbol is taken to be the left-hand side of the first grammar rule.

Can be explicitly specified with:

	%start <name>

where <name> is the non-terminal to start parsing from.

#### Actions

Enclosed in braces '{' and '}' - follows the symbol. Can be any Go code (libraries must have import defined in declaration section).

To return a value, assign something to '$$', i.e. ```{ $$ = 1 }```

Values from the lexical-analyzer can be obtained by using $<position> notation, where <position> indidcates the 1-based index of the value in the
right-hand side of the grammar. ```A : B C D;``` we have '$1' == B, '$2' == C etc...

Default is ```{ $$ == $1 }```

Actions can occur mid-rule, i.e. ```A : B { $$ = 1 } C { x = $2 } ;```. It is assumed that the mid-rules return a value, and become accessible, e.g. $2 in the example.

#### Conflicts

A valid grammar file may have shift/reduce conflict or reduce/reduce conflict. The former results in a default shift action, where the later results in a reduce of the earlier grammar rule. reduce/reduce conflicts should be avoided.

Passing ```-v y.output``` argument to goyacc will produce a y.output file showing internal stack of parser. Useful for debugging. At the bottom there is information about tokens, conficts etc.

#### Precedence

Situations may arise where auto-conflict resolution is not sufficient - typically aritmetic expressions.

This can be specified with precidence operators in the declaration section, the order of the line defines the precedence - lowest first, highest last.

	%left '+' '-'
	%left '*' '/'

You can use ```%left```, ```%right``` or ```%nonassoc``` for defining if the rule is left, right or no association. no association would be something like less-than operator that doesn't associate with itself.

## Resources

* [http://dinosaur.compilertools.net/yacc/](http://dinosaur.compilertools.net/yacc/)
* [https://github.com/golang-samples/yacc/blob/master/simple/calc.y](https://github.com/golang-samples/yacc/blob/master/simple/calc.y)
* [https://thorstenball.com/blog/2017/01/04/a-virtual-brainfuck-machine-in-go/](https://thorstenball.com/blog/2017/01/04/a-virtual-brainfuck-machine-in-go/)
* [https://github.com/Overv/BFC](https://github.com/Overv/BFC)
