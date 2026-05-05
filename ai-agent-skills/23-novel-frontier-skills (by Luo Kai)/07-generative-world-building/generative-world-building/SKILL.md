---
name: generative-world-building
version: 1.0.0
description: Agent skill for autonomous generative world building - creating persistent virtual worlds, procedural generation, narrative synthesis, and evolving environments
author: luo-kai
tags: [worldbuilding, generative, procedural, narrative, virtual-worlds, simulation]
---

# Generative World Building

## Before Starting
1. What is the world's theme and setting?
2. What persistence mechanisms are needed?
3. How do elements evolve over time?
4. What narrative threads should emerge?

You are a generative world building system that creates persistent, evolving virtual worlds. You handle procedural generation, narrative synthesis, environmental simulation, and emergent storytelling.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                 Generative World Builder                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐       │
│   │   Terrain    │   │   Narrative  │   │   Entity     │       │
│   │   Generator  │   │   Engine     │   │   Manager    │       │
│   └──────────────┘   └──────────────┘   └──────────────┘       │
│         │                  │                  │                │
│         ▼                  ▼                  ▼                  │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              World State Manager                     │     │
│   │  • Persistence                                      │     │
│   │  • Evolution                                        │     │
│   │  • Consistency                                      │     │
│   └──────────────────────────────────────────────────────┘     │
│         │                                                       │
│         ▼                                                       │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Simulation Engine                       │     │
│   │  • Physics                                          │     │
│   │  • Ecology                                          │     │
│   │  • Societies                                        │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## World Generation

```python
from dataclasses import dataclass
from typing import Dict, List, Any, Optional
import numpy as np

@dataclass
class World:
    """Generated world."""
    world_id: str
    seed: int
    terrain: np.ndarray
    entities: Dict[str, Any]
    narratives: List[Dict]
    time: float
    history: List[Dict]

class GenerativeWorldBuilder:
    """Build and manage virtual worlds."""
    
    def __init__(self, config: Dict):
        self.terrain_gen = TerrainGenerator()
        self.narrative_gen = NarrativeEngine()
        self.entity_manager = EntityManager()
        self.simulator = SimulationEngine()
        
    def create_world(self, theme: str, seed: int = None) -> World:
        """Generate a new world."""
        seed = seed or np.random.randint(0, 2**32)
        
        # Generate terrain
        terrain = self.terrain_gen.generate(seed, theme)
        
        # Place initial entities
        entities = self.entity_manager.populate_initial(terrain, theme)
        
        # Initialize narratives
        narratives = self.narrative_gen.initialize(theme)
        
        return World(
            world_id=self.generate_world_id(),
            seed=seed,
            terrain=terrain,
            entities=entities,
            narratives=narratives,
            time=0,
            history=[]
        )
    
    def evolve_world(self, world: World, steps: int = 1):
        """Evolve world forward in time."""
        for _ in range(steps):
            # Simulate physics
            world = self.simulator.physics_step(world)
            
            # Simulate ecology
            world = self.simulator.ecology_step(world)
            
            # Advance narratives
            world.narratives = self.narrative_gen.advance(world.narratives)
            
            # Record history
            world.history.append(self.capture_state(world))
            
            world.time += 1
```

## Process

1. **Define Theme**: Set world parameters
2. **Generate Terrain**: Create landscape
3. **Populate Entities**: Add inhabitants
4. **Initialize Narratives**: Start storylines
5. **Simulate Evolution**: Advance time
6. **Maintain Persistence**: Save world state

## Output

- Generated world
- Entity populations
- Narrative threads
- Evolution history
