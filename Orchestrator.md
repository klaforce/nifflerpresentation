# Greedy Orchestration

The `BinarySpaceBotStrategy` strategy utilizes the `ShipOrchestrator` class to select the next set of moves for the ship. The `ShipOrchestrator utilizes a greedy algorithm to select the node for the ship to move to next.

```javascript
const hlt = require("../../hlt");

class ShipOrchestration {
    constructor(config, logger) {
        this.shipsInRoute = new Map();
        this.shipsBackToSpawn = new Map();
        this.shipInNode = new Map();
        this.config = config;
        this.logger = logger;
    }

    selectNodeForShip(ship, tree) {
        //traverse through the tree and apply a fitness function to each leaf node.
        let leaves = tree.getLeaves();
        let selected = this.getMaximumFitness(leaves);

        //update the node
        selected.shipsInRouteToBlock++;
        this.shipsInRoute.set(ship.id, {
            ship: ship,
            node: selected
        });
    }

    getNextMoves(game, tree) {
        const commandQueue = [];
        const { gameMap, me } = game;

        for (const ship of me.getShips()) {
            this.updateShipDetails(ship);

            if (this.isShipInRouteToNode(ship)) {
                commandQueue.push(this.navigateShipToNode(ship, gameMap));
            } else if (this.isShipInRouteToSpawn(ship)) {
                commandQueue.push(
                    this.navigateShipToClosestSpawn(ship, me, gameMap)
                );
            } else {
                this.selectNodeForShip(ship, tree);
                commandQueue.push(this.navigateShipToNode(ship, gameMap));
            }

            this.cleanUpShipDetails(ship, me);
        }

        if (this.canBuildSpawnPoint(game, me)) {
            commandQueue.push(this.getSpawnPoint(me));
        }

        return commandQueue;
    }
}
module.exports = ShipOrchestration;
```

```javascript
class Block {
    constructor(map, level, config) {
        this.map = map;
        this.level = level;
        this.config = config;
        this.left = null;
        this.right = null;
        this.totalHalite = 0;
        this.distanceFromClosestDropoff = 100000000;
        this.shipsInRouteToBlock = 0;
        this.leaf = false;
        this.orientation = null;
        this.partition = null;
        this.xModifier = 0;
        this.yModifier = 0;
    }

    getModifiedPosition(x, y) {
        return { x: x + this.xModifier, y: y + this.yModifier };
    }

    getFitness() {
        //maximum value is the best
        return (
            (this.shipsInRouteToBlock < this.config.params.maxships
                ? this.config.params.fitnessformaxships
                : this.shipsInRouteToBlock) +
            this.config.params.fitnessfordistancetodropoff /
                this.distanceFromClosestDropoff +
            this.totalHalite
        );
    }
}
module.exports = Block;
```
