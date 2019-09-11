# Tic-Tac-Toe
Player Class:
// coding starts here
#pragma once

#include <iostream>
#include <limits>

class board;
//Player class methods defining here
class Player
{
    char playermark;
public:
    char mark() const;
    void setmark(bool &, Player &);
    void turn(board &);
};

#include "Player.h"
#include "board.h"

//returns the mark for current player i.e X or O
char Player::mark() const{
    return playermark;
}

//sets the mark of each player i.e x or o
//uses markpass to tell game loop that correct inputs have been chosen
void Player::setmark(bool &markpass, Player &p2) {
    char tempmark; //hold input to select piece

    while (!markpass) {
        std::cin >> tempmark;
        if (tempmark == 'x' || tempmark == 'X' || tempmark == 'o' || tempmark == 'O') {
            playermark = tempmark;
            markpass = true;
        }
        else {
            std::cout << "Incorrect input\nTry again: ";
        }
    }
    //sets player 2's piece, I wanted a lowercase for a lower case and an upper for an upper
    switch (playermark) {
    case 'X': p2.playermark = 'O';
        break;
    case 'x': p2.playermark = 'o';
        break;
    case 'O': p2.playermark = 'X';
        break;
    case 'o': p2.playermark = 'x';
        break;
    }
}

//player turns
void Player::turn(board &game1){
    size_t tempposition = 0; //holds input for space player wishes to mark
    bool inputpass = false; //used to check if the input is passed, failed, or not marked
    while (!inputpass) {
        //tests input type
        if (std::cin >> tempposition) {
            game1.markboard(tempposition, playermark, inputpass); // goes to markboard function
        }
        //if the input type fails
        else {
            std::cout << "INCORRECT INPUT TYPE\nTry agian:";
            std::cin.clear(); //clears input fail state
            std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
        }
    }
}
board class:

#include "board.h"


//outputs the board onto the screen
void board::drawboard() {
    std::cout << " " << position[0] << " |" << " " << position[1] << " |" << " " << position[2] << "\n";
    std::cout << "___|___|___ \n";
    std::cout << " " << position[3] << " |" << " " << position[4] << " |" << " " << position[5] << "\n";
    std::cout << "___|___|___ \n";
    std::cout << " " << position[6] << " |" << " " << position[7] << " |" << " " << position[8] << "\n";
    std::cout << "   |   |   ";
}

//clears all user input from the board
void board::clearboard() {
    position = "123456789";
}

//marks board with x or o in proper space
//boardIndex is position given by user
void board::markboard(const size_t &boardIndex, const char &playermark, bool &inputpass) {
    char check = position[boardIndex - 1];
    //if check is used to determine if currently selected position is marked or not
    if (check != 'X' && check != 'x' && check != 'O' && check != 'o') {
        //tests if space choice is within domain of the board
        if (boardIndex > 0 && boardIndex <= 9) {
            position[boardIndex - 1] = playermark;
            inputpass = true; //'Passes' input so turn is over, otherwise loops through turn again
        }
        else {
            //entered the incorrect position
            std::cout << "INCORRECT SPACE CHOICE\nTry again: ";
        }
    }
    else {
        //entered position that has previously been marked
        std::cout << "SPACE HAS ALREADY BEEN MARKED\nTry again: ";
    }
}

//wrote down every scenario, found two patterns (it was this or 8 if else statements *I thought this was more creative*)
char board::checkwin(bool& gamewin, int &turncnt) {
    for (size_t i = 0, k = 8; i <= 3 && k >= 5; i++, k--) {
        if (position[4] == position[i] && position[i] == position[k]) {
            gamewin = true; //sets game condition to true, game has been won
            return position[4]; //returns winning 'piece' (X or O) to test who won
        }
    }
    int cnt = 0;
    size_t initial = 2;
    //second pattern
    for (size_t i = 1, k = 0; i <= 7 && k <= 9; i += 4, k += 8) {
        if (cnt == 1) {
            i -= 2; k -= 8;
            initial += 4;
        }
        if (position[initial] == position[i] && position[i] == position[k]) {
            gamewin = true;
            return position[initial];
        }
        cnt++;
    }
    //checks for cats game, must be greater than or equal to eight  to return C after game loop has ended(with no winner)
    if (turncnt >= 8) {
        gamewin = true;
        return 'C'; //retruns C for cats game
    }
}
Main cpp



#include <iostream>
#include <limits>
#include "board.h"
#include "Player.h"

int gamePlay();
void pause();

int main() {

    std::cout << "Welcome to Tic Tac Toe!\n\n";
    gamePlay();
    std::cout << "\n";
    pause();
}

int gamePlay() {
    board game1;
    Player p1, p2;
    bool markpass = false; //checks wether players have selected their pieces
    bool gamewin = false; //sets condition for game
    int turncnt = 0; //counts number of turns

    game1.drawboard(); //draws initial board with numbered spaces
    while (!gamewin) {
        if (!markpass) {
            std::cout << "\nWhat team do you want: X or O?\nPLayer 1: ";
            p1.setmark(markpass, p2); //sets piece for player 1 and 2
            std::cout << "Player 2: " << p2.mark() << "\n\n";
        }

        //turn for player 1
        if (!(turncnt % 2)) {
            std::cout << "Player 1 trun:\nSQUARE: ";
            p1.turn(game1);
            game1.checkwin(gamewin, turncnt);
        }
        //turn for player 2
        else {
            std::cout << "Player 2 trun:\nSQUARE: ";
            p2.turn(game1);
            game1.checkwin(gamewin, turncnt);
        }

        game1.drawboard(); // update gameboard
        std::cout << "\n";
        turncnt++; // increment turn count
    }


    std::cout << "GAME OVER!!!!!!!\n\n";
    //check cats game if none, conditional is used to determine who won
    if (!(game1.checkwin(gamewin, turncnt) == 'C')) {
        std::cout << "WINNER: Player "
            << ((game1.checkwin(gamewin, turncnt) == p1.mark()) ? "1" : "2");
    }
    else {
        std::cout << "Cats game!";
    }
    game1.clearboard();//resest board
    return 0;
}

//used to pause and see results
void pause() {
    char quit;
    std::cout << "Press any key followed by enter to quit";
    std::cin >> quit;
}
