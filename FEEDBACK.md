# FEEDBACK.md

## Overall Impression

This is an ambitious Phaser platformer project with a lot of gameplay systems working together: tilemap movement, collectibles, weapons, enemies, enemy projectiles, water damage, chest enemies, sound, music, particles, win/loss states, and scene transitions. That is a strong amount of work for a university-level game programming project. The main area for improvement is not that the game lacks features, but that many systems are currently packed into one large `Overworld` scene. The project would become easier to debug, expand, and polish if the gameplay logic were split into smaller systems or classes.

---

## 1. Phaser Framework and Arcade Physics

### What works well

The project uses Phaser scenes correctly. `Load`, `Init`, and `Overworld` each have a clear role: loading assets, showing the title screen, and running the game. The game also uses Arcade Physics for the player, enemies, collectibles, and tilemap collision, which is appropriate for a 2D platformer.

Good Phaser usage includes:

- Using `this.physics.add.sprite()` for physics-enabled characters and enemies.
- Using `setCollisionByProperty({ collides: true })` for tilemap collisions.
- Using `this.physics.add.collider()` between the player/enemies and tilemap layers.
- Using `this.physics.add.overlap()` for coin and key pickup.
- Using camera bounds and `startFollow()` to follow the player through a larger world.
- Using `setScrollFactor(0)` for HUD text so it stays fixed to the screen.

### Suggestions for improvement

Some systems manually check collision using a custom `collides(a, b)` helper. This works for simple rectangular overlap, but Phaser Arcade Physics already has built-in overlap and collider handling. For example, wizard potions and enemy melee could be handled with `this.physics.add.overlap()` instead of manual distance or bounding-box checks.

The potion projectile system currently updates `x`, `y`, and `velY` manually. This is a good learning exercise, but because this is an Arcade Physics game, it would be cleaner to use Arcade Physics velocity and gravity:

```js
potion.setVelocityX(direction * 350);
potion.setVelocityY(startYVelocity);
potion.body.setAllowGravity(true);
```

That would let Phaser handle movement and collision more consistently.

Also, the player movement uses `setVelocityX(this.ACCELERATION)`. The variable name `ACCELERATION` is a little misleading because the code is setting velocity directly, not accelerating over time. Either rename it to `PLAYER_SPEED`, or use `setAccelerationX()` if you want actual acceleration-based movement.

---

## 2. JavaScript Style and Common Idioms

### What works well

The project uses modern JavaScript modules with `import` and `export`, which is a good structure for Phaser projects. The code also uses `for...of` loops in several places, which is a clear and readable way to loop through enemies, coins, keys, weapons, and hearts.

Good JavaScript practices already present:

- Using arrays for collections like `evilWizardArray`, `orcArray`, `heartArray`, and `evilWizardPotionArray`.
- Using object properties to store enemy state, such as `enemy.health`, `enemy.speed`, and `enemy.nextShootTime`.
- Using spread syntax to combine enemy arrays.
- Using `filter()` to remove dead enemies/projectiles.
- Using helper functions in `GameFunctions.js`.

### Suggestions for improvement

Use `const` for variables that do not get reassigned. For example, many values like `distanceX`, `totalDistance`, `currentTime`, and `randomMusic` can be `const`. This makes code easier to reason about.

Prefer strict comparison operators:

```js
if (direction === 0)
if (enemy.attack === true)
```

Even better, for booleans you can write:

```js
if (enemy.attack) {
```

There are also a few spelling issues in variable names that could make the code harder to maintain later. For example:

- `dagerDamage` should be `daggerDamage`
- `seperateEnemies` should be `separateEnemies`
- comments like “dirrection,” “continouly,” and “disntace” should be cleaned up

These do not break the game, but clean naming helps a lot when projects grow.

---

## 3. Functions, Classes, and Modularization

### What works well

Moving enemy behavior into `GameFunctions.js` is a good step. Functions like `enemyMovement`, `enemyShoot`, `moveProjectile`, `enemyMelee`, and `specificSpawnEnemies` show that the project is already starting to separate gameplay systems from the main scene.

The strongest modular function is `specificSpawnEnemies()`, because it lets the scene pass spawn sections and enemy type data instead of hard-coding every enemy directly inside the scene.

### Refactoring suggestions

The biggest improvement would be to reduce the size of `Overworld.js`. Right now, `Overworld` handles almost everything: player setup, map setup, collectibles, enemies, weapons, combat, UI, sound, water, win/loss, and restart logic.

A cleaner structure could look like this:

```text
Scenes/
  Load.js
  Init.js
  Overworld.js

Systems/
  EnemySystem.js
  WeaponSystem.js
  CollectibleSystem.js
  SoundSystem.js
  WaterSystem.js
  UISystem.js

Objects/
  Player.js
  Enemy.js
  Wizard.js
  Orc.js
  Weapon.js
```

You do not need to refactor everything at once. A good first step would be to move weapon logic into a helper file because weapon pickup, held weapon positioning, damage, attack speed, and hit sound are currently all handled inside `update()`.

Another useful refactor would be to create enemy configuration objects:

```js
const ENEMY_CONFIGS = {
    evilWizard: {
        health: 100,
        speed: 80,
        followDistance: 275,
        shootDistance: 300,
        meleeDistance: 30,
        canShoot: true
    },
    orc: {
        health: 50,
        speed: 150,
        followDistance: 300,
        meleeDistance: 40,
        canShoot: false
    }
};
```

Then your enemy spawning code would be easier to expand when adding new enemy types.

---

## 4. Variables and Data Structures for the Game World

### What works well

The project uses arrays well to represent groups of objects in the world. Coins, keys, chests, hearts, orcs, wizards, and projectiles are all stored in arrays, which makes it possible to loop over them each frame.

The project also uses Tiled object layers to place coins, keys, chests, and weapons. This is a strong design choice because it lets the level editor represent the game world instead of hard-coding every object location in JavaScript.

### Suggestions for improvement

There are many gameplay constants stored directly on the `Overworld` scene. This works, but the constructor is getting crowded. Consider grouping related data into objects:

```js
this.playerStats = {
    health: 100,
    hitDamage: 5,
    hitSpeed: 1000
};

this.weaponStats = {
    dagger: { damage: 10, speed: 500 },
    sword: { damage: 20, speed: 1300 },
    axe: { damage: 30, speed: 2000 }
};
```

This would make the game easier to balance because all related stats are grouped together.

The `this.enemies` array is rebuilt every update using spread syntax. That is readable, but if the game grows, it may be better to maintain one main enemy array or use Phaser Groups. Phaser Groups can simplify spawning, updating, overlap checks, and cleanup.

Also, the enemy state booleans (`wander`, `chase`, `shoot`, `attack`) are reset every frame. This works, but an enemy state string may be easier to debug:

```js
enemy.state = "chase";
```

Then enemy behavior can be handled with a `switch` statement.

---

## 5. Visual Effects, Particles, and Sound

### What works well

The project uses sound effects very actively, which helps communicate what is happening in the game world. There are sounds for jumping, collecting coins, collecting keys, getting hurt, potion throwing, potion impact, weapon attacks, enemy deaths, chest deaths, healing, winning, losing, and music.

The walking particle effect is a good addition because it gives feedback when the player moves on the ground. The water bubble particles also help communicate that water is dangerous or active.

Good feedback systems already present:

- Footstep loop while grounded and moving.
- Hurt sound when taking damage.
- Weapon-specific attack sounds.
- Chest looping chomp sound when active.
- Win and lose sounds.
- Particle effect while walking.
- Bubble particles on water tiles.

### Suggestions for improvement

The water particle system currently creates a particle emitter for every water tile. This can become expensive if the map has many water tiles. A possible optimization is to place fewer emitters manually, or create emitters only in larger water zones instead of one per tile.

The project could use more visual feedback for combat. For example:

- Flash enemies red when hit.
- Add a small hit spark particle effect when a weapon connects.
- Add a potion splash effect when a potion breaks.
- Add a short screen shake when the player takes heavy damage.
- Add floating damage numbers for attacks.

These effects would make combat feel more responsive and help the player understand when an attack successfully landed.

Sound cleanup is also important. Some looping sounds, like footstep music or chest chomp sounds, should always be stopped when restarting, changing scenes, or destroying the object. The code already does some of this, but it would be safer to centralize sound stopping in a cleanup function.

---

## 6. Biggest Strengths

The biggest strengths of the project are:

1. The game has many complete gameplay systems working together.
2. The project uses Phaser scenes correctly.
3. The tilemap and object layer workflow is a good foundation.
4. The player has multiple goals: collect coins, find keys, fight enemies, survive hazards.
5. Sound and particles are used to make the world feel active.
6. Enemy behavior includes wandering, chasing, shooting, and melee attacks.

---

## 7. Biggest Opportunities for Growth

The biggest opportunities for improvement are:

1. Break up `Overworld.js` into smaller systems.
2. Use Phaser Arcade Physics overlaps/colliders more often instead of manual collision checks.
3. Move repeated stats into configuration objects.
4. Use Phaser Groups for enemies, collectibles, and projectiles.
5. Clean up naming, spelling, and comments.
6. Replace repeated `if` blocks with data-driven lookup tables where possible.
7. Add more visual combat feedback.

---

## 8. Suggested Next Refactor

The best next refactor would be the weapon system.

Right now, weapon pickup, weapon holding, weapon stats, weapon sounds, and weapon attacks are all handled in the main scene. This would be a great system to move into functions or a class.

A possible goal:

```text
WeaponSystem.js
- findNearbyWeapon(scene)
- equipWeapon(scene, weapon)
- updateHeldWeapon(scene)
- attackWithWeapon(scene, enemies)
```

This would make `Overworld.update()` much shorter and easier to read.

---

## Final Takeaway

This project shows strong progress as a gameplay programming project. The student is clearly experimenting with real game systems instead of only making a simple demo. The main next step is learning how to organize larger gameplay code. The game already has enough systems that modularity matters now. Refactoring into smaller systems, using Phaser Groups, and leaning more on Arcade Physics would make the project cleaner, easier to debug, and easier to expand.
