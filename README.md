# 🏚️ The House That Remembers

> *"Each window holds a memory."*

A cinematic, interactive storytelling experience 

A single HTML file. No framework. No build step. No backend. Open it and walk inside a memory.

---

## 🎬 What It Is

You arrive at a house at dusk. Four windows glow from inside. Each one is a frozen moment from a family's final day. Click a window — the world transitions — and you step into a 3D room in first-person.

You are not playing a game. You are walking inside a memory.

Every room has objects. Every object has a story. Click something — and the room responds.

---

## 🚀 Live Demo

https://housethatremembers.netlify.app

## 🗂️ Project Structure

```
house-that-remembers-v3.html   ← Entire experience in one file
README.md
```

Everything — HTML, CSS, JavaScript, Three.js scenes, Web Audio, canvas textures — lives in a single self-contained file. Total load time under 5 seconds. Zero external assets.

---

## 🎮 How to Experience It

1. Open the file in a browser
2. Click anywhere to start audio
3. Hover over a glowing window — a tooltip appears
4. Click the window — you enter the room
5. Move your mouse slowly to look around
6. Approach objects — your cursor glows when something is interactive
7. Click the object — something happens
8. Click **← Leave** to return to the exterior
9. Explore all 4 rooms to unlock the basement

---

## 🏠 The Rooms

### 🍰 The Kitchen
Warm amber light. A birthday cake sits on the dining table — candles unlit, one chair pulled back suddenly. A knife rests on the cutting board. A pot on the stove. Jars on the counter.

**Interaction:** Click the cake → candles light one by one → *Happy Birthday* plays on the piano → cuts off mid-note → silence.

---

### 🖍️ The Child's Room
Soft yellow lamplight. Toys scattered on the floor — wooden blocks, a red ball, a toy train. A teddy bear with asymmetric ears sits watching. A crayon drawing is pinned to the back wall.

**Interaction 1:** Click the drawing → camera moves close → a child's handprint fades in on the paper → slowly disappears.

**Interaction 2:** Click the window → glass fogs → four small handprints appear on the glass from outside → a lullaby plays and cuts abruptly.

---

### 📖 The Living Room
Cool blue moonlight through a rain-streaked window. A sofa. An armchair with a book lying open on it, face-down. Two teacups on the coffee table — one empty, one with steam rising. A clock ticks on the wall. A television flickers.

**Interaction 1 (book):** Click the open book → a fullscreen letter overlay appears, rendered in handwriting on aged paper. Click anywhere to close — the letter fades back.

**Interaction 2 (window):** Click the window → moonlight flickers → rain intensifies → a figure silhouette appears briefly in the glass, then vanishes.

**Interaction 3 (TV):** Click the television → static burst, rapid channel flicker → screen goes dark.

---

### 🚪 The Attic
Dusty. A bare bulb hangs flickering. An old wardrobe stands against the left wall — three everyday clothes hang inside on their hangers. An open suitcase on the floor. Spilled photographs. A wooden box with a letter leaning against it.

**Interaction 1 (wardrobe):** Click the wardrobe → camera pushes in → doors swing open slowly → interior light fills → clothes revealed.

**Interaction 2 (photographs):** Click the spilled photos → a fullscreen letter overlay opens immediately — the same aged handwritten letter, readable in full.

---

### 🔮 Basement *(unlocks after all 4 rooms)*
A small glow appears at the base of the house exterior once all four memories are collected. Click it to enter the final room.

---

## 🧠 Architecture

### State Machine
```
EXTERIOR → ZOOMING → IN_ROOM → LEAVING → EXTERIOR
```
All user input is gated between states — no broken transitions, no accidental double-triggers.

### Core Systems

| System | Role |
|--------|------|
| `MemorySystem` | Tracks which rooms have been interacted with, shows cinematic feedback text, unlocks the basement, persists via `localStorage` |
| `SceneManager` | Handles room entry/exit, fade transitions, HUD show/hide |
| `RoomEngine` | Three.js renderer, camera controller, raycasting, room builders, event functions |
| `AudioManager` | All Web Audio API — room ambiences, event sounds, generative music |

### Memory System
Each room has one primary interaction. When it completes:
- A cinematic text fades in with a room-specific message (*"This room remembers / the last birthday"*)
- A dot in the top-right corner lights up amber
- After all 4 rooms — the basement hint appears on the house exterior

### Raycasting
Every interactive object has an invisible `BoxGeometry` hit mesh with `MeshBasicMaterial({ visible: false, side: THREE.DoubleSide })`. The raycaster builds a `Map` from child mesh → parent interactable so any geometry inside a group reliably triggers the correct event.

### Camera
Soft mouse-look: mouse X/Y position gently rotates camera within limits (±38° horizontal, ±16° vertical), lerped at `0.028` damping. Camera dollies toward objects during events at `0.022` lerp — slow, cinematic, never instant.

---

## 🎨 Visual Style

**PS1 / PSX Era Cozy 3D** — inspired by early console games. Not horror. Warm, intimate, slightly unsettling.

- `MeshLambertMaterial` flat shading throughout — no PBR
- `antialias: false` — intentional pixel feel
- `devicePixelRatio` capped at 1.5 — performance + aesthetic
- `THREE.BasicShadowMap` — soft low-fi shadows
- Per-room fog (`THREE.Fog`) — dark corners, no visible seams
- Each room has a completely different dominant colour temperature

| Room | Key Light | Mood |
|------|-----------|------|
| Kitchen | `#ffaa44` warm amber | Nostalgic, domestic |
| Child's Room | `#ffdd88` soft yellow | Innocent, slightly sad |
| Living Room | `#6688bb` cool blue moonlight | Quiet, heavy |
| Attic | `#ddaa44` flickering gold | Dusty, weighted |

---

## 🔊 Audio

All audio is generated procedurally via the **Web Audio API**. Zero audio files. Zero downloads.

| Sound | Method |
|-------|--------|
| Exterior rain | White noise → lowpass filter |
| Wind | Bandpass noise + LFO modulation |
| Bass drone | Sine oscillator, `42Hz` |
| Birthday melody | Triangle wave oscillators, C major |
| Candle lights | Arpeggiated sine tones |
| Lullaby | Pentatonic sine sequence |
| Book open | Filtered noise burst |
| Creak | Bandpass noise, short decay |
| Piano note | Sine + exponential decay |
| Room ambiences | Per-room unique drone frequency |

---

## 🖼️ Letter Rendering

Handwritten letters are rendered via **`CanvasTexture`** — no fonts, no DOM, no SVG.

Each letter canvas includes:
- Warm aged paper background (`#d9ca9a`)
- Procedural paper grain (800+ random micro-rects)
- Faint ruled lines
- `Georgia` italic text at slight random rotation per line
- Ink pressure variation via `globalAlpha` randomisation
- Coffee stain radial gradient
- Applied as a `THREE.CanvasTexture` to a `PlaneGeometry` mesh, or shown as a fullscreen overlay via `canvas.toDataURL()`

**Attic letter content:**
> *I heard the door again last night. But no one was there. You said you would come back. I kept your chair by the fire. The house still knows your step. I am still waiting. — M*

**Living room letter content:**
> *The chair is still warm on his side. I do not move it. I keep the fire going. He will know the way back by the smoke. I still set two cups. Every morning. The window fogs. I trace his name. It disappears. — always*

---

## ⚙️ Tech Stack

| Technology | Purpose | Cost |
|-----------|---------|------|
| **Three.js r128** | 3D rooms, geometry, lighting, raycasting | Free (CDN) |
| **Web Audio API** | All sound generation | Free (browser built-in) |
| **Canvas API** | Letter texture rendering | Free (browser built-in) |
| **localStorage** | Memory + visited room persistence | Free (browser built-in) |
| **CSS / SVG** | 2D exterior house, sky, animations | Free |
| **Google Fonts** | Crimson Text (italic), Special Elite | Free (CDN) |

**External dependencies: 1** (Three.js CDN)
**Audio files: 0**
**Image files: 0**
**Backend: 0**
**Build step: 0**

---

## 📱 Mobile Support

- Touch drag → mouse-look equivalent
- Tap → click for object interaction
- Responsive exterior SVG scales to all screen sizes
- `devicePixelRatio` cap keeps mobile performance smooth
- Landscape orientation recommended for room viewing

---

## 🔑 Key Design Rules

1. **No UI buttons inside rooms** — everything is diegetic
2. **No instant anything** — every interaction has a minimum 1.5s lead-in
3. **Camera never teleports** — always lerps with damping
4. **Silence is audio design** — most rooms are uncomfortably quiet
5. **One focus object per room** — judges never wonder "what do I click"
6. **Events are one-time per session** — re-clicking after an event does nothing

*"Not like a game, but like stepping into a moment that already happened."* 
