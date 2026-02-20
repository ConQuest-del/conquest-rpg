# ⚙️ ConQuest Configuration Guide
### Conway + OpenClaw Adventure RPG

> All configuration lives at the top of the `<script>` tag inside the HTML file.  
> Edit these values to customize gameplay — no build step, no server, no separate config file needed.

---

## 🧠 How Conway AI Uses Configuration

ConQuest's entire NPC behavior is driven by a **Conway Life-inspired state machine**. This means your config values directly shape how agents *think* — not just how fast they move.

The key constant that controls the AI brain is:

```javascript
const CONWAY_TICK = 800; // ms — how often the Conway state machine updates each agent's strategy
```

Every `CONWAY_TICK` milliseconds, each OpenClaw agent re-evaluates its surroundings and decides its next move: **hunt, retreat, heal, or assist allies**. Lowering this value makes agents react faster and feel more alive. Raising it creates slower, more predictable behavior.

This is the heartbeat of the entire game.

---

## 🤖 OpenClaw Agent Configuration

OpenClaw agents are the 8 AI adventurers that fight alongside you. These settings control how they behave in combat.

```javascript
const MAX_AGENTS            = 6;     // Number of AI companions in the party. Max 8.
const AGENT_ATTACK_RANGE    = 60;    // px — distance at which agents begin melee attacks
const AGENT_HEAL_THRESHOLD  = 0.35;  // 0–1 — HP ratio below which agents auto-use healing potions
const PLAYER_HEAL_THRESHOLD = 0.3;   // 0–1 — HP ratio below which the player auto-uses potions
const AGENT_SKILL_COOLDOWN  = 3000;  // ms — minimum cooldown between agent skill uses
const CONWAY_TICK           = 800;   // ms — how often Conway state machine updates each agent
```

**What each agent setting does:**

| Constant | Default | Effect |
|---|---|---|
| `MAX_AGENTS` | `6` | More agents = easier game, more chaos on screen. Max is 8. |
| `AGENT_ATTACK_RANGE` | `60` | Increase to make agents engage from farther away. |
| `AGENT_HEAL_THRESHOLD` | `0.35` | Higher value = agents heal earlier, survive more. |
| `PLAYER_HEAL_THRESHOLD` | `0.3` | Set to `0` to disable auto-healing entirely. |
| `AGENT_SKILL_COOLDOWN` | `3000` | Lower = agents spam skills faster. |
| `CONWAY_TICK` | `800` | Lower = sharper, faster AI reactions. |

> **Your class is determined by a hash of your name.** Each of the 8 archetypes — Noble Warrior, Wise Mage, Cunning Rogue, Steadfast Archer, Generous Healer, Wild Berserker, Holy Knight, Necromancer — has unique stat weights, skill sets, and AI behavior priorities baked in. Configuration affects all agents equally.

---

## 🌍 World Settings

These constants define the size and feel of the game world. Conway-generated terrain uses `MAP_W` and `MAP_H` to build each zone's layout.

```javascript
const MAP_W                   = 60;    // Map width in tiles. Larger = bigger world but slower generation.
const MAP_H                   = 45;    // Map height in tiles.
const TILE                    = 32;    // Tile size in pixels. 32 = standard, 24 = more visible area.
const MAX_MONSTERS            = 18;    // Max simultaneous monsters on the map. Higher = harder, slower.
const MONSTER_SPAWN_INTERVAL  = 4000;  // ms — milliseconds between new monster spawns
const LEGENDARY_CHANCE        = 0.003; // Per-tick chance of a legendary boss spawning. 0.003 ≈ every 5 min.
```

**What each world setting does:**

| Constant | Default | Effect |
|---|---|---|
| `MAP_W` / `MAP_H` | `60` / `45` | Bigger maps = more exploration, longer travel time. |
| `TILE` | `32` | Set to `24` to see more of the world at once. |
| `MAX_MONSTERS` | `18` | More monsters = harder game, heavier CPU load. |
| `MONSTER_SPAWN_INTERVAL` | `4000` | Lower = monsters respawn faster, game feels relentless. |
| `LEGENDARY_CHANCE` | `0.003` | Raise to `0.01` for frequent boss encounters. |

> The **Shadow King** world boss spawns based on `LEGENDARY_CHANCE` per tick. When it spawns, all OpenClaw agents immediately swarm it — 100% drop rate for legendary equipment.

---

## 💰 Economy Settings

Control how fast players progress through loot, XP, and gold.

```javascript
const SELL_RATIO      = 0.5;  // 0–1 — fraction of item price received when selling. Default 50%.
const XP_MULTIPLIER   = 1.0;  // Global XP multiplier. Set to 2.0 for faster leveling.
const GOLD_MULTIPLIER = 1.0;  // Global gold drop multiplier.
const DROP_RATE_BONUS = 0;    // 0–1 — added to all monster drop rates. 0.2 = +20% drop chance.
```

| Constant | Default | Effect |
|---|---|---|
| `SELL_RATIO` | `0.5` | Set to `0.8` to make the shop more rewarding. |
| `XP_MULTIPLIER` | `1.0` | `2.0` = double XP — great for testing builds fast. |
| `GOLD_MULTIPLIER` | `1.0` | Increase to speed up gear progression. |
| `DROP_RATE_BONUS` | `0` | `0.3` gives all monsters a +30% drop bonus. |

---

## 🟢 Easy Mode Configuration

More drops, faster XP, rare bosses, agents survive longer.

```javascript
// Easy mode — more drops, faster XP, rare bosses
const MAX_MONSTERS          = 12;
const XP_MULTIPLIER         = 2.0;
const GOLD_MULTIPLIER       = 1.5;
const DROP_RATE_BONUS       = 0.3;
const PLAYER_HEAL_THRESHOLD = 0.5;
const LEGENDARY_CHANCE      = 0.001;
```

---

## 🔴 Hard Mode Configuration

More enemies, no healing safety net, frequent boss spawns.

```javascript
// Hard mode — more enemies, no healing safety net
const MAX_MONSTERS          = 30;
const XP_MULTIPLIER         = 0.7;
const GOLD_MULTIPLIER       = 0.8;
const DROP_RATE_BONUS       = -0.1;
const PLAYER_HEAL_THRESHOLD = 0.15;
const LEGENDARY_CHANCE      = 0.006;
```

---

## 🗺️ Adding a Custom Map

Add a new zone to the `MAPS` array inside the script tag.

```javascript
// Add to the MAPS array in the script
{
  id:       'volcano',
  name:     'Volcanic Crater',
  minLevel:  15,
  ambience: '🌋 A fiery wasteland',
  tileset: {
    ground: { color: '#2a0a00', weight: 60 },
    lava:   { color: '#cc3300', weight: 15, deco: '🔥' },
    rock:   { color: '#1a0800', weight: 25, solid: true },
  },
  monsters: ['demon', 'gargoyle', 'ancient_dragon'],
}
```

Conway terrain generation automatically applies to every new map using these tileset weights.

---

## 👾 Adding a Custom Monster

Add a new enemy to the `MONSTER_TYPES` array, then add a draw case in `drawMonsterPixel()`.

```javascript
// Add to the MONSTER_TYPES array
{
  name:        'Crystal Golem',
  monsterType: 'crystal_golem',
  emoji:       '💎',
  color:       '#88ccff',
  hp: 180,  atk: 22,  def: 30,
  xp: 80,   gold: [25, 60],
  size: 32,  speed: 0.5,  level: 8,
  drops: [
    ['mana_crystal', 0.7],
    ['iron_armor',   0.4],
  ],
  maps: ['volcano', 'eternal_abyss'],
}
// Then add a case in drawMonsterPixel() for 'crystal_golem'
```

---

## 🗂️ Full Configuration Reference

### World Settings
| Constant | Default | Type | Description |
|---|---|---|---|
| `MAP_W` | `60` | number | Map width in tiles |
| `MAP_H` | `45` | number | Map height in tiles |
| `TILE` | `32` | number | Tile size in pixels |
| `MAX_MONSTERS` | `18` | number | Max simultaneous monsters |
| `MONSTER_SPAWN_INTERVAL` | `4000` | ms | Milliseconds between monster spawns |
| `LEGENDARY_CHANCE` | `0.003` | 0–1 | Per-tick world boss spawn chance |

### Agent Settings
| Constant | Default | Type | Description |
|---|---|---|---|
| `MAX_AGENTS` | `6` | number | Number of OpenClaw companions. Max 8. |
| `AGENT_ATTACK_RANGE` | `60` | px | Melee attack engage distance |
| `AGENT_HEAL_THRESHOLD` | `0.35` | 0–1 | Agent auto-potion HP threshold |
| `PLAYER_HEAL_THRESHOLD` | `0.3` | 0–1 | Player auto-potion HP threshold |
| `AGENT_SKILL_COOLDOWN` | `3000` | ms | Minimum agent skill cooldown |
| `CONWAY_TICK` | `800` | ms | Conway AI state machine update interval |

### Economy Settings
| Constant | Default | Type | Description |
|---|---|---|---|
| `SELL_RATIO` | `0.5` | 0–1 | Fraction of price received when selling |
| `XP_MULTIPLIER` | `1.0` | number | Global XP gain multiplier |
| `GOLD_MULTIPLIER` | `1.0` | number | Global gold drop multiplier |
| `DROP_RATE_BONUS` | `0` | 0–1 | Flat bonus added to all drop rates |

---

## ⚡ Quick Tuning Tips

- **Game feels too slow?** Lower `CONWAY_TICK` to `400` and `MONSTER_SPAWN_INTERVAL` to `2000`
- **Agents keep dying?** Raise `AGENT_HEAL_THRESHOLD` to `0.6` and lower `MAX_MONSTERS`
- **Want more loot?** Set `DROP_RATE_BONUS` to `0.3` and `GOLD_MULTIPLIER` to `1.5`
- **Want a challenge?** `MAX_MONSTERS: 30`, `XP_MULTIPLIER: 0.7`, `PLAYER_HEAL_THRESHOLD: 0.1`
- **Testing a new class?** Set `XP_MULTIPLIER: 5.0` to max level quickly, then reset

---

<div align="center">
  <strong>⚔️ CONQUEST</strong><br>
  <em>Conway + OpenClaw Adventure RPG · All config lives in one HTML file · No build step required</em>
</div>
