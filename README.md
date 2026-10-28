# Brick Breaker

A neon-styled Brick Breaker game built with plain **HTML, CSS and JavaScript** (HTML5 Canvas). No frameworks, no build step: open `index.html` in a browser and play.

## Features

- Aim the ball before launch (A / D keys) with a dashed aim line
- Paddle that steers the ball based on where it hits (left edge, centre, right edge)
- Circle-vs-rectangle collision for bricks and paddle
- Score, lives (hearts) and win / game over screens
- Pause / resume (P or Space), auto-pause when the window loses focus
- How to Play card, Start / Play Again / Resume buttons
- Gradient bricks per row, glowing paddle and ball, space-themed UI

## Controls

| Key | Action |
|---|---|
| Left / Right arrows | Move the paddle |
| A / D | Aim the ball (before launch) |
| Space | Launch the ball / pause / resume / play again |
| P | Pause / resume |

## Project structure

```
.
├── index.html                      # page, styles and game script
├── brick_breaker_icon.png          # logo and favicon
├── brick_breaker_background.jpg    # page background
├── 1646656079PixelArt-Heart-1.svg  # heart icon for lives
└── README.md
```

## How to run

1. Put all files in one folder.
2. Open `index.html` in a browser (or use a local server such as the VS Code Live Server extension).

## How it works

### The game loop

The game runs through `requestAnimationFrame`. Every frame does three things:

1. **Handle input**: read the key flags (`leftPressed`, `rightPressed`, `leftAngle`, `rightAngle`).
2. **Update state**: move the ball, check collisions, update score and lives.
3. **Draw**: clear the canvas, then draw bricks, paddle, ball and the aim line.

Only one loop must ever run. `animId` stores the pending frame so `resetGame()` can cancel it before restarting.

### Game states

A single `gameState` variable drives everything:

| State | Meaning |
|---|---|
| `start` | Ball rests on the paddle, player aims |
| `playing` | Ball moving, collisions active |
| `paused` | Everything frozen, ball still drawn |
| `won` | All bricks broken, loop stops |
| `lost` | No lives left, loop stops |

A second variable, `panel` (`main` or `howto`), decides which card shows inside the overlay.

### Key ideas and formulas

- **Movement**: `x += dx`, `y += dy` each frame.
- **Wall bounce**: flip `dx` or `dy`, using the ball's edge (`x ± radius`), not its centre.
- **Clamp**: `Math.max(min, Math.min(value, max))` keeps the paddle inside the canvas.
- **Circle vs rectangle**: find the closest point on the rectangle to the ball's centre by clamping, then test `(x - cx)² + (y - cy)² < radius²`.
- **Which side was hit**: compare the ball's previous position (`y - dy`) with the brick. If it came from above or below, flip `dy`, otherwise flip `dx`.
- **Paddle bounce angle**: `hit = (ballX - paddleCentre) / (paddleWidth / 2)` gives -1 to +1. Then `angle = hit * maxAngle`, `dx = speed * sin(angle)`, `dy = -speed * cos(angle)`. The speed is kept constant with `Math.sqrt(dx*dx + dy*dy)`.
- **Brick grid**: `x = col * (brickWidth + padding) + offsetLeft`, same idea for `y`. The grid is centred with `(canvasWidth - gridWidth) / 2`.

## Concepts you need to build this

- JavaScript basics: variables, functions, loops, arrays, objects, events
- Canvas 2D API: `fillRect`, `arc`, `roundRect`, gradients, `shadowBlur`, `save()` / `restore()`
- 2D coordinates (y grows downward) and basic collision maths
- Keyboard events (`keydown` / `keyup`, `e.repeat`) and handling held keys with boolean flags
- DOM updates: `textContent`, `classList.toggle`
- CSS: variables, flexbox, `position: absolute` overlays, gradients, box-shadow glow

## Lessons learned

- A canvas has a drawing size and a display size. Set `width` / `height` as attributes to avoid blur.
- `backdrop-filter: blur()` over an animated canvas caused uneven frame rate, so it was removed.
- Calling `move()` twice starts two loops and doubles the speed. Keep one loop and cancel the old frame before restarting.
- Canvas state (`shadowBlur`, `lineDash`, `fillStyle`) persists. Wrap effects in `ctx.save()` / `ctx.restore()`.
- Methods like `Math.min` return a value. Assign the result back.
- Handling `blur` on the window avoids stuck keys.

## Possible improvements

- Levels with different brick layouts and rising speed (the HUD already has a Level slot)
- Sound effects
- High score saved with `localStorage`
- Delta time so speed is identical on 60 Hz and 144 Hz screens
- Settings (ball speed, paddle size, show aim line)
- Global leaderboard with Node.js / Express and MySQL

## Tech stack

HTML5, CSS3, JavaScript (ES6), Canvas API. Fonts: Orbitron and Poppins (Google Fonts).
