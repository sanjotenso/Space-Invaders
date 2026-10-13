# 🚀 Space Invaders 2.0

A feature-rich, arcade-style Space Invaders remake built with **Python + Pygame**, with a full authentication system, persistent leaderboards, and a polished neon-noir UI. This is a **weekly commit project** — features and improvements are added incrementally each week.

---

## ✨ Feature Overview

### 🎮 Core Gameplay

| Feature | Details |
|---|---|
| **Classic enemy formation** | Enemies drift side-to-side in a deliberate sweep and reverse at screen edges |
| **Enemy fire** | A random live enemy fires a bolt every 90 frames; threat scales with wave size |
| **Hold-to-fire** | Holding SPACE continuously shoots with a controlled cooldown |
| **Smooth motion** | Frame-rate-scaled movement and animation, rendered at up to 120 FPS |
| **Energy bolts** | Distinct cached neon spear bolts for player and enemy fire |
| **Scaling waves** | Each cleared wave adds one more enemy to the next formation |
| **Collision detection** | Per-frame rect collision — player bullets ↔ enemies and enemy bullets ↔ player |
| **Blink invincibility** | After taking a hit the ship can still move while blinking and remains immune for at least 3 seconds |
| **Heart drops** | Collectible hearts occasionally fall on their own when you have a lost life; each restores one life, up to the 3-heart maximum |
| **Multishot power-up** | Collect a falling 3X power-up for 10 seconds of three-way firing |

---

### 🚀 Wave Progression

New enemy formations spawn immediately when the current wave is cleared. The wave
counter stays visible in the compact in-game HUD, without an interrupting splash.

---

### ❤️ Lives System

- Player starts with **3 hearts** per game
- Compact hearts are drawn using a **mathematically correct parametric heart curve** (sampled at 80 points)
- Live hearts glow vivid red with a soft bloom; lost hearts render as dark outlines
- Losing all hearts triggers game over, saves the score, and loads the leaderboard

---

### 🔫 Procedural Bullet Visuals

Bullets are drawn entirely in code — no image files used:

**Player bolts (cyan / white)**
- Multi-layer bloom ellipses in teal/cyan
- Bright white core with a glowing centre spine
- Sparkle tip at the leading edge

**Enemy bolts (red / orange)**
- Pulsing orange bloom that breathes using a sine wave each frame
- Hot red core with a warm white spine
- Distinct silhouette so friendly vs. enemy bullets are instantly readable

---

### 🌌 Visual Polish & Aesthetics

- **Neon-noir colour palette** — dark charcoal backgrounds with plum, teal, and gold accents
- **Animated galaxy backdrop** — 70 stars of varying size drift slowly; soft nebula ellipses glow behind them
- **Glassmorphism panels** — cards feature layered drop shadows, inner gradient, neon top-edge accent, and frosted highlight
- **Glow buttons** — every button has outer glow rings, a top shimmer, and a specular highlight line
- **Glow input fields** — focused text fields emit a soft neon border glow and a blinking cursor
- **Glow titles** — screen titles rendered with offset transparent copies for a luminous halo effect
- **Decorative dividers** — colour-matched horizontal accent lines separate UI sections

---

### 📊 HUD (In-Game Heads-Up Display)

| Chip | Position | Contents |
|---|---|---|
| **Score** | Top-left | Live score in retro pixel font, blue glow chip |
| **Best** | Below score | Personal best in gold retro pixel font |
| **Wave** | Top-centre | Current wave number in a teal glassy pill |
| **Lives** | Top-right | Parametric heart icons (filled = alive, dark = lost) |

Below the lives chip:
- **Controls hint** — shows the active control scheme (arrow keys or WASD)
- **Difficulty badge** — colour-coded current difficulty label

---

### 📱 Mobile / Touchscreen Buttons

- **← Left / → Right** — glassy blue directional buttons with polygon triangle arrows
- **FIRE** — vivid crimson button, hold to keep firing
- All buttons feature outer glow rings, top shimmer, specular highlight, and coloured border
- Toggle touch buttons and sound, and adjust volume, from the in-game settings opened with **ESC**

### 🔊 Sound

- Procedural sound effects play when firing and taking damage
- Sound enablement and volume are saved in `settings.json`

---

### 🔐 Authentication System

| Feature | Details |
|---|---|
| **Registration** | Username (3+ chars) + password (4+ chars) + confirmation; duplicate check |
| **Login** | SHA-256 password hashing; distinct errors for "user not found" vs "wrong password" |
| **Auto-login** | Successful registration logs the player in immediately |
| **Background auth** | All DB calls run on a background thread — UI never freezes |
| **DB offline banner** | Clear error shown immediately when MySQL is unreachable |
| **Field navigation** | Tab to cycle fields; Enter to submit; Ctrl+Backspace to clear; Ctrl+V to paste |

---

### 👤 Player Profiles

Saved to the database and restored on every login:

| Setting | Options |
|---|---|
| **Spaceship** | Verdant Warden, Crimson Reaver, Solar Vanguard, Tidal Phantom, Nova Striker, or Storm Sentinel |
| **Control scheme** | Arrow Keys or WASD |
| **Difficulty** | Easy (3 enemies/wave) · Normal (5) · Hard (8) |

- Ship preview rendered live as you change spacecraft
- Username can be renamed from the name-input screen; DB record updated automatically

---

### 🏆 Leaderboard

- **Top 10 all-time best scores** — one entry per player (personal best only)
- Rank medals: Gold · Silver · Bronze for positions 1-3
- Your own row highlighted with a green tint
- Accessible from the login screen (no login required), the player dashboard, and the game over screen
- Score date shown in a fourth column

---

### 🎮 Game Over Screen

- Animated "GAME OVER" title in glowing red
- Final score in large retro pixel font
- **"New Personal Best!"** gold badge if the score beats your record
- Inline top-5 leaderboard with your row highlighted
- **Full Leaderboard** and **Play Again** buttons

---

## 🗄️ Database Schema

MySQL backend (`space_invaders_bula`) with a 3-second connection timeout:

```sql
CREATE TABLE IF NOT EXISTS players (
    id              INT AUTO_INCREMENT PRIMARY KEY,
    username        VARCHAR(50) UNIQUE NOT NULL,
    password_hash   VARCHAR(64) DEFAULT NULL,
    ship_color      VARCHAR(20) DEFAULT 'green',
    player_title    VARCHAR(50) DEFAULT 'Space Cadet',
    control_scheme  VARCHAR(10) DEFAULT 'arrows',
    difficulty      VARCHAR(10) DEFAULT 'normal',
    created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE IF NOT EXISTS scores (
    id          INT AUTO_INCREMENT PRIMARY KEY,
    player_id   INT,
    score       INT NOT NULL,
    achieved_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (player_id) REFERENCES players(id) ON DELETE CASCADE
);
```

---

## 🗂️ Project Structure

```
Space-Invader-2.0/
├── main.py                   # Entry point — window setup & game loop
├── database_setup.py         # One-time DB schema initialisation
├── requirements.txt
├── settings.json
├── assets/
│   ├── myship.png            # Player ship sprite
│   ├── enemyship.png         # Enemy ship sprite
│   └── bullet.png            # (legacy, replaced by procedural drawing)
├── entities/
│   ├── player.py             # Player ship, movement, selectable ship sprites
│   ├── enemy.py              # Enemy AI, formation logic, dive animation
│   ├── bullet.py             # Procedural plasma bolt rendering
│   └── button.py             # Mobile on-screen touch buttons
└── managers/
    ├── game_manager.py       # All game states, HUD, drawing, wave logic
    ├── db_manager.py         # MySQL CRUD — auth, profiles, scores, leaderboard
    └── settings_manager.py   # JSON-backed settings persistence
```

---

## 🕹️ Controls

| Action | Arrow Keys scheme | WASD scheme |
|---|---|---|
| Move left | Left Arrow | A |
| Move right | Right Arrow | D |
| Fire | SPACE (hold) | SPACE (hold) |

---

## ⚙️ Setup Instructions

### 1. Create a Virtual Environment

```bash
python -m venv .venv
```

### 2. Activate It

**PowerShell / CMD:**
```bash
.\.venv\Scripts\activate
```
**bash / Git Bash:**
```bash
source .venv/Scripts/activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Set Up the Database

Make sure MySQL is running locally (default: `root` with no password), then:

```bash
python database_setup.py
```

### 5. Run the Game

```bash
python main.py
```

Enjoy playing!

---

## 📅 Changelog

### Latest — Wave Flow & HUD Polish
- Next enemy formation spawns immediately after the current wave is cleared
- Wave number chip added to top-centre of the in-game HUD
- Procedural plasma bullet visuals (cyan player bolts, pulsing orange enemy bolts)
- Smaller parametric hearts for a cleaner lives HUD (80-point smooth heart)

### Auth, UI & Visual Overhaul
- Full login / register flow with SHA-256 password hashing
- Background-threaded DB calls — UI never freezes on slow connections
- "DB offline" error displayed immediately when MySQL is unreachable
- Glassmorphism panel system, glow buttons, glow input fields, glow titles
- Animated galaxy backdrop (nebula ellipses + drifting star field)
- Mobile on-screen directional and fire buttons with glassy styling
- Neon-noir colour palette (charcoal, plum, teal, gold)
- Retro pixel-font score rendering via surface scaling
- Personal best tracking and "New Personal Best!" badge on game over
- Full leaderboard (Top 10) accessible without logging in
- Player profile: spaceship, control scheme, difficulty — all persisted to DB

### Arcade Combat & Wave Tuning
- Hold-to-fire with cooldown; classic formation side-to-side movement
- 3-heart lives system with blink invincibility after each hit
- Galaxy-inspired background with soft nebula glow and drifting stars
- Enemy ships scaled down for better formation readability
