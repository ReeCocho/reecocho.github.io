---
layout : post
title : "Math Parser"
date : 2018-8-27 17:45:00
categories : "Programming"
---

Math Parser is a small little mathematical expression parser I wrote in C to test my language skills. It uses no external libraries other than the standard C library. The parser works in two stages: lexical analysis and parsing. The lexical analysis stage reads the user's input and breaks it up into a set of tokens. These tokens contain a type (Number, addition, subtraction...) and in the case of a number, a string containing their value. These tokens are fed into the parser. The parser takes the tokens and converts them into [reverse polish notation](https://en.wikipedia.org/wiki/Polish_notation) using the [shunting yard algorithm](https://en.wikipedia.org/wiki/Shunting-yard_algorithm). These new tokens are fed into the final parser which evaluates the expressions and spits out the results.

As of now, Math Parser only supports a limited number of operations. I plan on expanding it to include other common operations, functions, and variables. Math Parser is my first complete C program, so please be gentle when reading it!

[GitHub Link](https://github.com/ReeCocho/Math-Parser)