/*Authors: Gabrielle King, Disha Patel, Jason Liang, Braden Turner
Program: connect4.c
Purpose: Allow the user to play a simplified version of Connect 4*/

#include <stdio.h>
#include <string.h>

#define FILE_NAME "scores.txt"
#define MAX_VALUES 100

//Function prototypes
int displayMenu();
char enterNames();
void pieceTracker(player1Name, player2Name);
int displayBoard(player1Name, player2Name, char piece[][]);
int winCondition(char piece[][], int numToConnect);
void showScores(FILE* filePtr);
int playAgain();

int main()
{
    //Variables
    int menuChoice, playChoice;
    char winnerName[MAX_VALUES], player1Name[MAX_VALUES], player2Name[MAX_VALUES];
    int winner, numToConnect;
    char piece[MAX_VALUES][MAX_VALUES];
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
                
                //Assign each player their piece
                pieceTracker(player1Name, player2Name);
                
                //Begin playing
                do{
                    displayBoard(player1Name, player2Name, piece);
                }while(winCondition(piece, numToConnect) != 1 || -1);
                
                //If someone wins, congratulate them. If there's a tie, say so.
                if(winCondition(piece, numToConnect) == 1){
                    printf("%s YOU WON!!! CONGRATS :D", winnerName);
                    filePtr = fopen(FILE_NAME, "r");
                    fprintf(filePtr, "%s: 1", winnerName);
                    fclose(filePtr);
                }else if(winCondition(piece, numToConnect) == -1){
                    printf("Tie game.");
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
    printf("***CONNECT 4***\
        1. Play Game\
        2. Show Scores\
        0. EXIT");
        
    printf("Enter your choice: ");
    scanf("%d", &menuChoice);
    
    return menuChoice;
}

void enterNames(char name1[], char name2[]){
    printf("Player 1, enter your name");
    scanf("%s", name1);
    
    printf("Player 2, enter your name");
    scanf("%s", name2);
}

void pieceTracker(player1Name, player2Name){
    printf("%s you'll be X's\n", player1Name);
    printf("%s you'll be O's\n", player2Name);
    
}

int displayBoard(player1Name, player2Name, char piece[][]){
    
}

int winCondition(char piece[][], int numToConnect){
    
}

void showScores(FILE* filePtr){
    
}

int playAgain(){
    int choice; 
    
    printf("Play again?\
    1 - yes\
    0 - no");
    printf("Enter your choice: ");
    scanf("%d", &choice);   
    
    return choice;
}
