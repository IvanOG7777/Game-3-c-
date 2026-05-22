# AGENTS.md

## Project Overview

This project is a Phaser 3 action-platformer game called **Viking Valhalla**. The player controls a Viking character through a tilemap-based world filled with enemies, keys, coins, water hazards, melee combat, and projectile-based enemies.

The game is structured around Phaser scenes and helper utility functions.

---

# Project Structure

## Main Entry

### `main.js`

Responsible for:

* Creating the Phaser game config
* Setting game width/height
* Configuring Arcade Physics
* Registering scenes
* Creating the Phaser game instance

Current scenes:

* `Load`
* `Init`
* `Overworld`

Game uses:

* `Phaser.CANVAS`
* Pixel art rendering
* Arcade physics

---

# Scenes

## `Load.js`

Purpose:

* Preloads all game assets before gameplay begins.

Includes:

* Tilemaps
* Spritesheets
* Enemy sprites
* Weapons/items
* Fonts
* Particle atlases
* Audio/music

Important loaded assets:

### Player / Enemies

* `vikingPlayer`
* `evilWizard`
* `orc`

### Items

* `redPotion`
* `heart`

### Tilemaps

* `platformer-level-1`

### Audio

* Footsteps
* Combat sounds
* Potion sounds
* Music tracks 1–6

### Fonts

* `rocketSquare`

Scene Flow:

```js
this.scene.start("initScene");
```

---

## `Init.js`

Purpose:

* Main menu / title screen.

Features:

* Displays title text
* Shows controls
* Plays menu music
* Waits for SPACE input
* Starts the main game scene

Starts:

```js
this.scene.start("platformerScene");
```

Menu controls:

* SPACE → Start game

---

## `Overworld.js`

Main gameplay scene.

Handles:

* Tilemap creation
* Physics
* Player movement
* Enemy spawning
* Combat
* Inventory/equipment
* Coins/keys
* Health system
* Water hazards
* Projectiles
* Audio
* Camera setup
* Win/loss state

---

# Tilemap Layers

The game currently uses:

### Background

Visual-only background layer.
Can contain:

* Water tiles
* Decorative elements

### Ground-n-Platforms

Main collision layer.
Used for:

* Walking
* Jumping
* Platform collisions

### Foreground

Front decorative layer.
Can also contain collisions.
Used to render objects in front of player.

---

# Core Gameplay Systems

## Player

### Movement

Player movement uses:

* Acceleration
* Drag
* Gravity
* Jump velocity

Important values:

```js
this.ACCELERATION = 250;
this.DRAG = 800;
this.JUMP_VELOCITY = -700;
```

### Combat

Weapons currently include:

* Dagger
* Sword
* Axe

Each weapon has:

* Damage
* Attack speed

Example:

```js
this.swordDamage = 20;
this.swordSpeed = 1300;
```

### Health

Player starts with:

```js
this.playerHealth = 100;
```

Damage sources include:

* Enemy melee
* Wizard projectiles
* Water hazards

---

# Enemy System

## Enemy Types

### Evil Wizard

Features:

* Chasing
* Projectile attacks
* Melee attacks
* Wander behavior

Variables:

```js
this.evilWizardFollowDistance
this.evilWizardShootDistance
this.evilWizardShootDelay
```

### Orc

Features:

* Melee combat
* Chasing
* Wandering

---

# Enemy AI

Enemy logic is mainly handled inside:

## `GameFunctions.js`

Contains helper systems for:

* Enemy movement
* Random wandering
* Projectile movement
* Melee attacks
* Enemy separation
* Enemy spawning

---

# AI States

Enemies use boolean state switching:

```js
enemy.attack
enemy.chase
enemy.shoot
enemy.wander
```

State priority:

1. Attack
2. Chase
3. Shoot
4. Wander

---

# Projectile System

Used mainly by Evil Wizards.

Features:

* Arc-based projectile motion
* Gravity simulation
* Velocity clamping
* Collision checks

Projectile update handled by:

```js
moveProjectile(scene, deltaTime)
```

Projectile destruction occurs when:

* Hitting player
* Leaving world bounds

---

# Audio System

Audio stored under:

```js
my.sounds
```

Examples:

```js
my.sounds.jump
my.sounds.coinCollect
my.sounds.music
```

Music:

* Randomly selected from tracks 1–5 during gameplay
* Menu uses music6

---

# Water Hazard System

Water tiles are detected using tile properties:

```js
tile.properties.water == true
```

Water damage variables:

```js
this.waterDamage
this.waterDamageDelay
```

Damage is applied over time while player remains inside water.

---

# Arrays Used Throughout Project

## Enemy Arrays

```js
this.evilWizardArray
this.orcArray
this.spiderArray
```

## Projectile Arrays

```js
this.evilWizardPotionArray
```

---

# Common Development Patterns

## Delta Time Movement

Projectiles use delta time movement:

```js
projectile.x += velocity * (deltaTime / 1000)
```

---

## Timers

Enemy attacks use Phaser time system:

```js
scene.time.now
```

Common timers:

* nextShootTime
* nextMeleeTime
* nextWanderChange

---

## Collision Style

The project commonly uses helper collision checks:

```js
collides(player, enemy)
```

---

# Scene Naming Conventions

| Scene     | Key               |
| --------- | ----------------- |
| Load      | `loadScene`       |
| Init      | `initScene`       |
| Overworld | `platformerScene` |

---

# Coding Style Notes

## Preferred Style

* Use helper functions for repeated logic
* Keep enemy behavior modular
* Use arrays for enemy management
* Use Phaser Arcade Physics
* Use tile properties for environmental hazards
* Store sounds in `my.sounds`
* Store sprites in `my.sprite`

---

# Important Gameplay Variables

## Combat

```js
this.playerHitDamage
this.nextPlayerHitTime
this.playerHitSpeed
```

## Game State

```js
this.gameOver
this.gameWon
this.playerAlive
```

## Collectibles

```js
this.keysCollected
this.coinsCollected
```

---

# Suggested Future Improvements

Potential future systems:

* Boss fights
* Inventory UI
* Save/load system
* Additional enemy types
* NPC dialogue
* Expanded weapon system
* More maps/biomes
* Skill tree
* Particle combat effects
* Better enemy pathfinding

---

# Notes For Future Contributors

When adding new systems:

* Keep gameplay logic modular
* Avoid putting all logic directly in `Overworld.js`
* Prefer helper functions in `GameFunctions.js`
* Use Phaser groups/arrays consistently
* Reuse timers instead of creating unnecessary update checks
* Keep asset loading centralized in `Load.js`

---

# Key Files Summary

| File               | Purpose                                |
| ------------------ | -------------------------------------- |
| `main.js`          | Phaser configuration and startup       |
| `Load.js`          | Asset loading                          |
| `Init.js`          | Main menu/title screen                 |
| `Overworld.js`     | Main gameplay scene                    |
| `GameFunctions.js` | Enemy AI and gameplay helper functions |

---

# Phaser Version

Project uses Phaser 3. fileciteturn0file0L1-L10

---

# References

Main configuration and scenes: fileciteturn0file1L1-L24

Enemy helper systems: fileciteturn0file2L1-L170

Init scene/menu: fileciteturn0file3L1-L49

Asset loading: fileciteturn0file4L1-L79

Main gameplay systems: fileciteturn0file5L1-L160
