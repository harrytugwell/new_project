# Q1: Variable name via functional programming 

---

## Description

In this question, you are asked to check if a given string can be used to represent a valid variable name in Python _and_ follow the naming convention of this course, but this time it is solved via the functional programming approach, with the use of `map()` and `reduce()` (from `functools`).

---

## Background information

Recall that a valid variable name in Python needs to be non-empty and satisfy the following rules:
1. It must be composed of letters (`a-zA-Z`), digits (`0-9`) and/or underscore `_` (see Note)
2. It cannot start with a digit
3. It must not be one of the Python keywords: `['False', 'None', 'True', 'and', 'as', 'assert', 'async', 'await', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']`

Rules:

4. No mixing case (letters are either all lowercase for variables that are suppose to vary, or all uppercase for variables that are suppose to be constant)
5. Meaningful names

While it is difficult to check rule 5 ("meaningful" is situational wise), it is possible to check other rules by a program.

---

## Instructions

Do the following:

1.  write the function definition for the function `is_valid_variable_name()`, for which it takes 1 argument: a variable name to check (type: `str`, length > 0). It returns `bool` for which it represents whether the given string satisfies the first 4 rules listed above. 
    * Unlike PS4, this time you _must_ solve the problem in functional programming way with the use of functions `map()` and `reduce()` (from `functools`). Please see `Note` for more details about the tools you can use

2.  demonstrate the use of the function

---

## Example use

Example use of the required function `is_valid_variable_name()`:
```
>>> is_valid_variable_name('num student')
False
>>> is_valid_variable_name('2x')
False
>>> is_valid_variable_name('if')
False
>>> is_valid_variable_name('numStudent')
False
>>> is_valid_variable_name('_')
True
```

---



## Note

* Note that Python actually accepts some other characters like `Γ`, but we do not accept them in this course (so as this question)
* Only the following built-in functions / functions from the Python standard library can be used:
  * built-in function `ord()`
  * built-in function `len()`
  * built-in function `map()`
  * `reduce()` from `functools`
* You MUST use `map()` and `reduce()` (from `functools`) to solve the question
* No loops (`for` loop and `while` loop) can be used
* Any function written must be a pure function
* No change in variable value or modification of objects

---

## Function argument assumptions

Assume the argument provided is always valid, i.e. for `is_valid_variable_name()`, the argument provided is always a `str` with non-zero length.
