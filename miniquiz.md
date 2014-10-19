## Parenthesis

You are tasked with determining whether a string of parenthesis is correctly balanced.  Write a function that takes as input a single string that may be any contain any ASCII character and determines at that moment whether or not the associated parenthesis (`[`, `]`, `(`, `)`,`{`,`}`) are correctly closed.  Return true or false accordingly.

__All open parenthesis should be closed in the same order that they were opened__

Ex:

check_parens('(a(b))()()') #=> True
check_parens('a(b())') #=> True
check_parens(')a(b))') #=> False
check_parens('(a(b){c}') #=> False