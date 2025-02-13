<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RPG Game</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            color: #333;
            padding: 20px;
        }
        #gameLog {
            margin-bottom: 20px;
            background-color: #222;
            color: white;
            padding: 10px;
            max-height: 200px;
            overflow-y: scroll;
        }
        .button {
            padding: 10px 20px;
            margin: 5px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <h1>RPG Game</h1>
    <div id="gameLog"></div>
    <div>
        <button class="button" onclick="exploreWorld()">Explore World</button>
        <button class="button" onclick="startCombat()">Start Combat</button>
        <button class="button" onclick="craftItem()">Craft Item</button>
        <button class="button" onclick="interactWithNPC()">Interact with NPC</button>
    </div>
    <script src="game.js"></script>
</body>
</html>// Character Class
class Character {
    constructor(name) {
        this.name = name;
        this.health = 100;
        this.mana = 50;
        this.attack = 10;
        this.defense = 5;
        this.level = 1;
        this.alignment = "Neutral";
        this.skills = {
            "Fireball": 10,
            "Ice Blast": 12
        };
        this.inventory = ["Potion", "Mana Potion"];
    }

    levelUp() {
        this.level++;
        this.health += 20;
        this.mana += 10;
        this.attack += 5;
        this.defense += 3;
        log(`${this.name} leveled up to level ${this.level}!`);
    }

    useSkill(skill, target) {
        if (this.skills[skill] && this.mana >= this.skills[skill]) {
            this.mana -= this.skills[skill];
            const damage = this.attack + Math.floor(Math.random() * 10) + 5;
            target.takeDamage(damage);
            log(`${this.name} casts ${skill}, dealing ${damage} damage!`);
        } else {
            log(`${skill} failed. Not enough mana or skill not available.`);
        }
    }

    takeDamage(damage) {
        const netDamage = Math.max(0, damage - this.defense);
        this.health -= netDamage;
        log(`${this.name} takes ${netDamage} damage, remaining health: ${this.health}`);
    }

    heal(amount) {
        this.health = Math.min(100, this.health + amount);
        log(`${this.name} heals for ${amount} health. Current health: ${this.health}`);
    }
}

// NPC Class
class NPC {
    constructor(name, role, affinity) {
        this.name = name;
        this.role = role;
        this.affinity = affinity;
        this.backstory = `${name} has a rich history in the realm.`;
    }

    interact() {
        log(`${this.name} says: "${this.getDialogue()}"`);
    }

    getDialogue() {
        if (this.affinity > 50) {
            return "Welcome, friend! I have important news for you.";
        } else {
            return "Hmm... I don't trust you. What do you want?";
        }
    }
}

// World Class
class World {
    constructor() {
        this.npcs = [
            new NPC("Elora", "Quest Giver", 80),
            new NPC("Grim", "Merchant", 40),
            new NPC("Thal", "Blacksmith", 60)
        ];
        this.currentEvent = "";
    }

    randomEvent() {
        const events = [
            "A magical storm approaches.",
            "A village is under attack by bandits.",
            "You find an ancient artifact."
        ];
        this.currentEvent = events[Math.floor(Math.random() * events.length)];
        log(`World Event: ${this.currentEvent}`);
    }

    interactWithNPC(index) {
        if (this.npcs[index]) {
            this.npcs[index].interact();
        } else {
            log("NPC not found.");
        }
    }
}

// Crafting System
class CraftingSystem {
    constructor() {
        this.materials = {
            "Iron": 5,
            "Wood": 3
        };
        this.gold = 100;
    }

    craftItem(item) {
        if (item === "Sword" && this.materials["Iron"] >= 3 && this.materials["Wood"] >= 2) {
            this.materials["Iron"] -= 3;
            this.materials["Wood"] -= 2;
            this.gold -= 50;
            log("Crafted a Sword! Remaining gold: " + this.gold);
        } else {
            log("Not enough materials or gold.");
        }
    }
}

// Game Functions
const player = new Character("Hero");
const world = new World();
const crafting = new CraftingSystem();

function log(message) {
    const gameLog = document.getElementById('gameLog');
    gameLog.innerHTML += `<p>${message}</p>`;
    gameLog.scrollTop = gameLog.scrollHeight;
}

function exploreWorld() {
    world.randomEvent();
}

function startCombat() {
    log("A battle begins!");
    const enemy = new NPC("Bandit Leader", "Enemy", 100);
    const combat = new Combat(player, enemy);
    combat.startBattle();
}

function craftItem() {
    crafting.craftItem("Sword");
}

function interactWithNPC() {
    world.interactWithNPC(0); // Interact with the first NPC (Elora)
}

// Combat Class
class Combat {
    constructor(player, enemy) {
        this.player = player;
        this.enemy = enemy;
    }

   } startBattle() {
        while (this.player.health > 0 && this.enemy.affinity > 0) {
            this.playerTurn();
            this.enemyTurn();
            if (this.player.health <= 0 || this.enemy.affinity <= 0) break;
        }
        this.endBattle();
    

  }  playerTurn() {
        const damage = this.player.attack + Math.floor(Math.random() * 10) + 5;
        this.enemy.affinity -= damage;
        log(`${this.player.name} attacks ${this.enemy.name} for ${damage} damage!`);
    }

  }  enemyTurn() {
        const damage = Math.max(0, this.enemy.affinity - this.player.defense);
        this.player.health -= damage;
        log(`${this.enemy.name} attacks ${this.player.name} for ${damage} damage!`);
    }

  }  endBattle() {
        if (this.player.health <= 0) {
            log(`${this.player.name} has been defeated.`);
        } else {
            log(`${this.player.name} won the battle!`);
        }
    }
}
