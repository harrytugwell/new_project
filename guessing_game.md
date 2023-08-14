# Q3a Number guessing game 

---
## Background

In this question, you are asked to implement a number guessing game. The game you are required to implement is played as follows:

* (i) One integer `n` from {1, 2, ..., `m`} is chosen as random
* (ii) The player needs to guess what `n` is. Once the player makes a guess (say integer `x`), the player will be informed whether `x` is too high, too low, or correct
* (iii) If the guess is not correct (i.e. `x` is too high or too low), the player makes another guess (i.e. back to (ii))
* (iv) One round of the game is finished when `n` is correctly guessed

Here we consider 3 different types of players with 3 different strategies for this part:
* `Player`: choose `x` randomly from the possible range of integers
* `MiddlePlayer`: choose `x` as the middle integer from the possible range of integers
* `QuartilePlayer`: choose `x` as the integer closet to the _first quartile_ from the possible range of integers

You will need to implement the game, and also 2 different types players `MiddlePlayer` and `QuartilePlayer` (note `Player` is already implemented for you). You will then retrieve and compare the number of guesses of each type of player when `m` changes.

---

## Instructions

0. (no submission needed) Please read the code and understand how it works. Please do not make changes to the class definition of `Player`

1. Write the class definitions for the classes `MiddlePlayer` and `QuartilePlayer`. All three classes (including `Player`) require the information of `m` to initialise, and have the following three methods:
  * `guess()`: guess the unknown integer based on the corresponding strategy. The method does not take any argument and it returns the guess (type: `int`, possible values: 1, ..., `m`)
  * `outcome()`: get the outcome of the guess. The method takes a string (`'too high'`, `'too low'` or `'correct'`) as an argument and it has no return value (or return `None`)
  * `record()`: provide the number of guesses made for the last `n` games played. The method takes 1 argument `n` (type: `int` > 0) and returns a `list` of non-negative integers

  Below we have some examples to help you understand the required behaviour of `MiddlePlayer` and `QuartilePlayer`. Note here only part of the game (players) are implemented, and therefore other parts of the game (e.g. to find out if the guess from the player is too high, too low or correct) are done manually in the examples below.

  Example behaviour of an instance of `MiddlePlayer`:
  ```
  player_1 = MiddlePlayer(1000) # m is 1000 for this example
  >>> player_1.guess() # select the middle integer from 1 to 1000, depending on your implementation, it can be 500 or 501
  500
  >>> player_1.outcome('too high')
  >>> player_1.guess() # select the middle integer from 1 to 499
  250
  >>> player_1.outcome('correct') # game finished
  >>> player_1.record(1)
  [2]
  >>> player_1.guess()# select the middle integer from 1 to 1000, depending on your implementation, it can be 500 or 501
  500
  >>> player_1.outcome('too low')
  >>> player_1.guess() # select the smallest integer from 501 to 1000, depending on your implementation, it can be 750 or 751
  750
  >>> player_1.outcome('correct') # game finished
  ```

  Example behaviour of an instance of `QuartilePlayer`:
  ```
  player_2 = QuartilePlayer(100) # 100 here is the maximum for the range of integer to guess
  >>> player_2.guess() # select the integer that is closest to the first quartile from 1 to 100, i.e. 26 (25 will be accepted as well)
  26
  >>> player_2.outcome('too high')
  >>> player_2.guess() # select the integer that is closest to the first quartile from 1 to 25, i.e. 7
  7
  >>> player_2.outcome('too high')
  >>> player_2.guess() # select the integer that is closest to the first quartile from 1 to 7, i.e. 3 (or 2)
  3
  >>> player_2.outcome('correct') # game finished
  >>> player_2.record(1)
  [3]
  >>> player_1.guess() # select the integer that is closest to the first quartile from 1 to 100, i.e. 26 (25 will be accepted as well)
  26
  >>> player_2.outcome('too low')
  >>> player_1.guess() # select the integer that is closest to the first quartile from 27 to 100, i.e. 45 (46 will be accepted as well)
  45
  >>> player_1.outcome('correct') # game finished
  >>> player_1.record(2)
  [3, 2]
  ```

2. write code to implement the game logic. The idea of what the code should do with a given player `player` in one round:
  * (i) Choose `n` randomly from in {1, 2, ..., `m`}
  * (ii) Call the `guess()` method of `player`
  * (iii) Check the return value from (ii) and call the `outcome()` method in `player` with the corresponding argument. For example, if the value from (ii) is smaller than `n` in (i), the argument is `'too low'`
  * (iv) Repeat (ii) and (iii) until `n` is guessed correctly

  For this part, please write the code in the form of a class or a function so that you can use it in part 3

3. simulate the games with three players and find out the average number of guesses each type of players need per round with `m = 10, 100, 500, 1000, 10000`, and comment on the performance by doing the following:
  * (i) For each `m`:
    * (step 1) Create an instance of each type of player using the classes defined in part 1 for this specific `m`. There should be 3 players
    * (step 2) Make use of the code written in part 2 and let each player plays the game for 1000 rounds
    * (step 3) Retrieve the record for all the games played by each player, and calculate the average number of guesses each type of players made in those 1000 rounds
  * (ii) For each player, draw a line plot with `m` on the x axis and the average number of guesses from (i) (step 3) on the y axis
  * (iii) Based on (ii), comment on how the performance of each type of players changes when `m` increases. How is the rate of increase in number of guesses different with different types of players?

---


---

## Some more coding details and additional requirements

* You can use `random` module for the whole question, and you are allowed to reference the official documents about the module
* For part 3(ii), You can use any functionality from some plotting libraries like `matplotlib` and `seaborn`, or the plotting functionality from `pandas`. You can also google as much as you want for this part of the question (e.g. find out how to create line plots). _However_, if you are referencing other people's code / answer / logic, please state it in your report
* Please make sure you demonstrate the OOP concepts in this task


---
