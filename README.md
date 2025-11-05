[테트리스.html](https://github.com/user-attachments/files/23354441/default.html)
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>듀얼 테트리스</title>
  <style>
    body {
      margin: 0;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      background: #111;
      color: #f5f5f5;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }
    #app {
      width: 900px;
      max-width: 100%;
      background: #181818;
      border-radius: 16px;
      box-shadow: 0 10px 30px rgba(0,0,0,0.6);
      padding: 20px 24px 24px;
      box-sizing: border-box;
    }
    h1, h2 {
      margin: 0 0 12px;
      text-align: center;
    }
    .subtitle {
      text-align: center;
      font-size: 13px;
      color: #aaa;
      margin-bottom: 20px;
    }
    .screen {
      display: none;
    }
    .screen.active {
      display: block;
    }
    .btn-row {
      display: flex;
      justify-content: center;
      gap: 12px;
      margin-top: 16px;
    }
    button {
      padding: 8px 16px;
      border-radius: 999px;
      border: none;
      cursor: pointer;
      font-size: 14px;
      background: #2f8cff;
      color: #fff;
      transition: transform 0.07s ease, box-shadow 0.07s ease, background 0.15s;
      box-shadow: 0 4px 12px rgba(47,140,255,0.35);
    }
    button.secondary {
      background: #333;
      box-shadow: 0 3px 10px rgba(0,0,0,0.4);
    }
    button:hover {
      transform: translateY(-1px);
      box-shadow: 0 6px 16px rgba(47,140,255,0.5);
    }
    button.secondary:hover {
      box-shadow: 0 5px 14px rgba(0,0,0,0.6);
    }
    button:active {
      transform: translateY(0);
      box-shadow: 0 3px 8px rgba(47,140,255,0.3);
    }
    button.secondary:active {
      box-shadow: 0 2px 6px rgba(0,0,0,0.6);
    }

    /* 게임 화면 */
    #gameLayout {
      display: flex;
      justify-content: center;
      gap: 24px;
      margin-top: 10px;
    }
    .board-wrap {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 6px;
    }
    .board-title {
      font-size: 14px;
      color: #ddd;
    }
    canvas {
      background: #050505;
      border-radius: 8px;
      border: 1px solid #333;
    }
    .info-row {
      display: flex;
      justify-content: space-between;
      margin-top: 12px;
      font-size: 13px;
      color: #bbb;
    }
    .badge {
      display: inline-block;
      padding: 2px 8px;
      border-radius: 999px;
      font-size: 11px;
      border: 1px solid #444;
      background: #202020;
      margin-left: 4px;
    }
    #activeBoard {
      font-weight: 600;
      color: #2f8cff;
    }
    .score-label {
      font-size: 12px;
      color: #aaa;
    }

    /* 설정 화면 */
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 10px;
      font-size: 13px;
    }
    th, td {
      padding: 6px 4px;
      text-align: center;
    }
    th {
      border-bottom: 1px solid #333;
      color: #ccc;
      font-weight: 500;
    }
    tr:nth-child(even) {
      background: #202020;
    }
    .key-display {
      padding: 3px 8px;
      border-radius: 6px;
      background: #111;
      border: 1px solid #333;
      font-family: monospace;
      font-size: 12px;
      display: inline-block;
      min-width: 60px;
    }
    #rebindInfo {
      margin-top: 8px;
      font-size: 12px;
      color: #ffdd77;
      text-align: center;
      min-height: 18px;
    }
  </style>
</head>
<body>
<div id="app">
  <!-- 로비 화면 -->
  <div id="screen-lobby" class="screen active">
    <h1>듀얼 테트리스</h1>
    <div class="subtitle">왼쪽 &amp; 오른쪽 두 개의 테트리스를 동시에! 키는 설정 탭에서 자유롭게 변경 가능합니다.</div>
    <div class="btn-row">
      <button id="btn-start">게임 시작</button>
      <button class="secondary" id="btn-settings">설정</button>
    </div>
  </div>

  <!-- 게임 화면 -->
  <div id="screen-game" class="screen">
    <h2>플레이</h2>
    <div class="subtitle">왼쪽 / 오른쪽 보드를 동시에 조작해보세요. 스왑 키로 두 보드의 블록을 교환할 수 있습니다.</div>

    <div id="gameLayout">
      <div class="board-wrap">
        <div class="board-title">왼쪽 보드<span class="badge">기본: WASD</span></div>
        <canvas id="canvasLeft" width="200" height="400"></canvas>
        <div class="score-label">점수: <span id="scoreLeft">0</span></div>
        <div class="score-label" id="statusLeft"></div>
      </div>
      <div class="board-wrap">
        <div class="board-title">오른쪽 보드<span class="badge">기본: 방향키</span></div>
        <canvas id="canvasRight" width="200" height="400"></canvas>
        <div class="score-label">점수: <span id="scoreRight">0</span></div>
        <div class="score-label" id="statusRight"></div>
      </div>
    </div>

    <div class="info-row">
      <div>스왑 키: <span class="badge" id="swapKeyLabel">Enter</span></div>
      <div>현재 상태: <span id="gameStatus">진행 중</span></div>
    </div>

    <div class="btn-row">
      <button class="secondary" id="btn-back-from-game">로비로</button>
      <button class="secondary" id="btn-restart">재시작</button>
    </div>
  </div>

  <!-- 설정 화면 -->
  <div id="screen-settings" class="screen">
    <h2>키 설정</h2>
    <div class="subtitle">변경 버튼을 누른 뒤, 사용하고 싶은 키를 누르세요.</div>

    <h3 style="font-size:14px;margin-top:6px;">왼쪽 보드</h3>
    <table>
      <tr>
        <th>동작</th><th>현재 키</th><th>변경</th>
      </tr>
      <tr>
        <td>왼쪽 이동</td>
        <td><span id="left-left-key" class="key-display"></span></td>
        <td><button class="secondary" onclick="startRebind('left','left')">변경</button></td>
      </tr>
      <tr>
        <td>오른쪽 이동</td>
        <td><span id="left-right-key" class="key-display"></span></td>
        <td><button class="secondary" onclick="startRebind('left','right')">변경</button></td>
      </tr>
      <tr>
        <td>아래 이동</td>
        <td><span id="left-down-key" class="key-display"></span></td>
        <td><button class="secondary" onclick="startRebind('left','down')">변경</button></td>
      </tr>
      <tr>
        <td>회전</td>
        <td><span id="left-rotate-key" class="key-display"></span></td>
        <td><button class="secondary" onclick="startRebind('left','rotate')">변경</button></td>
      </tr>
    </table>

    <h3 style="font-size:14px;margin-top:12px;">오른쪽 보드</h3>
    <table>
      <tr>
        <th>동작</th><th>현재 키</th><th>변경</th>
      </tr>
      <tr>
        <td>왼쪽 이동</td>
        <td><span id="right-left-key" class="key-display"></span></td>
        <td><button class="secondary" onclick="startRebind('right','left')">변경</button></td>
      </tr>
      <tr>
        <td>오른쪽 이동</td>
        <td><span id="right-right-key" class="key-display"></span></td>
        <td><button class="secondary" onclick="startRebind('right','right')">변경</button></td>
      </tr>
      <tr>
        <td>아래 이동</td>
        <td><span id="right-down-key" class="key-display"></span></td>
        <td><button class="secondary" onclick="startRebind('right','down')">변경</button></td>
      </tr>
      <tr>
        <td>회전</td>
        <td><span id="right-rotate-key" class="key-display"></span></td>
        <td><button class="secondary" onclick="startRebind('right','rotate')">변경</button></td>
      </tr>
    </table>

    <h3 style="font-size:14px;margin-top:12px;">공통</h3>
    <table>
      <tr>
        <th>동작</th><th>현재 키</th><th>변경</th>
      </tr>
      <tr>
        <td>스왑 (두 보드 블록 교환)</td>
        <td><span id="global-swap-key" class="key-display"></span></td>
        <td><button class="secondary" onclick="startRebind('global','swap')">변경</button></td>
      </tr>
    </table>

    <div id="rebindInfo"></div>

    <div class="btn-row">
      <button class="secondary" id="btn-back-from-settings">로비로</button>
      <button id="btn-save-and-play">저장 후 게임 시작</button>
    </div>
  </div>
</div>

<script>
  // ===== 화면 전환 =====
  const screens = {
    lobby: document.getElementById('screen-lobby'),
    game: document.getElementById('screen-game'),
    settings: document.getElementById('screen-settings'),
  };

  function showScreen(name) {
    Object.values(screens).forEach(el => el.classList.remove('active'));
    screens[name].classList.add('active');
    currentScreen = name;
    if (name !== 'settings') {
      rebindTarget = null;
      document.getElementById('rebindInfo').textContent = '';
    }
  }

  document.getElementById('btn-start').onclick = () => {
    resetGames();
    showScreen('game');
  };
  document.getElementById('btn-settings').onclick = () => {
    showScreen('settings');
  };
  document.getElementById('btn-back-from-game').onclick = () => {
    showScreen('lobby');
  };
  document.getElementById('btn-back-from-settings').onclick = () => {
    showScreen('lobby');
  };
  document.getElementById('btn-save-and-play').onclick = () => {
    showScreen('game');
    resetGames();
  };
  document.getElementById('btn-restart').onclick = () => {
    resetGames();
  };

  // ===== 키 바인딩 =====
  const keyBindings = {
    left: {
      left: 'a',
      right: 'd',
      down: 's',
      rotate: 'w',
    },
    right: {
      left: 'ArrowLeft',
      right: 'ArrowRight',
      down: 'ArrowDown',
      rotate: 'ArrowUp',
    },
    global: {
      swap: 'Enter',
    },
  };

  let rebindTarget = null; // { board: 'left'|'right'|'global', action: 'left'|'right'|'down'|'rotate'|'swap' }

  function keyToLabel(key) {
    if (key.startsWith('Arrow')) return key.replace('Arrow', '방향 ');
    if (key === ' ') return 'Space';
    if (key === 'Enter') return 'Enter';
    return key.length === 1 ? key.toUpperCase() : key;
  }

  function updateBindingsUI() {
    document.getElementById('left-left-key').textContent = keyToLabel(keyBindings.left.left);
    document.getElementById('left-right-key').textContent = keyToLabel(keyBindings.left.right);
    document.getElementById('left-down-key').textContent = keyToLabel(keyBindings.left.down);
    document.getElementById('left-rotate-key').textContent = keyToLabel(keyBindings.left.rotate);

    document.getElementById('right-left-key').textContent = keyToLabel(keyBindings.right.left);
    document.getElementById('right-right-key').textContent = keyToLabel(keyBindings.right.right);
    document.getElementById('right-down-key').textContent = keyToLabel(keyBindings.right.down);
    document.getElementById('right-rotate-key').textContent = keyToLabel(keyBindings.right.rotate);

    document.getElementById('global-swap-key').textContent = keyToLabel(keyBindings.global.swap);
    document.getElementById('swapKeyLabel').textContent = keyToLabel(keyBindings.global.swap);
  }

  function startRebind(board, action) {
    rebindTarget = { board, action };
    document.getElementById('rebindInfo').textContent = '변경할 키를 누르세요...';
  }

  function handleRebindKey(e) {
    if (!rebindTarget) return;
    e.preventDefault();
    const key = e.key;
    if (!key) return;
    if (rebindTarget.board === 'global') {
      keyBindings.global[rebindTarget.action] = key;
    } else {
      keyBindings[rebindTarget.board][rebindTarget.action] =
        key.length === 1 ? key.toLowerCase() : key;
    }
    rebindTarget = null;
    updateBindingsUI();
    document.getElementById('rebindInfo').textContent = '변경 완료!';
  }

  updateBindingsUI();

  // ===== 테트리스 로직 =====
  const COLS = 10;
  const ROWS = 20;
  const BLOCK_SIZE = 20;

  const canvasLeft = document.getElementById('canvasLeft');
  const canvasRight = document.getElementById('canvasRight');
  const ctxLeft = canvasLeft.getContext('2d');
  const ctxRight = canvasRight.getContext('2d');

  const tetrominoes = [
    { // I
      shape: [
        [0,0,0,0],
        [1,1,1,1],
        [0,0,0,0],
        [0,0,0,0]
      ],
      color: '#00f0f0'
    },
    { // J
      shape: [
        [1,0,0],
        [1,1,1],
        [0,0,0]
      ],
      color: '#0000f0'
    },
    { // L
      shape: [
        [0,0,1],
        [1,1,1],
        [0,0,0]
      ],
      color: '#f0a000'
    },
    { // O
      shape: [
        [1,1],
        [1,1]
      ],
      color: '#f0f000'
    },
    { // S
      shape: [
        [0,1,1],
        [1,1,0],
        [0,0,0]
      ],
      color: '#00f000'
    },
    { // T
      shape: [
        [0,1,0],
        [1,1,1],
        [0,0,0]
      ],
      color: '#a000f0'
    },
    { // Z
      shape: [
        [1,1,0],
        [0,1,1],
        [0,0,0]
      ],
      color: '#f00000'
    },
  ];

  function createBoard() {
    return Array.from({ length: ROWS }, () => Array(COLS).fill(0));
  }

  function randomPiece() {
    const t = tetrominoes[Math.floor(Math.random() * tetrominoes.length)];
    return {
      shape: t.shape.map(row => row.slice()),
      color: t.color,
      x: 3,
      y: 0,
    };
  }

  function clonePiece(p) {
    return {
      shape: p.shape.map(r => r.slice()),
      color: p.color,
      x: p.x,
      y: p.y,
    };
  }

  function rotateMatrix(matrix) {
    const N = matrix.length;
    const result = Array.from({ length: N }, () => Array(N).fill(0));
    for (let y = 0; y < N; y++) {
      for (let x = 0; x < N; x++) {
        result[x][N - 1 - y] = matrix[y][x];
      }
    }
    return result;
  }

  function collide(board, piece) {
    const m = piece.shape;
    const o = { x: piece.x, y: piece.y };
    for (let y = 0; y < m.length; y++) {
      for (let x = 0; x < m[y].length; x++) {
        if (m[y][x]) {
          const bx = x + o.x;
          const by = y + o.y;
          if (bx < 0 || bx >= COLS || by >= ROWS) return true;
          if (by >= 0 && board[by][bx]) return true;
        }
      }
    }
    return false;
  }

  function merge(board, piece) {
    const m = piece.shape;
    for (let y = 0; y < m.length; y++) {
      for (let x = 0; x < m[y].length; x++) {
        if (m[y][x]) {
          const bx = x + piece.x;
          const by = y + piece.y;
          if (by >= 0 && by < ROWS && bx >= 0 && bx < COLS) {
            board[by][bx] = piece.color;
          }
        }
      }
    }
  }

  function sweep(board) {
    let lines = 0;
    for (let y = ROWS - 1; y >= 0; ) {
      if (board[y].every(v => v !== 0)) {
        const row = board.splice(y, 1)[0].fill(0);
        board.unshift(row);
        lines++;
      } else {
        y--;
      }
    }
    return lines;
  }

  function createGameState() {
    return {
      board: createBoard(),
      piece: randomPiece(),
      nextPiece: randomPiece(),
      dropCounter: 0,
      dropInterval: 700,
      score: 0,
      gameOver: false,
    };
  }

  let leftGame = createGameState();
  let rightGame = createGameState();

  function resetGames() {
    leftGame = createGameState();
    rightGame = createGameState();
    document.getElementById('scoreLeft').textContent = '0';
    document.getElementById('scoreRight').textContent = '0';
    document.getElementById('statusLeft').textContent = '';
    document.getElementById('statusRight').textContent = '';
    document.getElementById('gameStatus').textContent = '진행 중';
  }

  function playerDrop(game) {
    if (game.gameOver) return;
    game.piece.y++;
    if (collide(game.board, game.piece)) {
      game.piece.y--;
      merge(game.board, game.piece);
      const lines = sweep(game.board);
      if (lines > 0) {
        game.score += lines * 100;
      }
      game.piece = game.nextPiece;
      game.nextPiece = randomPiece();
      if (collide(game.board, game.piece)) {
        game.gameOver = true;
      }
    }
    game.dropCounter = 0;
  }

  function playerMove(game, dir) {
    if (game.gameOver) return;
    game.piece.x += dir;
    if (collide(game.board, game.piece)) {
      game.piece.x -= dir;
    }
  }

  function playerRotate(game) {
    if (game.gameOver) return;
    const old = game.piece.shape;
    const rotated = rotateMatrix(old);
    const oldX = game.piece.x;
    game.piece.shape = rotated;

    let offset = 1;
    while (collide(game.board, game.piece)) {
      game.piece.x += offset;
      offset = -(offset + (offset > 0 ? 1 : -1));
      if (offset > 2 || offset < -2) {
        game.piece.shape = old;
        game.piece.x = oldX;
        return;
      }
    }
  }

  function drawBoard(ctx, game) {
    ctx.clearRect(0, 0, canvasLeft.width, canvasLeft.height);
    // 배경 격자
    ctx.fillStyle = '#050505';
    ctx.fillRect(0, 0, COLS * BLOCK_SIZE, ROWS * BLOCK_SIZE);
    ctx.strokeStyle = '#222';
    ctx.lineWidth = 0.5;
    for (let x = 0; x <= COLS; x++) {
      ctx.beginPath();
      ctx.moveTo(x * BLOCK_SIZE + 0.5, 0);
      ctx.lineTo(x * BLOCK_SIZE + 0.5, ROWS * BLOCK_SIZE);
      ctx.stroke();
    }
    for (let y = 0; y <= ROWS; y++) {
      ctx.beginPath();
      ctx.moveTo(0, y * BLOCK_SIZE + 0.5);
      ctx.lineTo(COLS * BLOCK_SIZE, y * BLOCK_SIZE + 0.5);
      ctx.stroke();
    }

    // 고정 블록
    for (let y = 0; y < ROWS; y++) {
      for (let x = 0; x < COLS; x++) {
        const cell = game.board[y][x];
        if (cell) {
          ctx.fillStyle = cell;
          ctx.fillRect(
            x * BLOCK_SIZE + 1,
            y * BLOCK_SIZE + 1,
            BLOCK_SIZE - 2,
            BLOCK_SIZE - 2
          );
        }
      }
    }

    // 현재 피스
    const m = game.piece.shape;
    ctx.fillStyle = game.piece.color;
    for (let y = 0; y < m.length; y++) {
      for (let x = 0; x < m[y].length; x++) {
        if (m[y][x]) {
          const bx = x + game.piece.x;
          const by = y + game.piece.y;
          if (by >= 0) {
            ctx.fillRect(
              bx * BLOCK_SIZE + 1,
              by * BLOCK_SIZE + 1,
              BLOCK_SIZE - 2,
              BLOCK_SIZE - 2
            );
          }
        }
      }
    }
  }

  function swapPieces() {
    if (leftGame.gameOver || rightGame.gameOver) return;
    const leftClone = clonePiece(leftGame.piece);
    const rightClone = clonePiece(rightGame.piece);

    // 위치 재조정 없이 그대로 시도
    leftGame.piece = rightClone;
    rightGame.piece = leftClone;

    // 충돌나면 롤백
    if (collide(leftGame.board, leftGame.piece) ||
        collide(rightGame.board, rightGame.piece)) {
      leftGame.piece = leftClone;
      rightGame.piece = rightClone;
    }
  }

  let lastTime = 0;
  let currentScreen = 'lobby';

  function update(delta) {
    [leftGame, rightGame].forEach(game => {
      if (game.gameOver) return;
      game.dropCounter += delta;
      if (game.dropCounter > game.dropInterval) {
        playerDrop(game);
      }
    });

    document.getElementById('scoreLeft').textContent = leftGame.score;
    document.getElementById('scoreRight').textContent = rightGame.score;

    if (leftGame.gameOver) {
      document.getElementById('statusLeft').textContent = '게임 오버';
    }
    if (rightGame.gameOver) {
      document.getElementById('statusRight').textContent = '게임 오버';
    }

    if (leftGame.gameOver && rightGame.gameOver) {
      document.getElementById('gameStatus').textContent = '양쪽 다 게임 오버';
    }
  }

  function draw() {
    drawBoard(ctxLeft, leftGame);
    drawBoard(ctxRight, rightGame);
  }

  function loop(timestamp) {
    const delta = timestamp - lastTime;
    lastTime = timestamp;
    if (currentScreen === 'game') {
      update(delta);
      draw();
    }
    requestAnimationFrame(loop);
  }
  requestAnimationFrame(loop);

  // ===== 키 입력 처리 =====
  document.addEventListener('keydown', (e) => {
    if (currentScreen === 'settings') {
      handleRebindKey(e);
      return;
    }
    if (currentScreen !== 'game') return;

    const key = e.key.length === 1 ? e.key.toLowerCase() : e.key;

    // 스왑
    if (key === (keyBindings.global.swap.length === 1
      ? keyBindings.global.swap.toLowerCase()
      : keyBindings.global.swap)) {
      e.preventDefault();
      swapPieces();
      return;
    }

    // 왼쪽 보드
    const leftBind = keyBindings.left;
    if (key === (leftBind.left.length === 1 ? leftBind.left.toLowerCase() : leftBind.left)) {
      e.preventDefault();
      playerMove(leftGame, -1);
    } else if (key === (leftBind.right.length === 1 ? leftBind.right.toLowerCase() : leftBind.right)) {
      e.preventDefault();
      playerMove(leftGame, 1);
    } else if (key === (leftBind.down.length === 1 ? leftBind.down.toLowerCase() : leftBind.down)) {
      e.preventDefault();
      playerDrop(leftGame);
    } else if (key === (leftBind.rotate.length === 1 ? leftBind.rotate.toLowerCase() : leftBind.rotate)) {
      e.preventDefault();
      playerRotate(leftGame);
    }

    // 오른쪽 보드
    const rightBind = keyBindings.right;
    if (key === (rightBind.left.length === 1 ? rightBind.left.toLowerCase() : rightBind.left)) {
      e.preventDefault();
      playerMove(rightGame, -1);
    } else if (key === (rightBind.right.length === 1 ? rightBind.right.toLowerCase() : rightBind.right)) {
      e.preventDefault();
      playerMove(rightGame, 1);
    } else if (key === (rightBind.down.length === 1 ? rightBind.down.toLowerCase() : rightBind.down)) {
      e.preventDefault();
      playerDrop(rightGame);
    } else if (key === (rightBind.rotate.length === 1 ? rightBind.rotate.toLowerCase() : rightBind.rotate)) {
      e.preventDefault();
      playerRotate(rightGame);
    }
  });
</script>
</body>
</html>
