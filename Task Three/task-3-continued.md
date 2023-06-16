**Task 3 continued**
Ill be covering The updated added functionality implemented in task 3, therefore functional requirements which remain same as task 2 will not be covered in the section.


// SECTION Implement a function to uncover cells and recursively uncover adjacent cells with no adjacent mines.


The "adjacentMineHandler" function remains primarily the same, however the modified section allows for recursion "if" the adjacent mines calculated earlier are strictly equal to 0. The program then continues to utilize the same nested "for" loops to access the -1,0,1 offset for the rows (offsetX) and columns (offsetY), and assign these to the "adjacentX" and "adjacentY", by adding the current index "x" or "y" plus the current offset provided by the loop (-1,0,1). If "x" and "y" are both 1, 1  the offset would be X-1, Y-1 (0 (x), 0 (y)) the first iteration would be for the x(row) axis staring at 0, then the and the first iteration of y(column) axis would be 0. The use of the inner nested loop means for every one outer loop, the inner loop completes its full cycle of iterations trough the (column) axis, until column offsets and the "offsetY <= 1" condition has been met, therefore the second (column) axis iteration coordinates would become 0(x) 1(y) then, 0(x) 2(y)

## Diagram off all iterations of grid indexes for a 3x3 grid if the starting coordinates are 1(x), 1(y):
0(x) 0(y), 0(x) 1(y), 0(x) 2(y) : iteration 1 outer loop, iteration 1,2,3 inner loop
1(x) 0(y), 1(x) 1(y), 1(x) 2(y) : iteration 2 outer loop, iteration 1,2,3 inner loop
2(x) 0(y), 2(x) 1(y), 2(x) 2(y) : iteration 3 outer loop, iteration 1,2,3 inner loop

Next within the inner loop (y) axis it checks if the "adjacentX" and "adjacentY" are within the grid boundaries, it dose this by checking if the "adjacentX" and "adjacentY" are greater or equal to 0 which is the minium row index, and if the "adjacentX" is less then row length and "adjacentY" is less then column length, because the ".length" returns a number starting from 1 and not 0 we use the "<" less than as our row index starts at 0. If it passed the previous condition we then check "if" the current "adjacentTile" which is a reference "gridObj[adjacentX][adjacentY]" visibility is not equal to "open" meaning that tile has not been opened, we then update the ".visibility" and ".char" properties of the "adjacentTile". Then we call the function "adjacentMineHandler" again but instead of parsing in the "x","y" input coords we parse in "adjacentX" and "adjacentY" which will run every time the inner loop iterates therefore creating an instance or context, This allows the function to be applied again to each adjacent tile, including their adjacent tiles, and so on, providing that it passes the specified conditions mentioned earlier. 

lastly, "if" the "adjacentMines === 0" condition is false, the code will skip the recursion and update the properties of the current "tileObj" which is a reference to "gridObj[x][y]" and change those properties and change the ".char" to "adjacentMines" number. At the end of the function it checks to see if the user has won this is important to add since on any recursive instance of this function being called the user could win the game due to not having any more valid tiles to select.

## code snippet taken from function
<script>
    if (adjacentMines === 0) {
        // The recursive opening of tiles only applies to cells without adjacent mines so with if statement checks
        // if the current tile object has no adjacent mines.

        tileObj.char = 0; // this updates the number which will represent it on the grid

        for (let offsetX = -1; offsetX <= 1; offsetX++) { // here again we have the exact same for loop with the offset as the code above to calculate the mines
            const adjacentX = x + offsetX;
            //calculates the current off set of the user x input

            for (let offsetY = -1; offsetY <= 1; offsetY++) {
                const adjacentY = y + offsetY;
                //calculates the current off set of the user y input

                if ( adjacentX >= 0 && adjacentX < rowLength && adjacentY >= 0 && adjacentY < colLength ) {
                    // we are also see the same grid boundary condition as above 

                    const adjacentTile = gridObj[adjacentX][adjacentY]; // adding a reference the the grid object with the adjacent coordinates as an index

                    if (adjacentTile.visibility !== "open") {
                        // only open unopened adjacent tiles

                        adjacentTile.visibility = "open";
                        adjacentTile.char = 0;
                        // updating the properties to display the adjacent tile

                        adjacentMineHandler(gridObj, adjacentX, adjacentY, gridObj[adjacentX][adjacentY]); // recursively open adjacent tiles

                        // we call the function again parsing in the adjacentX and adjacentY as our reference point which will then go though the same steps as
                        // the X, Y user input coords creating recursion for all adjacent tiles which have no adjacent mines.
                        // this function dose not stop the for loop until the <= 1 condition is met in the for loop, 
                        // it executes multiple instances of adjacentMineHandler function, for each adjacent tile, and then executes
                        // multiple instances of adjacentMineHandler function again, for each adjacent tile of the adjacent tile and so on 
                        // until the conditions are met stopping it from recurring again.
                    }
                }
            }
        }
    }

    if (adjacentMines > 0) { // if the selected tile has adjacent mines add the number
        tileObj.char = adjacentMines;
        tileObj.visibility = "open";
    }

    checkWinConditions(gridObj)
</script>

## full function
<script>
    function adjacentMineHandler(gridObj, x, y, tileObj) {
    let tileObj = gridObj[x][y] // reference to the current tile

     //declaring these variables in the local scope
    let adjacentMines = 0;
    const rowLength = gridObj.length;
    let colLength = gridObj[0].length; 

    // checks for adjacent mines by adding the offset to the x,y coords and sees if the surrounding tiles are mines.
    // coords neighboring offsets:
    // 0: (x - 1, y - 1) ,(x - 1, y),(x - 1, y + 1)
    // 1: (x, y - 1), (input : x , y ), (x, y + 1)
    // 2: (x + 1, y - 1),(x + 1, y),(x + 1, y + 1)

    // the offsetX and offsetY refers to how much we need to add or subtract from the users input which we index from
    // eg. if the input is 1,1 in the middle of the 3x3 grid 
    // to get to the top right index which would need to be 0,0
    // mathematically this is how it would work, x -1 = 0, y -1 = 0
    
    // we then use a nested for loop to apply all the off sets -1,0,+1 to each index
    // eg. assuming the input is (1,1)
    //   first row position : 1st(0,0), 2nd (0,1), 3rd (0,2)
    //   second row position : 1st(1,0), 2nd(1,1), 3rd (1,2)
    //   first row potion :   1st:(2,0), 2nd(2,1) , 3rd(2,2)

    // using the nested for loops we can achieve this bellow as the offsetX will run once every time the offsetY completes its loop

    for (let offsetX = -1; offsetX <= 1; offsetX++) {
        // offsetX is -1, it then becomes 0
        // offsetX is 0, it then becomes 1
        // loop is terminated as the offsetX <= 1 is not longer true

        const adjacentX = x + offsetX;
        // assuming x = 1, adjacentX = x + -1 = 0
        
        for (let offsetY = -1; offsetY <= 1; offsetY++) {
            // here we are doing the same offset but for every column but this will run as many times until the offsetY < 1 is met 
            // therefor it will run 3 times -1, 0, 1
            // then go back to the offsetX loop
            
            const adjacentY = y + offsetY; // adding the offset to the current coords

            if ( // check if tile is in boundaries
            adjacentX >= 0 && adjacentX < rowLength && adjacentY >= 0 && adjacentY < colLength
            // this insures that mines will be assigned as long as its an index in bounds or dose not exceed the number of rows
            ) {
                
                const adjacentTile = gridObj[adjacentX][adjacentY]; // adds the value of the adjacentX and Y to the index to get teh adjacent tile
                if (adjacentTile.isMine) { // checks if that tile is a mine
                    adjacentMines++; //  if it is a mine it updates mine counter 
                }
            }
        }
    }

    //SECTION Implement a function to uncover cells and recursively uncover adjacent cells with no adjacent mines.
    if (adjacentMines === 0) {
        // The recursive opening of tiles only applies to cells without adjacent mines so with if statement checks
        // if the current tile object has no adjacent mines.

        tileObj.char = 0; // this updates the number which will represent it on the grid

        for (let offsetX = -1; offsetX <= 1; offsetX++) { // here again we have the exact same for loop with the offset as the code above to calculate the mines
            const adjacentX = x + offsetX;
            for (let offsetY = -1; offsetY <= 1; offsetY++) {
                const adjacentY = y + offsetY;

                if ( adjacentX >= 0 && adjacentX < rowLength && adjacentY >= 0 && adjacentY < colLength ) {
                    // we are also see the same grid boundary condition as above 

                    const adjacentTile = gridObj[adjacentX][adjacentY]; // adding a reference the the grid object with the adjacent coordinates as an index

                    if (adjacentTile.visibility !== "open") {
                        // only open unopened adjacent tiles

                        adjacentTile.visibility = "open";
                        adjacentTile.char = 0;
                        // updating the properties to display the adjacent tile

                        adjacentMineHandler(gridObj, adjacentX, adjacentY, gridObj[adjacentX][adjacentY]); // recursively open adjacent tiles

                        // we call the function again parsing in the adjacentX and adjacentY as our reference point which will then go though the same steps as
                        // the X, Y user input coords creating recursion for all adjacent tiles which have no adjacent mines.
                        // this function dose not stop the for loop until the <= 1 condition is met in the for loop, 
                        // it executes multiple instances of adjacentMineHandler function, for each adjacent tile, and then executes
                        // multiple instances of adjacentMineHandler function again, for each adjacent tile of the adjacent tile and so on 
                        // until the conditions are met stopping it from recurring again.
                    }
                }
            }
        }
    }

    if (adjacentMines > 0) { // if the selected tile has adjacent mines add the number
        tileObj.char = adjacentMines;
        tileObj.visibility = "open";
    }

    checkWinConditions(gridObj)
}

</script>


//SECTION Implement a user input using the command line interface to display the game board and accept user input.


The "startGame" function ties the game together, and first allows us to get input for level choice to parse into the "initGridObj(gridSize)", and get the starting coordinates validate it within the "handleInput" and return an array which we assign to multiple variables using destructuring assignment, we can then avoid assigning mines to that position and we can assign the specified amount of mines from that level in the "assignMines" function. By adding these into the the function before the "while" loop we've ensured that this will only run when "startGame" is called and therefore acts like a first move. We then call the "tileState" function to apply the functionality to the tiles, within this function calls the "checkLossConditions", "adjacentMineHandler" and "checkWinConditions" and "displayGrid" functions. I did this particularly because these functions all rely on each other to parse inputs and it was easier to implement into the game logic by just calling one function that handles everything to do with the tiles and their various states. eg if the user wants to open a tile, first it will check if they've lost by calling the "checkLossCondition" function, if they haven't lost the code will continue to open the adjacent tiles with the "adjacentMineHandler" and then within the "adjacentMineHandler" function check if they've won by calling the "checkWinConditions" function,  the "tileState" will then call "displayGrid" to show the user the grid with their updated action.

We then implement a "while" loop, which will ask the user for input and validate it within the "handleInput" and return an array which we assign to multiple variables using destructuring assignment. Then call the "tileState" with the new inputs until the user has won or lost then the "gameLoop" will be set to false from within the "checkLossConditions" or "checkWinConditions" functions as its a global scope variable and is accessible to all functions.

Outside of this function is what will run first, we have alert which tells the user to navigate to the console. then the "startGame" function is called after 5 seconds (5000ms) due to the "setTimeOut" method, this allows the user enough time to navigate to the console before the program starts as once you have a prompt you cannot access the console without refreshing the browser.


<script>
    let gameLoop = true 
    // Global variable which will be turned off when the game ends

    function startGame() {

    const levelChoice = prompt("Choose your level E : easy | 3x3 3 mines, M: medium | 9x9 10 mines, H : Hard | 16x16 40 mines").toUpperCase();
    // gets user input on level choice and converts it to upper case

    let [numberOfMines, gridSize] = level(levelChoice); 
    // takes the array output of level function and assigns it to the variables using destructuring assignment 

    displayGrid(initGridObj(gridSize));
    // calls the displayGrid for the user to visualize there first move

    let [initInputX,initInputY,initAction] = handleInput(
        prompt("To start the game, please enter coordinates located on the left and top index in the format of: X, Y, O (open), F (flag), or ? (unsure), e.g., 1,1,O"),
        gridSize);
        // takes the array output of handleInput function and assigns it to the variables using destructuring assignment 
        // 

    const gridObj = initGridObj(gridSize); 
    // create the grid objects

    assignMines(initInputX, initInputY, gridObj, numberOfMines); 
    // assign mines to those grid objects

    tileState(gridObj, initInputX, initInputY, initAction); 
    // display the tile state from the users initial Input

    while (gameLoop === true) { 
    // after the initial move then we enter the game loop
    //this code will be executed until gameLoop is set to false in the win or loss condition functions

        let [inputX,inputY,action] = handleInput(prompt("Enter coords: X, Y, O F or ?"), gridSize);
        // we get a new input from the user each loop and apply the arr returned to the variables using destructuring assignment 

        tileState(gridObj, inputX, inputY, action);
        //then we call this function to apply the desired functionality to the users tile
    }
}

alert("Welcome to Minesweeper! In this game, you must use your powers of deduction to uncover tiles on the board without revealing hidden mines. Please navigate to your console as the game will beguin in 5 seconds")

setTimeout(startGame,5000); // call the function to start the game

</script>


## Bonus User stories implementation -------------------------------------------------------------------------------------
while utilizing the "agile SDLC" part of the development and testing process was to gather any further functional requirements in the form of user stories, my partner play tested my code and gave me some requirements he would expect from minesweeper. Ive organized these requirements in order of priority of which they will be implemented.

These requirements included :
- Instant death bug fix | High (done)
- Level difficulty's | High (done)
- Static mine counts for each level so that users can better utilize deduction | medium  (done)
- The first turn always has a tile with no adjacent mines so that you start with a blank slate to work with| low (future update) 



//SECTION Level selection 


This function takes the "levelChoice" from prompting the user in the "startGame" input in then applies the "levelChoice" to the "switch" statement and compares what the input value of which the user has chosen and assigns the "numberOfMines, currentLevel, gridSize" and returns an arr "[numberOfMines, gridSize]", to the parse into the "initGridObj" and "assignMines" function.


<script>
    function level(levelChoice) {
    let numberOfMines, currentLevel, gridSize // initializing multiple variables 

    switch (levelChoice) { 
        //using the switch we can check the level the user has selected in the levelChoice

        case "E":
            // if the user selected E the level will be specified as bellow
            numberOfMines = 3;
            currentLevel = "easy";
            gridSize = 3;
            break;
        case "M":
            // if the user selected M the level will be specified as bellow
            numberOfMines = 20;
            currentLevel = "medium";
            gridSize = 9;
            break;
        case "H":
            // if the user selected H the level will be specified as bellow
            numberOfMines = 40;
            currentLevel = "hard";
            gridSize = 16;
            break;
        default:
            // if the user inputted an invalid input the level will be specified as bellow as a kind of safe measure
            numberOfMines = 40;
            currentLevel = "The hidden level";
            gridSize = 9;
    }
    console.log(`your current difficulty is set to ${currentLevel} with ${numberOfMines} mines and a ${gridSize} x ${gridSize}`);
    // the display to the user there chosen level with the amount of mines

    return [numberOfMines, gridSize]; 
    // return an array of data we can parse into the initGrid function and assign mine function
}

</script>


//SECTION validating input for grid sizes


To implement the level feature for validating the input based on the grid size the only updated functionality since task 2 is the switch statement in the "handleInput" function, which is quite simple as it checks the "boardSize" variable and changed the "validInput" variable based upon what size the board is.


<script>
function handleInput(string,boardSize) {
    // string will represent the user input from prompt, board size comes from another function called level added into the final version

    let userInput = string
    .split(/\s*,\s*/) // uses Regex to split at commas and remove whitespace around them and return an array
    .map((item)=>{ // .map iterates over the array, for each item and will change the item based on the condition bellow
        return isNaN(item) ? item : parseInt(item);
        // uses a turnery operator to compare if the sting item is a NaN (not an Integer), 
        // if the string contains an integer it will convert it using parseInt
    });
    
    let validInput = []; // initializing an empty array to store the new value bellow otherwise its unaccessible out of the switch block

    switch (boardSize) {
        // this is not particularly reusable code but due to only having few levels I chose to do it like to save time

        case 3: // if the board size 3 x 3 bellow are the valid inputs
            validInput = [0, 1, 2, "O", "F", "?"];
            break;
        case 15: // if the board size 16 x 16 bellow are the valid inputs
            validInput = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, "O", "F", "?"];
            break;
        default: // the default ensures if the input is invalid you still get a result
            validInput = [0, 1, 2, 3, 4, 5, 6, 7,8, "O", "F", "?"];
            break;
    }

    while (!validInput.includes(userInput[0]) || !validInput.includes(userInput[1]) || !validInput.includes(userInput[2])) {
        // while validInput not including userInput at its 0 index 
        // or validInput not including userInput and its 1 index 
        // validInput not including userInput and its 2 index 


        newInput = prompt("Invalid input. Please enter coordinates (X, Y) and action (O, F, ?) in the format: X, Y, O/F/? (e.g., 1,1,O)");
        // get a valid input from the user until its correct
        
        userInput = newInput.split(/\s*,\s*/).map((item)=>{ 
            return isNaN(item) ? item : parseInt(item);
        }); // use the same method as above to remove space and commas, then map the items that contain integers and change the item based on the condition
        
    }

    // shows user their input location
        
    console.log(`board size 3 x 3`);// tells the user their grid choice
    console.log("Your X input:",userInput[0],"Your Y input:",userInput[1],"Your action:",userInput[2]);
    
    return userInput; // returns an array of the user input
}

</script>