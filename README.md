<!DOCTYPE html>
<html lang="en">
  <head>
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Connect 4</title>
    <!-- Google Fonts -->
    <link
      href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;500&display=swap"
      rel="stylesheet"
    />
    <!-- Stylesheet -->
    <link rel="stylesheet" href="style.css">
    <link rel="javascript" href="script.js">
  </head>
  <body>
    <div class="wrapper">
      <div id="playerTurn">Demo Text</div>
      <div class="container"></div>
      <div id="information">
        <div class="player-wrappers">
          Player 1
          <div class="player1"></div>
        </div>
        <div class="player-wrappers">
          Player 2
          <div class="player2"></div>
        </div>
      </div>
    </div>
    <div class="startScreen">
      <div id="message"></div>
      <button id="start">Start Game</button>
    </div>
    <!-- Script -->
    <script src="script.js"></script>
  </body>
</html>
body {
  margin: 0;
  padding: 0;
  font-family: "Poppins", sans-serif;
  background-color: #a19fa3;
}
.wrapper {
  width: 33.75em;
  background-color: #4fb3ca;
  position: absolute;
  padding: 2em;
  transform: translateX(-50%);
  left: 50%;
  top: 1.25em;
  border-radius: 0.5em;
}
.container {
  margin: 1.25em auto;
  width: 33.75em;
  border-radius: 0.6em;
  overflow: hidden;
  background-color: #8ddcb0;
}
#information {
  display: grid;
  width: 100%;
  grid-template-columns: 1fr 1fr;
}
.player-wrappers {
  display: flex;
  align-items: center;
  flex-direction: column;
}
.player1:before {
  content: "";
  display: inline-block;
  width: 3.12em;
  height: 3.12em;
  border-radius: 50%;
  background: radial-gradient(#fff04e 1.12em, #ffc400 1.25em);
}
.player2:before {
  content: "";
  display: inline-block;
  width: 3.12em;
  height: 3.12em;
  border-radius: 50%;
  background: radial-gradient(#ff4747 1.12em, #c00303 1.25em);
}
#playerTurn {
  margin: 1em 0;
  text-align: right;
}
#playerTurn span {
  font-weight: 600;
}
.startScreen {
  position: absolute;
  top: 0;
  height: 100vh;
  width: 100vw;
  background-color: #416cea;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.startScreen button {
  background-color: #ffffff;
  font-size: 1.4em;
  padding: 1em 2em;
  border: none;
  outline: none;
  border-radius: 2em;
  cursor: pointer;
}
.grid-row {
  margin: 0 auto;
  display: flex;
}
.grid-box {
  width: 5em;
  height: 5em;
  display: grid;
  place-items: center;
  background: radial-gradient(white 1.37em, #416cea 1.43em);
  cursor: pointer;
}
.hide {
  display: none;
}
#message {
  margin-bottom: 1em;
  color: #ffffff;
  font-size: 1.2em;
}
//Initial references
const container = document.querySelector(".container");
const playerTurn = document.getElementById("playerTurn");
const startScreen = document.querySelector(".startScreen");
const startButton = document.getElementById("start");
const message = document.getElementById("message");
let initialMatrix = [
  [0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0],
];
let currentPlayer;

//Random Number Between Range
const generateRandomNumber = (min, max) =>
  Math.floor(Math.random() * (max - min)) + min;

//Loop through array and check for same values
const verifyArray = (arrayElement) => {
  let bool = false;
  let elementCount = 0;
  arrayElement.forEach((element, index) => {
    if (element == currentPlayer) {
      elementCount += 1;
      if (elementCount == 4) {
        bool = true;
      }
    } else {
      elementCount = 0;
    }
  });
  return bool;
};

//Check for game over(Last step)
const gameOverCheck = () => {
  let truthCounnt = 0;
  for (let innerArray of initialMatrix) {
    if (innerArray.every((val) => val != 0)) {
      truthCounnt += 1;
    } else {
      return false;
    }
  }
  if (truthCounnt == 6) {
    message.innerText = "Game Over";
    startScreen.classList.remove("hide");
  }
};

//Check rows
const checkAdjacentRowValues = (row) => {
  return verifyArray(initialMatrix[row]);
};

//Check columns
const checkAdjacentColumnValues = (column) => {
  let colWinCount = 0,
    colWinBool = false;
  initialMatrix.forEach((element, index) => {
    if (element[column] == currentPlayer) {
      colWinCount += 1;
      if (colWinCount == 4) {
        colWinBool = true;
      }
    } else {
      colWinCount = 0;
    }
  });
  //no match
  return colWinBool;
};

//Get Right diagonal values
const getRightDiagonal = (row, column, rowLength, columnLength) => {
  let rowCount = row;
  let columnCount = column;
  let rightDiagonal = [];
  while (rowCount > 0) {
    if (columnCount >= columnLength - 1) {
      break;
    }
    rowCount -= 1;
    columnCount += 1;
    rightDiagonal.unshift(initialMatrix[rowCount][columnCount]);
  }
  rowCount = row;
  columnCount = column;
  while (rowCount < rowLength) {
    if (columnCount < 0) {
      break;
    }
    rightDiagonal.push(initialMatrix[rowCount][columnCount]);
    rowCount += 1;
    columnCount -= 1;
  }
  return rightDiagonal;
};

const getLeftDiagonal = (row, column, rowLength, columnLength) => {
  let rowCount = row;
  let columnCount = column;
  let leftDiagonal = [];
  while (rowCount > 0) {
    if (columnCount <= 0) {
      break;
    }
    rowCount -= 1;
    columnCount -= 1;
    leftDiagonal.unshift(initialMatrix[rowCount][columnCount]);
  }
  rowCount = row;
  columnCount = column;
  while (rowCount < rowLength) {
    if (columnCount >= columnLength) {
      break;
    }
    leftDiagonal.push(initialMatrix[rowCount][columnCount]);
    rowCount += 1;
    columnCount += 1;
  }
  return leftDiagonal;
};

//Check diagonal
const checkAdjacentDiagonalValues = (row, column) => {
  let diagWinBool = false;
  let tempChecks = {
    leftTop: [],
    rightTop: [],
  };
  let columnLength = initialMatrix[row].length;
  let rowLength = initialMatrix.length;

  //Store left and right diagonal array
  tempChecks.leftTop = [
    ...getLeftDiagonal(row, column, rowLength, columnLength),
  ];

  tempChecks.rightTop = [
    ...getRightDiagonal(row, column, rowLength, columnLength),
  ];
  //check both arrays for similarities
  diagWinBool = verifyArray(tempChecks.rightTop);
  if (!diagWinBool) {
    diagWinBool = verifyArray(tempChecks.leftTop);
  }
  return diagWinBool;
};

//Win check logic
const winCheck = (row, column) => {
  //if any of the functions return true we return true
  return checkAdjacentRowValues(row)
    ? true
    : checkAdjacentColumnValues(column)
    ? true
    : checkAdjacentDiagonalValues(row, column)
    ? true
    : false;
};

//Sets the circle to exact points
const setPiece = (startCount, colValue) => {
  let rows = document.querySelectorAll(".grid-row");
  //Initially it will place the circles in the last row else if no place availabke we will decrement the count until we find empty slot
  if (initialMatrix[startCount][colValue] != 0) {
    startCount -= 1;
    setPiece(startCount, colValue);
  } else {
    //place circle
    let currentRow = rows[startCount].querySelectorAll(".grid-box");
    currentRow[colValue].classList.add("filled", `player${currentPlayer}`);
    //Update Matrix
    initialMatrix[startCount][colValue] = currentPlayer;
    //Check for wins
    if (winCheck(startCount, colValue)) {
      message.innerHTML = `Player<span> ${currentPlayer}</span> wins`;
      startScreen.classList.remove("hide");
      return false;
    }
  }
  //Check if all are full
  gameOverCheck();
};

//When user clicks on a box
const fillBox = (e) => {
  //get column value
  let colValue = parseInt(e.target.getAttribute("data-value"));
  //5 because we have 6 rows (0-5)
  setPiece(5, colValue);
  currentPlayer = currentPlayer == 1 ? 2 : 1;

  playerTurn.innerHTML = `Player <span>${currentPlayer}'s</span> turn`;
};

//Create Matrix
const matrixCreator = () => {
  for (let innerArray in initialMatrix) {
    let outerDiv = document.createElement("div");
    outerDiv.classList.add("grid-row");
    outerDiv.setAttribute("data-value", innerArray);
    for (let j in initialMatrix[innerArray]) {
      //Set all matrix values to 0
      initialMatrix[innerArray][j] = [0];
      let innerDiv = document.createElement("div");
      innerDiv.classList.add("grid-box");
      innerDiv.setAttribute("data-value", j);
      innerDiv.addEventListener("click", (e) => {
        fillBox(e);
      });
      outerDiv.appendChild(innerDiv);
    }
    container.appendChild(outerDiv);
  }
};

//Initialise game
window.onload = startGame = async () => {
  //Between 1 and 2
  currentPlayer = generateRandomNumber(1, 3);
  container.innerHTML = "";
  await matrixCreator();
  playerTurn.innerHTML = `Player <span>${currentPlayer}'s</span> turn`;
};

//start game
startButton.addEventListener("click", () => {
  startScreen.classList.add("hide");
  startGame();
});
 
