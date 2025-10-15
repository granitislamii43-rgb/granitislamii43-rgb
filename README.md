<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Chess vs AI</title>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/chessboard.js/1.0.0/css/chessboard.min.css">
  <style>
    body {
      background: #f0f0f0;
      font-family: sans-serif;
      text-align: center;
      padding: 40px;
    }
    #board {
      width: 480px;
      margin: 0 auto;
    }
    #status {
      margin-top: 20px;
      font-size: 18px;
    }
    #difficulty {
      margin: 20px;
    }
  </style>
</head>
<body>
  <h1>Play Chess vs AI</h1>

  <label for="difficulty">AI Difficulty:</label>
  <select id="difficulty">
    <option value="3">Easy (Depth 3)</option>
    <option value="6" selected>Medium (Depth 6)</option>
    <option value="10">Hard (Depth 10)</option>
    <option value="15">Expert (Depth 15)</option>
  </select>

  <div id="board"></div>
  <div id="status">White to move</div>

  <!-- Chess libraries -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/chess.js/1.0.0/chess.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/chessboard.js/1.0.0/js/chessboard.min.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/niklasf/stockfish.js/stockfish.js"></script>

  <script>
    const game = new Chess();
    const stockfish = new Worker('https://cdn.jsdelivr.net/gh/niklasf/stockfish.js/stockfish.js');

    const board = Chessboard('board', {
      draggable: true,
      position: 'start',
      onDrop: onDrop,
      onSnapEnd: () => board.position(game.fen()),
    });

    const statusElement = document.getElementById('status');
    const difficultySelect = document.getElementById('difficulty');

    function updateStatus() {
      let status = game.turn() === 'w' ? 'White to move' : 'Black to move';
      if (game.in_checkmate()) {
        status = 'Checkmate! ' + (game.turn() === 'w' ? 'Black' : 'White') + ' wins';
      } else if (game.in_draw()) {
        status = 'Draw!';
      } else if (game.in_check()) {
        status += ' — Check!';
      }
      statusElement.textContent = status;
    }

    function onDrop(source, target) {
      const move = game.move({
        from: source,
        to: target,
        promotion: 'q'
      });

      if (!move) return 'snapback';

      updateStatus();

      // Let the AI move
      setTimeout(makeAIMove, 300);
    }

    function makeAIMove() {
      if (game.game_over()) return;

      const depth = parseInt(difficultySelect.value);

      stockfish.postMessage('ucinewgame');
      stockfish.postMessage('position fen ' + game.fen());
      stockfish.postMessage('go depth ' + depth);

      stockfish.onmessage = function (event) {
        const line = event.data;

        if (line.startsWith('bestmove')) {
          const parts = line.split(' ');
          const move = parts[1];

          if (move === '(none)') return; // no legal moves

          game.move({
            from: move.substring(0, 2),
            to: move.substring(2, 4),
            promotion: 'q'
          });

          board.position(game.fen());
          updateStatus();
        }
      };
    }

    updateStatus();
  </script>
</body>
</html>
