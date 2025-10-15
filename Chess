<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Mobile Chess Game</title>
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/chessboard.js/1.0.0/css/chessboard.min.css"
    integrity="sha512-lb9FqAX6WzVqTJt0BujA2u1B+ykAJ4LmgGqedrYgnfd6MKZntAWRt54ctn8YvQ3KJqbsE5xV9KzW8sECEDhP4A=="
    crossorigin="anonymous"
    referrerpolicy="no-referrer"
  />
  <style>
    body {
      font-family: sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      margin: 0;
      padding: 0;
      height: 100vh;
      background: #f0f0f0;
    }

    #board {
      width: 90vw;
      max-width: 90vmin;
    }

    #status {
      margin-top: 10px;
      font-size: 1.2em;
    }

    .chessboard-63f37 {
      touch-action: manipulation;
    }
  </style>
</head>
<body>
  <div id="board"></div>
  <div id="status">Your move</div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/chess.js/0.13.4/chess.min.js"
    integrity="sha512-OT7My9WyGyrTZ/YZ7vNmYo0T0TSCZKz5+DYm7xT9o5xDZehgUsDjOPBJS8ULm5JfVkFiYkaR7G7OhRrOPD9rXg=="
    crossorigin="anonymous" referrerpolicy="no-referrer"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/chessboard.js/1.0.0/js/chessboard.min.js"
    integrity="sha512-fk8IK1QTwvJqGU5WfhOnuM37MHw5q3ZzfhJ1IvtpqXiPoK4RH+pN5Qnhzav69uADLDkgn7iFwdoN8I+F74wTmg=="
    crossorigin="anonymous" referrerpolicy="no-referrer"></script>
  <script>
    const boardEl = document.getElementById('board');
    const statusEl = document.getElementById('status');

    const game = new Chess();

    function onDragStart(source, piece, position, orientation) {
      // Do not pick up pieces if the game is over
      if (game.game_over()) return false;

      // Only pick up pieces for the side to move
      if (
        (game.turn() === 'w' && piece.search(/^b/) !== -1) ||
        (game.turn() === 'b' && piece.search(/^w/) !== -1)
      ) {
        return false;
      }
    }

    function onDrop(source, target) {
      // See if the move is legal
      const move = game.move({
        from: source,
        to: target,
        promotion: 'q', // Always promote to a queen for simplicity
      });

      // Illegal move
      if (move === null) return 'snapback';

      updateStatus();
    }

    function onSnapEnd() {
      board.position(game.fen());
    }

    function updateStatus() {
      let status = '';

      let moveColor = game.turn() === 'w' ? 'White' : 'Black';

      // Checkmate?
      if (game.in_checkmate()) {
        status = `Game over, ${moveColor} is in checkmate.`;
      }

      // Draw?
      else if (game.in_draw()) {
        status = 'Game over, drawn position';
      }

      // Game still on
      else {
        status = `${moveColor} to move`;

        // Check?
        if (game.in_check()) {
          status += `, ${moveColor} is in check`;
        }
      }

      statusEl.textContent = status;
    }

    const board = Chessboard('board', {
      draggable: true,
      position: 'start',
      onDragStart: onDragStart,
      onDrop: onDrop,
      onSnapEnd: onSnapEnd,
    });

    updateStatus();
  </script>
</body>
</html>
