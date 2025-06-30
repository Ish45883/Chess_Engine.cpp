
# Chess Engine - Ishan A




## Authors

- [@ishan](https://github.com/Ish45883)



## Appendix

Appendix
Minimax Search Algorithm (Core Logic):

Below is a simplified version of the brute-force Minimax algorithm with alpha-beta pruning used in the engine’s move evaluation:


    def minimax(position, depth, alpha, beta, maximizing_player):
    if depth == 0 or game_over(position):
        return evaluate(position)

    if maximizing_player:
        max_eval = float('-inf')
        for move in generate_legal_moves(position):
            eval = minimax(make_move(position, move), depth - 1, alpha, beta, False)
            max_eval = max(max_eval, eval)
            alpha = max(alpha, eval)
            if beta <= alpha:
                break  # beta cutoff
        return max_eval
    else:
        min_eval = float('inf')
        for move in generate_legal_moves(position):
            eval = minimax(make_move(position, move), depth - 1, alpha, beta, True)
            min_eval = min(min_eval, eval)
            beta = min(beta, eval)
            if beta <= alpha:
                break  # alpha cutoff
        return min_eval


Evaluation Function Details:

The evaluation function considers the following factors:

    1. Material count: Standard piece values (Pawn=1, Knight/Bishop=3, Rook=5, Queen=9)

    2. Positional bonuses: Central control and development

    3. King safety: Penalizes exposed king positions

Example formula:

    score = material_score(position) + positional_bonus(position) - king_exposure_penalty  (position)

UI Design (Console-Based Chessboard):

The chessboard UI updates in real time and uses Unicode characters to represent pieces.

Example Output:

    8 -R -N -B -Q -K -B -N -R
    7 -P -P -P -P -P -P -P -P
    6 .  .  .  .  .  .  .  .
    5 .  .  .  .  .  .  .  .
    4 .  .  .  .  .  .  .  .
    3 .  .  .  .  .  .  .  .
    2  P  P  P  P  P  P  P  P
    1  R  N  B  Q  K  B  N  R
       a  b  c  d  e  f  g  h


## Overview

**MACE** stands for **Modern Algorithm Chess Engine** (prototype), developed by Ishan A.  
This is a prototype chess engine written in C++ that demonstrates basic chess AI concepts, including move generation, board evaluation, and a simple search algorithm.  
The engine is designed for educational and experimental purposes and is not open source.

---

## Features

- **Custom Chess Engine:** Implements its own board representation and move logic.
- **AI Opponent:** The computer always plays as Black; the user plays as White.
- **Move Generator:** Generates all possible moves for both human and computer.
- **Simple Evaluation:** Evaluates board state based on material, development, and pawn advancement.
- **Text-Based Interface:** Runs in the terminal with ASCII board display.
- **Help System:** Type `?` for help on commands and piece notation.
- **Restart and Exit:** Type `n` to start a new game, `x` to exit.

---

## Code Structure

### Main Components

- **Board Representation:**  
  `int Board[12][12];`  
  Uses a 12x12 array for easy boundary checking (actual board is 8x8, surrounded by a border).

- **Move Generation:**  
  Functions like `HumanMoveGenerator`, `ComputerMoveGenerator`, and their helpers generate all legal moves for each side.

- **Evaluation:**  
  The `Evaluation()` function scores the board based on material, development, and pawn advancement.

- **Search:**  
  The engine uses a simple recursive search to select the best move for the computer.

- **Game Loop:**  
  The `main()` function handles user input, move validation, and alternates between human and computer moves.

### Key Functions

- `NewGame()`: Initializes the board to the starting position.
- `PrintBoard()`: Displays the current board in the terminal.
- `PrintHelp()`: Shows help and piece notation.
- `MakeMovement()` / `UnMakeMovement()`: Executes and undoes moves.
- `AllComputerMoves()` / `AllHumanMoves()`: Generates all possible moves for each side.
- `StartSearch()`: Begins the computer's move search.
- `Evaluation()`: Evaluates the board for the search algorithm.

---

## Optimizations & Suggestions

- **Move Validation:**  
  Currently, the engine does not check for all chess rules (e.g., castling, en passant, check/checkmate).
- **Search Depth:**  
  The search depth is fixed at 5; increasing it will make the engine stronger but slower.
- **Evaluation Function:**  
  The evaluation is basic; adding more positional factors can improve play.
- **User Interface:**  
  A graphical interface or web version could make the engine more user-friendly.
- **Code Modernization:**  
  Refactoring to use modern C++ features and removing `goto` would improve maintainability.


## In-Depth Documentation: Move Evaluation and Game Evaluation Functions

This section explains the core evaluation logic in `main1.cpp` for the MACE chess engine. These functions determine how the engine assesses the board and chooses moves.

---

### 1. `int Evaluation()`

**Purpose:**  
Calculates a numeric score for the current board position, combining material, development, pawn advancement, and piece proximity to objectives. The evaluation adapts based on the game phase (opening, middlegame, endgame).

**How it works:**  
- Calls `GameState()` to determine the phase of the game.
- In the **opening** (`State==1`):  
  `Evaluation = Material() + Development()`
- In the **middlegame** (`State==2`):  
  `Evaluation = Material() + Development() + HumanAproach(6,8) - ComputerAproach(6,3)`
- In the **endgame** (`State==3`):  
  Finds both kings' coordinates, then:  
  `Evaluation = Material() + Development() + PossibleCrown() + HumanAproach(XCK, YCK) - ComputerAproach(XHK, YHK)`

**Interpretation:**  
- Higher values favor the computer (Black), lower values favor the human (White).
- The function is called recursively during the search to compare possible future positions.

---

### 2. `int GameState()`

**Purpose:**  
Determines the current phase of the game: opening, middlegame, or endgame.

**How it works:**  
- If `MoveNo < 12`: **Opening** (`returns 1`)
- If `MoveNo > 30`: **Endgame** (`returns 3`)
- Otherwise: **Middlegame** (`returns 2`)

**Why:**  
Different evaluation criteria are more important in different phases (e.g., development in the opening, king safety in the endgame).

---

### 3. `int Material()`

**Purpose:**  
Calculates the total material value on the board.

**How it works:**  
- Sums the values of all pieces on the board using their integer codes (positive for computer, negative for human).
- Returns the net material balance.

**Why:**  
Material advantage is a fundamental factor in chess evaluation.

---

### 4. `int Development()`

**Purpose:**  
Rewards or penalizes piece development, especially in the opening.

**How it works:**  
- Adds points for computer pawns advanced to the 6th rank.
- Adds points if certain squares in the back rank are empty (indicating pieces have moved).
- Penalizes if human pieces are still on their back rank.
- Adds or subtracts points for pawns in the center.

**Why:**  
Encourages the engine to develop pieces and control the center early in the game.

---

### 5. `int PossibleCrown()`

**Purpose:**  
Rewards pawns that are close to promotion.

**How it works:**  
- For each computer pawn on the 3rd, 4th, or 5th rank, adds 15, 10, or 5 points, respectively.
- For each human pawn on the 8th, 7th, or 6th rank, subtracts 15, 10, or 5 points, respectively.

**Why:**  
Promoting a pawn is a major advantage, so the engine is encouraged to advance pawns.

---

### 6. `int HumanAproach(int X2, int Y2)`  
### 7. `int ComputerAproach(int X2, int Y2)`

**Purpose:**  
Measures how close pieces are to a target square (usually the enemy king or a promotion square).

**How it works:**  
- For each relevant piece, calculates the Euclidean distance to the target square.
- In the endgame, the king's distance is weighted more heavily.
- Sums the distances for all pieces.

---

### 8. `void KingCoordinates(int &XCK, int &YCK, int &XHK, int &YHK)`

**Purpose:**  
Finds the coordinates of both kings on the board.

**How it works:**  
- Scans the board for the computer king (`King`) and the human king (`-King`).
- Sets the reference variables to their positions.

**Why:**  
Used by the evaluation functions to assess king safety and proximity.

---

## Summary Table

| Function            | Main Role                                      | Key Factors Considered                |
|---------------------|------------------------------------------------|---------------------------------------|
| `Evaluation()`      | Overall board scoring                          | Material, development, pawn advance   |
| `GameState()`       | Game phase detection                           | Move number                           |
| `Material()`        | Piece count and value                          | All pieces                            |
| `Development()`     | Piece activity and central control             | Pawn advance, empty back rank squares |
| `PossibleCrown()`   | Pawn promotion potential                       | Pawns close to promotion              |
| `HumanAproach()`    | Human piece proximity to objectives            | Distance to target square             |
| `ComputerAproach()` | Computer piece proximity to objectives         | Distance to target square             |
| `KingCoordinates()` | Locates both kings                             | Board scan                            |

---

## Notes

- The evaluation is **basic** and does not account for all chess subtleties (e.g., king safety, mobility, threats).
- The engine does **not** currently check for check, checkmate, or castling.
- The evaluation is designed for speed and simplicity, suitable for a prototype engine.

---

**Note:**  
This is a prototype and a phase one beta model. Castling, checks, and checkmates are not yet implemented. Await further improvements



## Demo
 Below is a snapshot of the program running using the system terminal.



## Snapshots
[App Screenshot](https://ibb.co/dwsVCdGc)


## Known Limitations
    
    1. Currently limited to ~3–4 ply due to brute-force approach
    2. No advanced evaluation heuristics like mobility or threat detection

## License - IPR


Copyright © 2019 Ishan A

This project is my undertaking for educational and research purposes. I make no guarantees or accept any warranties concerning the performance, reliability, or suitability of this program for any particular purpose. The chess engine is therefore released 'as-is'.

Important Notices:

No Liability: The author shall not be liable for any damages or loss of data or any other problems occurring as a consequence of the use of this program.

No Warranty: No warranty is granted on the correctness of move evaluations, performance, or compatibility with third-party systems.

For Non-Commercial Purposes: The project is meant to be used for non-commercial purposes. Redistribution, modification, or commercial use of the software is regulated by pertinent licensing agreements.

Ownership: Any third-party library included in this project operates under its own license, and the author does not claim ownership over such components.

By using this code, you acknowledge that you have read and agree to these terms.

***Kindly note the above documentation is produced using the assitance of OpenAI***
