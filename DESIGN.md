# DESIGN.md

# Viking Valhalla - Game Design Document

## Overview

**Viking Valhalla** is a 2D side-scrolling action platformer built using Phaser 3 and Arcade Physics. The player controls a Viking warrior navigating a large tilemap-based world filled with enemies, collectibles, environmental hazards, melee combat, and exploration challenges.

The game emphasizes:

* Exploration
* Combat timing
* Platforming
* Enemy avoidance
* Resource collection
* Environmental danger

The player traverses multiple biome-style regions while collecting coins and keys, defeating enemies, avoiding hazards, and surviving until the end of the level.

---

# Core Gameplay Loop

The primary gameplay loop is:

1. Explore the overworld
2. Collect coins and keys
3. Fight or avoid enemies
4. Traverse platforming sections
5. Avoid environmental hazards
6. Reach progression goals
7. Survive until victory or restart after death

The game combines:

* Platforming movement
* Action combat
* Enemy AI behaviors
* Projectile dodging
* Collectible hunting

---

# Genre

* 2D Platformer
* Action Adventure
* Exploration Platformer

---

# Camera & Presentation

## Camera Style

The camera follows the player across a large tilemap world.

Presentation uses:

* Pixel art graphics
* Tilemap environments
* Layered foreground/background rendering
* Particle effects
* Environmental hazards
* Dynamic sound effects
* Background music

---

# World Structure

The world is built from a Tiled JSON tilemap.

Map includes multiple themed areas:

* Green forest region
* Desert region
* Snow region

The map contains:

* Platforms
* Collision terrain
* Decorative foreground layers
* Water hazards
* Enemy spawn areas
* Collectibles

---

# Tilemap Layers

## Background Layer

Purpose:

* Decorative visuals
* Water hazard detection
* Non-collision environment art

Special behavior:

* Tiles may contain custom properties
* Water tiles use:

```js
water = true
```

Water damages the player over time.

---

## Ground-n-Platforms Layer

Purpose:

* Main collision layer
* Solid terrain
* Walking surfaces
* Platforms

Collision behavior:

* Fully collidable
* Player and enemies collide against this layer

Tile collision property:

```js
collides = true
```

---

## Foreground Layer

Purpose:

* Decorative front rendering
* Foreground objects
* Some collidable objects

Special behavior:

* Rendered in front of player
* May also contain collision tiles

---

# Player Design

## Player Character

The player is a Viking warrior.

Capabilities:

* Run
* Jump
* Attack
* Equip weapons
* Collect items
* Survive hazards

---

# Player Movement

Movement uses Phaser Arcade Physics.

## Physics Values

These values are critical for matching gameplay feel.

### Gravity

```js
gravity.y = 1500
```

### Horizontal Acceleration

```js
ACCELERATION = 250
```

### Horizontal Drag

```js
DRAG = 800
```

### Jump Velocity

```js
JUMP_VELOCITY = -700
```

### Particle Velocity

```js
PARTICLE_VELOCITY = 50
```

---

# Player Health System

## Starting Health

```js
100 HP
```

The player loses health from:

* Enemy melee attacks
* Enemy projectiles
* Water hazards

If health reaches 0:

* Player dies
* Game over state activates
* Death sound plays

---

# Weapons System

The player can equip different melee weapons.

Weapons differ by:

* Damage
* Attack cooldown
* Attack speed

---

## Dagger

### Stats

```js
Damage: 10
Attack Speed: 500ms cooldown
```

### Design Role

* Fast attacks
* Low damage
* Best for quick combat

---

## Sword

### Stats

```js
Damage: 20
Attack Speed: 1300ms cooldown
```

### Design Role

* Balanced weapon
* Medium speed
* Medium damage

---

## Axe

### Stats

```js
Damage: 30
Attack Speed: 2000ms cooldown
```

### Design Role

* Heavy weapon
* Slow attack speed
* High damage

---

# Enemy Design

The game currently includes multiple enemy archetypes.

Enemies use AI state switching based on distance to player.

Possible enemy states:

* Wander
* Chase
* Shoot
* Attack

---

# Evil Wizard

## Behavior

The Evil Wizard is a ranged enemy.

Capabilities:

* Wander when player is far away
* Chase player
* Fire potion projectiles
* Melee attack at close range

---

## Evil Wizard Stats

### Health

```js
100 HP
```

### Follow Distance

```js
275 pixels
```

### Shoot Distance

```js
300 pixels
```

### Melee Distance

```js
30 pixels
```

### Shoot Delay

```js
2000ms
```

### Melee Delay

```js
1200ms
```

---

# Evil Wizard Projectile System

## Projectile Type

Thrown potion projectile.

Projectile behavior:

* Uses arc trajectory
* Simulated gravity
* Travels toward player
* Damages player on hit

---

## Projectile Stats

### Horizontal Speed

```js
350
```

### Projectile Gravity

```js
700
```

### Vertical Velocity Clamp

```js
Minimum: -600
Maximum: 200
```

### Collision Behavior

Projectile is destroyed when:

* Hitting player
* Leaving world bounds

### Damage

```js
10 HP
```

---

# Orc Enemy

## Behavior

The Orc is a melee-focused enemy.

Capabilities:

* Wander
* Chase player
* Melee attack

---

## Orc Stats

### Health

```js
50 HP
```

### Follow Distance

```js
300 pixels
```

### Melee Delay

```js
1500ms
```

---

# Enemy Wandering System

Enemies randomly patrol when player is not nearby.

## Wander Logic

Every:

```js
1000ms
```

Enemy randomly chooses:

* Move left
* Move right

Movement uses reduced speed:

```js
speed * 0.5
```

Enemies visually flip direction depending on movement.

---

# Enemy AI State Priority

Enemy decision order:

1. Attack
2. Chase
3. Shoot
4. Wander

This means:

* Close-range attacks override shooting
* Shooting overrides wandering
* Wandering occurs only when player is far away

---

# Enemy Collision Rules

## Ground Collision

Enemies collide with:

* Terrain
* Platforms
* Foreground collidable objects

---

## Enemy Separation

Enemies repel each other to avoid stacking.

Purpose:

* Prevent overlap
* Maintain readable combat
* Improve navigation visuals

---

# Collectibles

## Coins

Coins are collectible objects.

Purpose:

* Score/resource collection
* Exploration reward

Coin collection:

* Plays sound effect
* Increases coin count

---

## Keys

Keys are collectible progression objects.

Purpose:

* Gate progression
* Unlock progression paths

Key collection:

* Plays collection sound
* Increases key counter

---

# Environmental Hazards

## Water

Water tiles damage the player while standing inside them.

---

## Water Damage Rules

### Damage Amount

```js
1 HP
```

### Damage Interval

```js
1000ms
```

### Detection

Water uses tile property detection.

Special collision behavior:

* Water is NOT solid
* Player can enter water tiles
* Water applies periodic damage instead of collision blocking

---

# Combat System

## Melee Combat

Player attacks enemies using equipped weapon.

Combat characteristics:

* Cooldown based
* Directional
* Range-based hit detection
* Sound feedback

---

# Enemy Melee Combat

Enemies damage player on overlap/contact.

Features:

* Attack cooldowns
* Sound feedback
* Health reduction

---

# Audio Design

The game uses sound heavily to communicate gameplay events.

---

# Sound Categories

## Movement

* Footsteps
* Jumping

## Combat

* Weapon attacks
* Enemy hits
* Hurt sounds
* Death sounds
* Projectile impacts

## Collectibles

* Coin pickup
* Key pickup
* Health pickup

## Music

Background Viking-themed music tracks.

Gameplay randomly selects from:

* music1
* music2
* music3
* music4
* music5

Main menu uses:

* music6

---

# Visual Effects

## Particle Effects

The game includes particle systems using:

```js
kenny-particles
```

Effects include:

* Water bubbles
* Combat feedback
* Environmental particles

---

# Rendering Rules

## Foreground Rendering

Foreground layer renders above player.

Purpose:

* Depth illusion
* Environmental immersion

---

# User Interface

The game displays:

* Health
* Coin count
* Key count
* Menu text
* Game over state
* Win state

---

# Controls

## Movement

### Arrow Keys

* Move left/right
* Jump

---

## Combat

### SPACE

Attack with equipped weapon.

---

## Equipment

### E

Equip/interact with weapons.

---

## Interaction

### F

Interact/open objects.

---

## Restart

### R

Restart game.

---

# Game States

## Main Menu

Displays:

* Game title
* Controls
* Instructions

Transitions to gameplay on SPACE.

---

## Gameplay State

Main interactive game loop.

---

## Game Over State

Triggered when:

```js
playerHealth <= 0
```

Effects:

* Lose sound
* Player death state
* Gameplay stop

---

## Win State

Triggered when player completes objectives.

Effects:

* Victory sound
* Win display

---

# Object Management

## Enemy Arrays

Enemies are stored in arrays.

Examples:

```js
evilWizardArray
orcArray
spiderArray
```

---

## Projectile Arrays

Projectile tracking:

```js
evilWizardPotionArray
```

Projectiles are filtered after destruction.

---

# Animation & Sprite Direction

Sprites flip horizontally depending on movement direction.

Rules:

* Facing left → flipped
* Facing right → normal

Used for:

* Player
* Enemies

---

# Intended Gameplay Feel

The game is intended to feel:

* Heavy
* Grounded
* Slightly dangerous
* Exploration-focused
* Reactive during combat

Movement values intentionally create:

* Moderate momentum
* Strong gravity
* Committed jumps
* Deliberate combat timing

---

# Important Collision Behaviors Summary

| Object                      | Collision Behavior         |
| --------------------------- | -------------------------- |
| Ground tiles                | Solid                      |
| Foreground collidable tiles | Solid                      |
| Water tiles                 | Non-solid, periodic damage |
| Enemy bodies                | Damage on overlap/contact  |
| Projectiles                 | Destroy on impact          |
| Coins                       | Collect on overlap         |
| Keys                        | Collect on overlap         |

---

# Asset Requirements For Reimplementation

To recreate the game accurately, the following are required:

## Art Assets

* Player sprites
* Enemy sprites
* Weapons
* Tilemaps
* Particle textures
* UI assets
* Collectibles

## Audio Assets

* Music tracks
* Jump sounds
* Weapon sounds
* Enemy sounds
* Pickup sounds
* Ambient effects

## Level Files

* Tilemap JSON/TMJ
* Tileset images
* Tile properties
* Spawn positions

---

# Reimplementation Priorities

To faithfully recreate gameplay:

1. Match movement physics exactly
2. Preserve enemy distance thresholds
3. Preserve attack cooldowns
4. Preserve projectile arc behavior
5. Maintain layered tilemap rendering
6. Keep water damage timing identical
7. Preserve audio feedback timing
8. Preserve enemy AI priority ordering

---

# Summary

Viking Valhalla is a physics-driven 2D action platformer focused on exploration, combat, and environmental navigation. The game combines classic platforming movement with enemy AI behaviors, melee combat, ranged projectile threats, collectibles, layered tilemaps, and atmospheric Viking-inspired presentation.

The design emphasizes:

* Responsive movement
* Deliberate combat pacing
* Exploration reward
* Environmental danger
* Audio/visual gameplay feedback
* Clear enemy behavior states
