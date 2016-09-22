# Quiz Part 2: Algorithms

In this folder, create a JavaScript file containing a function that parses a string containing a series of characters, some of which may be brackets, braces, or parentheses (`[`, `]`, `{`, `}`, `(`, `)`), some of which may be any other character, and determines whether or not the braces match properly according to the following rules:

- An open bracket must precede its paired closing bracket
-- valid: `()`
-- invalid: `)(`
- An open bracket must be closed with a bracket of the same type
-- valid: `()`
-- invalid: `(]`

Some more valid/invalid cases:

- valid: `(([]{}))[]()`
- invalid: `(([]{)})[]()`
- valid: `()()()()()`
- invalid: `(()()()()`
- valid: `(((((())))))`
- invalid: `((((()))`

Write test cases for your function that validates its behavior.