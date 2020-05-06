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
void displayBoard(int winner, int turnCounter, char player1Name[], char player2Name[], char piece[6][7]);
int winCondition(int turnCounter, char piece[6][7], int numToConnect);
void showScores(FILE* filePtr);
int playAgain();
void strswap(char right[], char left[]);
void strcopy(char source[], char des[]);
int strcompare(char a[], char b[]);

int main()
{
    //Variables
    int menuChoice = 0, playChoice = 0, winsP1 = 0, winsP2 = 0;
    char winnerName[MAX_VALUES], player1Name[MAX_VALUES], player2Name[MAX_VALUES];
    int winner = 0, numToConnect = 0, turnCounter = 1;
    char piece[6][7];
    FILE* filePtr;
    
    do{
        menuChoice = displayMenu();
        
        switch(menuChoice){
            case 1:
                //Prompt for player names
                enterNames(player1Name, player2Name);
                
                //Ask user how many they want to connect in a row
                printf("How many do you want to connect in a row? ");
                scanf("%d", &numToConnect);

                // While loop that checks for correct input
                while(numToConnect > 7 || numToConnect <= 1)
                {
                  //Ask user how many they want to connect in a row
                  printf("Error, please enter a valid number (From 2 to 7): ");
                  scanf("%d", &numToConnect);
                }
                
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
                    displayBoard(winner, turnCounter, player1Name, player2Name, piece);

                    winner = winCondition(turnCounter, piece, numToConnect);

                    if(turnCounter == 1 && winner == 0)
                    {
                      turnCounter = 2;
                    }
                    else if (turnCounter == 2 && winner == 0)
                    {
                      turnCounter = 1;
                    }
                
                    //If someone wins, congratulate them. If there's a tie, say so.
                    if(winner == 1){
                        displayBoard(winner, turnCounter, player1Name, player2Name, piece);

                        if(turnCounter == 1)
                        {
                          for(int i = 0; i < MAX_VALUES; i++)
                          {
                            winnerName[i] = player1Name[i];
                          }
                          winsP1++;
                        }
                        else if(turnCounter == 2)
                        {
                          for(int i = 0; i < MAX_VALUES; i++)
                          {
                            winnerName[i] = player2Name[i];

                          }
                          winsP2++;                     
                        }

                        printf("%s YOU WON!!! CONGRATS :D\n", winnerName);
                    }else if(winner == -1){
                        displayBoard(winner, turnCounter, player1Name, player2Name, piece);
                        printf("It's a tie! Try again...\n");
                    }

                  // Calls playAgain() function to receive user choice on playing again
                  if(winner != 0)
                  {
                    playChoice = playAgain();
                        
                      // If playerChoice is yes, reset all row and column values to blank otherwise output to file
                      if(playChoice == 1)
                      {
                          for(int row = 0; row < 6; row++)
                          {
                            
                            for (int col = 0; col < 7; col++)
                            {
                                piece[row][col] = ' ';
                            }
                          }

                          winner = 0;
                          playChoice = 0;
                          if(turnCounter == 1 && winner == 0)
                          {
                            turnCounter = 2;
                          }
                          else if (turnCounter == 2 && winner == 0)
                          {
                            turnCounter = 1;
                          }
                      }
                      else
                      {
                        // Checks if file can be opened and will create a file or output to a file accordingly
                        if((filePtr = fopen(FILE_NAME, "r")) == NULL)
                        {
                          filePtr = fopen(FILE_NAME, "w");
                        }
                        else
                        {
                          filePtr = fopen(FILE_NAME, "a");
                        }

                        // Outputs to the file
                        if(winsP2 > winsP1 && winsP2 > 0 && winsP1 > 0)
                        {
                        fprintf(filePtr, "%s: %d\n", player2Name, winsP2);
                        fprintf(filePtr, "%s: %d\n", player1Name, winsP1);
                        }
                        else if(winsP1 > winsP2 && winsP1 > 0 && winsP2 > 0)
                        {
                        fprintf(filePtr, "%s: %d\n", player1Name, winsP1);
                        fprintf(filePtr, "%s: %d\n", player2Name, winsP2);
                        }
                        else if(winsP1 == winsP2 && winsP1 > 0)
                        {
                        fprintf(filePtr, "%s: %d\n", player1Name, winsP1);
                        fprintf(filePtr, "%s: %d\n", player2Name, winsP2);
                        }
                        else if(winsP1 > 0)
                        {
                        fprintf(filePtr, "%s: %d\n", player1Name, winsP1);
                        }
                        else if(winsP2 > 0)
                        {
                        fprintf(filePtr, "%s: %d\n", player1Name, winsP2);
                        }

                        fclose(filePtr);
                        winsP1 = 0;
                        winsP2 = 0;
                        winner = 0;
                        break;
                        }
                      }
                }while(winner != 1 && winner != -1);


            break;
            
            case 2:
                //Shows scores
                filePtr = fopen(FILE_NAME, "r");
                if(filePtr == NULL)
                {
                  filePtr = fopen(FILE_NAME, "w");
                  fclose(filePtr);
                  filePtr = fopen(FILE_NAME, "r");
                  showScores(filePtr);
                  fclose(filePtr);
                }
                else
                {
                  showScores(filePtr);
                }

                fclose(filePtr);
            break;
            
            case 0:
            break;
            
            default:
                printf("Please enter a valid option.\n");
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
    if(scanf("%s", name2) != 0)
    {
    printf("*********************\n");
    }
    else
    {
    scanf("%s", name2);
    }


}

void pieceTracker(char player1Name[], char player2Name[]){
    printf("*********************\n");  
    printf("%s you'll be X's\n", player1Name);
    printf("%s you'll be O's\n", player2Name); 
}

void displayBoard(int winner, int turnCounter, char player1Name[],char player2Name[], char piece[6][7]){
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
    if(turnCounter == 1 && winner == 0)
    {
      printf("%s - Enter your move: ", player1Name);
      scanf("%d", &move);
    }
    else if (turnCounter == 2 && winner == 0)
    {
      printf("%s - Enter your move: ", player2Name);
      scanf("%d", &move);
    }

    // Checks if user entered value is possible
    while((winner == 0 && move < 1) ||(winner == 0 && move > 7))
    {
      printf("Please enter a valid move: ");
      scanf("%d", &move);    
    }

    // Checks if the board is filled
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
    int win = 0, connected = 1, filled = 0;
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
          // Variable declarations
          int i = row;
          int j = col;

          // Checks for a column match
          while(piece[i-1][j] == movePiece)
          {
            connected++;
            i--;
          }
          // Resets connected if no win condition met, if win condition met sets win to the player being checked
          if(connected != numToConnect)
          {
            connected = 1;
          }
          else if (connected == numToConnect)
          {
            win = 1;
          }

          // Resets check
          i = row;
          j = col;

          // Checks for a row match
          while(piece[i][j+1] == movePiece)
          {
            connected++;
            j++;
          }

          // Resets connected if no win condition met, if win condition met sets win to the player being checked
          if(connected != numToConnect)
          {
            connected = 1;
          }
          else if (connected == numToConnect)
          {
            win = 1;
          }

          // Resets check
          i = row;
          j = col;

          // Checks for a topside diagonal match
          while(piece[i-1][j+1] == movePiece)
          {
            connected++;
            i--;
            j++;
          }

          // Resets connected if no win condition met, if win condition met sets win to the player being checked
          if(connected != numToConnect)
          {
            connected = 1;
          }
          else if (connected == numToConnect)
          {
            win = 1;
          }

          // Resets check
          i = row;
          j = col;

          // Checks for a botside diagonal match
          while(piece[i+1][j+1] == movePiece)
          {
            connected++;
            i++;
            j++;
          }

           // Resets connected if no win condition met, if win condition met sets win to the player being checked
          if(connected != numToConnect)
          {
            connected = 1;
          }
          else if (connected == numToConnect)
          {
            win = 1;
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

void showScores(FILE* filePtr)
{
  // Variable Declaration
  int wins = 0, winsA[MAX_VALUES], size = 0, tempW = 0;
  char name[MAX_VALUES], tempN[MAX_VALUES], *nameP = name, temp = ' ';
 
  printf("**HIGH SCORES**\n\n");

  while(feof(filePtr) == 0)
  {
    fscanf(filePtr, "%s %d\n", nameP, &tempW);
	printf("%s %d\n", nameP, tempW);
    nameP++;
    winsA[size] = tempW;
    size++;
  }

	for(int i=0; i<size; i++){
		printf("%d\n", winsA[i]);
	}


for (int i=0; i<size-1; i++){
		for(int j=0; j<size-i-1; j++){
			if(strcompare(winsA, winsA +1) < 0){
				strswap(winsA, winsA +1);
				//strswap(songs, songs[j+1]);
			}
		}
	}

	




  nameP = 0;

  for(int i = 0; i < size - 1; i++)
  {
    for(int j = 0; j < size - 1; j++)
    {
      if(winsA[j] > winsA[j+1])
      {
        tempW = winsA[j];
        tempN[MAX_VALUES - 1] = *(nameP+j);
        winsA[j] = winsA[j+1];
        *(nameP+j) = *(nameP+j+1);
        winsA[j+1] = tempW;
        *(nameP+j+1) = tempN[MAX_VALUES - 1];
      }
    }
  }

  nameP = 0;

  for(int i = 0; i < size; i++)
  {
    printf("%s: %d\n", nameP, winsA[i]);
    nameP++;
  }

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

int strcompare(char a[], char b[]){
	int i=0;


	do{
		if (a[i] < b[i]){
			return 1; 
		}else if (a[i]> b[i]){
			return -1; 
		}
	i++; 
	}while(a[i] != '\0' || b[i] != '\0'); 
	return 0;
}


void strcopy(char source[], char des[]){
	int i; 
	for(i=0; source[i] != '\0'; i++){
		des[i] = source[i];
	}
	des[i] ='\0';
	
}

void strswap(char right[], char left[]){
	char temp[MAX_VALUES];
	strcopy(right, temp);
	strcopy(left, right);
	strcopy(temp, left);


}
