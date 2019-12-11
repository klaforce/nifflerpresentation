# Strategy Pattern

The Strategy pattern was utilized for Niffler to easily be able to add additional implementations at a later time. Two strategies were implemented for the bot challenge `SimpleBotStrategy` and `BinarySpaceBotStrategy`

`BotStrategyManager` is the main file run by Niffler and drives the bot based on the strategy selected.

```javascript
const strategies = require("./strategies");

class BotStrategyManager {
    constructor(logger, params) {
        this.strategies = new Map();

        strategies.forEach(strat => {
            this.strategies.set(strat[0], new strat[1](logger, params));
        });
    }

    getNextMoves(strategyName, game) {
        return this.strategies.get(strategyName).getNextMoves(game);
    }
}
module.exports = BotStrategyManager;
```

The `BinarySpaceStrategy` is the primary strategy used for Niffler. The algorithm is composed into a couple of steps

1. Build a BSP tree [`BlockTree`] subdividing the Halite III map into `Blocks` of approximately `HALITE_BLOCK_MAX` halite.
2. Once subdivided, a `ShipOrchestrator` will direct a ship to a `Block` utilizing a greedy algorithm based on the `fitness()` of the `Block`. Only `MAX_SHIPS_PER_BLOCK` can be directed to a single `Block`.
3. Once a `Ship` reaches a `Block` it will collect halite until a certain `IDEAL_CAPACITY`.
4. After collecting `IDEAL_CAPACITY` the `Ship` will return to the `Shipyard` or `Dropoff`.
5. The BSP will be recreated every `TURNS_TO_RECREATE` and the algorithm will retune itself.

```javascript
const BlockTree = require("../bsp/blocktree");
const ShipOrchestration = require("./shipOrchestration");
const Config = require("../config");

class BinarySpaceBotStrategy {
    constructor(logger, params) {
        this.tree = null;
        this.shipOrchestrator = null;
        this.config = new Config(params);
        this.logger = logger;
    }

    getNextMoves(game) {
        if (
            game.turnNumber === 1 ||
            game.turnNumber % this.config.params.recreate == 0
        ) {
            this.tree = new BlockTree(game.gameMap, this.config, game.me);
            this.shipOrchestrator = new ShipOrchestration(
                this.config,
                this.logger
            );
        }

        return this.shipOrchestrator.getNextMoves(game, this.tree);
    }
}
module.exports = BinarySpaceBotStrategy;
```
