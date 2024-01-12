title: Simplify Game Logic Using a Payoff Matrix
description: As projects evolve, code complexity tends to follow suit. Traditional approaches, laden with multiple conditional statements and loops, can quickly become convoluted and challenging to maintain. This is where the elegance of nested arrays steps in.
tags: javascript

**Rock, Paper, Scissors is a game of imperfect information.**

When making a move, players act without any information about the opponent's move. This characteristic allows us to make a pretty decent approximation of a human player with very little code. We can give the computer a random choice of move and that's enough to make it competitive.

**If we were to give the computer player a predetermined strategy it would open the door to exploitation. Players could discern patterns and use them to their advantage in subsequent games.**

### The Challenge of Complex Logic

Using this random strategy, once we have our player's chosen move, and our random move, the only thing left is to write the logic to determine the outcome of the game based on these two moves.

A portion of this logic could be written in pseudo code as such: 

>If player one chooses rock and player 2 chooses scissors, player one wins and player two loses.

If we extrapolate that out to all nine possible outcomes, we might end up with something like this:

```js
function playRound(playerMove, computerMove) {
  if (playerMove === 'Rock') {
    if (computerMove === 'Rock') {
      return 'Draw';
    } else if (computerMove === 'Paper') {
      return 'You Lose: Paper beats Rock.';
    } else if (computerMove === 'Scissors') {
      return 'You Win: Rock beats Scissors.';
    }
  } else if (playerMove === 'Paper') {
    if (computerMove === 'Rock') {
      return 'You Win: Paper beats Rock.';
    } else if (computerMove === 'Paper') {
      return 'Draw';
    } else if (computerMove === 'Scissors') {
      return 'You Lose: Scissors beats Paper.';
    }
  } else if (playerMove === 'Scissors') {
    if (computerMove === 'Rock') {
      return 'You Lose: Rock beats Scissors.';
    } else if (computerMove === 'Paper') {
      return 'You Win: Scissors beats Paper.';
    } else if (computerMove === 'Scissors') {
      return 'Draw';
    }
  } 
}
```

Pretty ugly right? Logic like this can quickly become convoluted and difficult to maintain.

Fortunately, we have better tools at our disposal. Given that Rock, Paper, Scissors is a zero sum game, each of the possible outcomes for a player can be expressed using a payoff matrix to represent the gains and losses associated with each player's strategy:

![Payoff Matrix](https://github.com/columk1/assets/blob/main/blog/payoff-matrix-m.png?raw=true)
   
The rows represent player 1's possible strategies while the columns represent player 2's possible strategies. **1 represents a win, 0 a tie, and -1 a loss. So, for instance, the upper left entry is a 0 because if both players choose rock, they tie.***

And here is how we represent it in javascript using a two dimensional array:

```js
[
  [0, -1, 1],
  [1, 0, -1],
  [-1, 1, 0]
]
```

And then the whole game would look something like this:
  
```js
const moves = [0, 1, 2] // Rock, Paper, Scissors

const outcomes = [
  [0, -1, 1],
  [1, 0, -1],
  [-1, 1, 0]
]

function playRound(playerMove) {
  const computerMove = Math.floor(Math.random() * 3)
  const result = outcomes[playerMove][computerMove]
  return result === -1 ? "Lose" : result ? 'Win!' : 'Draw'
}

playRound(0) // Win!
```

The matrix allows us to condense the result logic into a couple of lines. If we want to add some more color to the return value to improve the user experience, we can replace the move integers with strings. Using a switch statement makes it a bit nicer to read:
```js
const moves = ['Rock', 'Paper', 'Scissors']

function playRound(playerMove) {
  const computerMove = moves[Math.floor(Math.random() * 3)]
  const result = outcomes[moves.indexOf(playerMove)][moves.indexOf(computerMove)]

  switch(result) {
    case -1:
      return `You Lose: ${computerMove} beats ${playerMove}`
    case 0:
      return 'Draw'
    case 1: 
      return `You Win: ${playerMove} beats ${computerMove}` 
    default:
      return 'Invalid move'
  }
}
```
A matrix can provide an organized structure for handling complex decision-making processes. Rock Paper Scissors serves as a canvas to illustrate how matrices, often overlooked, emerge as powerful tools for handling logic, elegantly.