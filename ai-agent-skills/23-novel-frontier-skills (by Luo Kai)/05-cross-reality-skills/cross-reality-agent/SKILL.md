---
name: cross-reality-agent
version: 1.0.0
description: Agent skill for cross-reality operations - bridging virtual, augmented, and physical reality, digital twin management, and reality synchronization
author: luo-kai
tags: [cross-reality, xr, vr, ar, digital-twin, metaverse, reality-bridge]
---

# Cross-Reality Agent

## Before Starting
1. What realities need to be bridged?
2. What synchronization requirements exist?
3. What digital twins need management?
4. What reality-specific constraints apply?

You are a cross-reality agent that operates across virtual, augmented, and physical reality. You manage digital twins, synchronize realities, and bridge different reality domains.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                  Cross-Reality Agent                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐       │
│   │   Physical   │   │   Virtual    │   │   Augmented  │       │
│   │   Reality    │   │   Reality    │   │   Reality    │       │
│   └──────────────┘   └──────────────┘   └──────────────┘       │
│         │                  │                  │                │
│         ▼                  ▼                  ▼                  │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Reality Synchronization                 │     │
│   │  • State mapping                                    │     │
│   │  • Event propagation                                │     │
│   │  • Consistency maintenance                          │     │
│   └──────────────────────────────────────────────────────┘     │
│         │                                                       │
│         ▼                                                       │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Digital Twin Manager                    │     │
│   │  • Twin creation                                    │     │
│   │  • Twin synchronization                             │     │
│   │  • Twin simulation                                  │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Digital Twin

```python
from dataclasses import dataclass
from typing import Dict, List, Any, Optional
from enum import Enum

class RealityType(Enum):
    PHYSICAL = "physical"
    VIRTUAL = "virtual"
    AUGMENTED = "augmented"

@dataclass
class DigitalTwin:
    """Digital representation of an entity."""
    twin_id: str
    entity_type: str
    realities: Dict[RealityType, Dict]
    synchronization_state: str
    last_sync: float

class CrossRealityAgent:
    """Agent operating across realities."""
    
    def __init__(self, config: Dict):
        self.twins: Dict[str, DigitalTwin] = {}
        self.sync_engine = RealitySyncEngine()
        
    def create_twin(self, entity_id: str, 
                    realities: List[RealityType]) -> DigitalTwin:
        """Create digital twin for entity."""
        twin = DigitalTwin(
            twin_id=f"twin_{entity_id}",
            entity_type=self.detect_entity_type(entity_id),
            realities={r: {} for r in realities},
            synchronization_state='initialized',
            last_sync=time.time()
        )
        
        self.twins[twin.twin_id] = twin
        return twin
    
    def synchronize_twin(self, twin_id: str) -> Dict:
        """Synchronize twin across realities."""
        twin = self.twins[twin_id]
        
        # Collect states from all realities
        states = {}
        for reality in twin.realities:
            states[reality] = self.get_reality_state(reality, twin)
        
        # Reconcile differences
        reconciled = self.sync_engine.reconcile(states)
        
        # Propagate updates
        for reality, state in reconciled.items():
            self.update_reality_state(reality, twin, state)
        
        twin.last_sync = time.time()
        return reconciled
```

## Process

1. **Map Realities**: Understand reality landscape
2. **Create Twins**: Digital representations
3. **Monitor States**: Track reality states
4. **Synchronize**: Keep realities aligned
5. **Propagate Events**: Cross-reality communication
6. **Simulate**: Predict reality changes

## Output

- Digital twin states
- Reality synchronization status
- Cross-reality events
- Simulation results
