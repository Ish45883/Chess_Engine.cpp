
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







## Demo
 Below is a snapshot of the program running using the system terminal.



## Snapshots
[App Screenshot](https://ibb.co/dwsVCdGc)


## Optimizations

Known Limitations and Future Work
    
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
