# Niffler & Hagrid

## Components

To solve the Halite III problem there were a couple of high level components utilized:

1) [Strategy Pattern](Strategy.md)
2) [Greedy Orchestration](Orchestrator.md)
3) [Binary Space Partition](BSP.md)
4) [Genetic Algorithm](GeneticAlgorithm.md)

## Code Overview

Two code responsitories were created during the solution: Niffler and Hagrid. Niffler is the bot that collects Halite. Hagrid is an implementation of a genetic algorithm to optimize the parameters used in the Niffler solution.

### Niffler

Niffler combines a few concepts together into a metaheuristic based approach to solving the problem of resource collection in Halite III. Niffler is implemented using the Strategy Design pattern via `BotStrategyManager`. The first iteration of Niffler contains two strategies: `SimpleBotStrategy` and `BinarySpaceBotStrategy`. The `SimpleBotStrategy` will choose a direction at random to collect halite and based on simple rules will direct the `Ship` back to the `Shipyard`.

### Hagrid

Niffler has a number of parameters that can be tweaked to drive the BSP Tree and Orchestrator. A few examples are the maximum halite per node and ideal capacity of a ship. Hagrid is an implementation of a genetic algorithm to see what values of all Niffler parameters will produce the best results.
