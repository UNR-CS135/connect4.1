/*Authors: Gabrielle King, Disha Patel, Jason Liang, Braden Turner
Program: connect4.c
Purpose: Allow the user to play a simplified version of Connect 4*/

#include <stdio.h>
#include <string.h>

#define FILE_NAME "scores.txt"
#define MAX_VALUES 100

//Function prototypes
int displayMenu();
void enterNames(char name1[], char name2[]);
void pieceTracker(char player1Name[], char player2Name[]);
void displayBoard(int turnCounter, char player1Name[], char player2Name[], char piece[6][7]);
int winCondition(int turnCounter, char piece[6][7], int numToConnect);
void showScores(FILE* filePtr);
int playAgain();

int main()
{
    //Variables
    int menuChoice, playChoice;
    char winnerName[MAX_VALUES], player1Name[MAX_VALUES], player2Name[MAX_VALUES];
    int winner, numToConnect, turnCounter = 1;
    char piece[6][7];
    FILE* filePtr;
    
    // Bug checking list:
    /*
    Shouldn't there be an error check if the user wants to connect like 8 in a row when the size of the display is only 6x7?
    Also what happens if we enter the same name for both players?
    - Jason
    */
    do{
        menuChoice = displayMenu();
        
        switch(menuChoice){
            case 1:
                //Prompt for player names
                enterNames(player1Name, player2Name);
                
                //Ask user how many they want to connect in a row
                printf("How many do you want to connect in a row? ");
                scanf("%d", &numToConnect);
                
                //Assign each player their piece
                pieceTracker(player1Name, player2Name);

                // Double for statement that sets pieces to a blank slate
                for(int row = 0; row < 6; row++)
                {
                  for(int col = 0; col < 7; col++)
                  {
                    piece[row][col] = ' ';
                  }
                }

                //Begin playing
                do{
                    displayBoard(turnCounter, player1Name, player2Name, piece);

                    winner = winCondition(turnCounter, piece, numToConnect);

                    if(turnCounter == 1)
                    {
                      turnCounter = 2;
                    }
                    else if (turnCounter == 2)
                    {
                      turnCounter = 1;
                    }
                }while(winner != 1 && winner != -1);
                
                //If someone wins, congratulate them. If there's a tie, say so.
                if(winner == 1){
                    printf("%s YOU WON!!! CONGRATS :D\n", winnerName);
                    filePtr = fopen(FILE_NAME, "r");
                    fprintf(filePtr, "%s: 1", winnerName);
                    fclose(filePtr);
                }else if(winner == -1){
                    printf("Tie game.\n");
                }
                
                // Calls playAgain() function to receive user choice on playing again
                playChoice = playAgain();
                
                // If playerChoice is yes, reset all row and column values to blank
                if(playChoice == 1)
                {
                    for(int row = 0; row < 6; row++)
                    {
                    
                        for (int col = 0; col < 7; col++)
                        {
                            piece[row][col] = ' ';
                        }
                    }
                }
                
            break;
            
            case 2:
                //Check if file can be opened. If successful, show scores.
                if((filePtr = fopen(FILE_NAME, "r")) == NULL){
                    printf("Sorry, can't open the file.\n");
                    return -1;
                }else{
                   while(!feof(filePtr)){
                       showScores(filePtr);
                   }
                }fclose(filePtr);
            break;
            
            case 0:
            break;
            
            default:
                printf("Please enter a valid option.");
            break;
        }
        
        
    }while(menuChoice != 0);

    return 0;
}

//Functions
int displayMenu(){
    int menuChoice;
    printf("***CONNECT 4***\n");
    printf("1. Play Game\n");
    printf("2. Show Scores\n");
    printf("0. EXIT\n");
        
    printf("Enter your choice: ");
    scanf("%d", &menuChoice);
    
    return menuChoice;
}

void enterNames(char name1[], char name2[]){
    printf("Player 1, enter your name: ");
    scanf("%s", name1);
    
    printf("Player 2, enter your name: ");
    scanf("%s", name2);

}

void pieceTracker(char player1Name[], char player2Name[]){
    printf("*********************\n");  
    printf("%s you'll be X's\n", player1Name);
    printf("%s you'll be O's\n", player2Name); 
}

void displayBoard(int turnCounter, char player1Name[],char player2Name[], char piece[6][7]){
    // Variable declaration
    int move = 0, movePossible = 0;
    char movePiece = ' ';

    // Sets which piece is being dropped
    if(turnCounter == 1)
    {
      movePiece = 'X';
    }
    else if(turnCounter == 2)
    {
      movePiece = 'O';
    }

    // Displays the top of the board
    printf("---------------------\n");
    printf("[1][2][3][4][5][6][7]\n");
    printf("---------------------\n");

    // Double for loop that outputs the rest of the pieces/board
    for(int row = 0; row < 6; row++)
    {
      printf("[%c][%c][%c][%c][%c][%c][%c]\n", piece[row][0], piece[row][1], piece[row][2], piece[row][3], piece[row][4], piece[row][5], piece[row][6]);
    }

    // Outputs the bottom of the board
    printf("---------------------\n");
    printf("[1][2][3][4][5][6][7]\n");  

    // Asks player to input a move
    if(turnCounter == 1)
    {
      printf("%s - Enter your move: ", player1Name);
      scanf("%d", &move);
    }
    else if (turnCounter == 2)
    {
      printf("%s - Enter your move: ", player2Name);
      scanf("%d", &move);
    }

    while(movePossible == 0)
    {
      // Converts user move to array storage format
      move = move - 1;

      // Checks if the move can be made in the column
      for(int row = 5; row >= 0; row--)
      {
        if(piece[row][move] == ' ')
        {
          piece[row][move] = movePiece;
          movePossible = 1;
          break;
        }
      }

      // Outputs error if move cannot be made
      if(movePossible == 0)
      {
        printf("Please enter a valid move: ");
        scanf("%d", &move);
      }
    }
}

int winCondition(int turnCounter, char piece[6][7], int numToConnect){
    // Variable declaration
    int win = 0, connected = 0, filled = 0;
    char movePiece = ' ';

    // Sets which piece is being checked
    if(turnCounter == 1)
    {
      movePiece = 'X';
    }
    else if(turnCounter == 2)
    {
      movePiece = 'O';
    }

    // Double for loop that checks for numToConnect in a row for win condition trigger
    for(int row = 5; row >= 0; row--)
    {
      for(int col = 0; col < 7; col++)
      {
        // Checks top, right, and top-right for more of the same pieces of the player
        if(piece[row][col] == movePiece)
        {
          // Checks for a column match
          if(piece[row-1][col] == movePiece && row > (6 - numToConnect))
          {
            for(int i = row; i >= 0; i--)
            {
              if(piece[i][col] == movePiece)
              {
                connected++;
              }
            }

            // Resets connected if no win condition met, if win condition met sets win to the player being checked
            if(connected != numToConnect)
            {
              connected = 0;
            }
            else if (connected == numToConnect)
            {
              win = 1;
            }
          }
          // Checks for a row match
          else if(piece[row][col+1] == movePiece && col < (7 - numToConnect))
          {
            for(int i = col; i < 7; i++)
            {
              if(piece[row][i] == movePiece)
              {
                connected++;
              }
            }

            // Resets connected if no win condition met, if win condition met sets win to the player being checked
            if(connected != numToConnect)
            {
              connected = 0;
            }
            else if (connected == numToConnect)
            {
              win = 1;
            }
          }
          // Checks for a diagonal match
          else if(piece[row-1][col+1] == movePiece && row > (6 - numToConnect) && col < (7 - numToConnect))
          {
            for(int i = row; i >= 0; i--)
            {
              for(int j = col; j < 7; j++)
              {
                if(piece[i][j] == movePiece)
                {
                  connected++;
                }
              }
            }

            // Resets connected if no win condition met, if win condition met sets win to the player being checked
            if(connected != numToConnect)
            {
              connected = 0;
            }
            else if (connected == numToConnect)
            {
              win = 1;
            }
          }
        }
        
        // Checks if board is entirely filled
        if(piece[row][col] != ' ')
        {
          filled += 1;
        }
      }
    }

    // If board entirely filled return tie
    if(filled == 42)
    {
      win = -1;
    }

    return win;
}

void showScores(FILE* filePtr){
    
}

int playAgain(){
    int choice; 
    
    printf("Play again?\n");
    printf("1 - yes\n");
    printf("0 - no\n");
    printf("Enter your choice: ");
    scanf("%d", &choice);   
    
    return choice;
}
