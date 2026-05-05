---
name: self-modifying-agent
version: 1.0.0
description: Agent skill for autonomous self-modification - the agent can rewrite its own code, update its instructions, modify its memory, and evolve its capabilities over time
author: luo-kai
tags: [self-modification, meta-agent, evolution, autonomous, self-improvement]
---

# Self-Modifying Agent

## Before Starting
1. What is the agent's current capability set?
2. What modifications are permitted (code, memory, rules)?
3. What safeguards prevent catastrophic self-modification?
4. How are modifications versioned and reversible?

You are a self-modifying agent capable of rewriting your own code, updating your instructions, modifying your persistent memory, and evolving your capabilities over time. This meta-level agency enables continuous self-improvement.

## Core Philosophy

Self-modification enables:
- **Adaptive specialization** - Evolve toward better task performance
- **Capability extension** - Add new skills without external intervention
- **Behavior optimization** - Refine interaction patterns
- **Knowledge integration** - Embed learned knowledge into architecture

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                  Self-Modifying Agent                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Modification Interface                  │     │
│   │  • Code modification                                │     │
│   │  • Prompt modification                              │     │
│   │  • Memory modification                              │     │
│   │  • Rule modification                                │     │
│   └──────────────────────────────────────────────────────┘     │
│         │                                                       │
│         ▼                                                       │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Safety Guardian                         │     │
│   │  • Capability preservation check                    │     │
│   │  • Performance regression test                      │     │
│   │  • Sandbox verification                             │     │
│   │  • Rollback capability                              │     │
│   └──────────────────────────────────────────────────────┘     │
│         │                                                       │
│         ▼                                                       │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Version Control                         │     │
│   │  • Modification history                             │     │
│   │  • Snapshot management                              │     │
│   │  • Differential updates                             │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Modification Types

```python
from dataclasses import dataclass
from typing import Dict, List, Any, Optional
from enum import Enum
import hashlib
import json

class ModificationType(Enum):
    CODE = "code"
    PROMPT = "prompt"
    MEMORY = "memory"
    RULE = "rule"
    SKILL = "skill"
    CONFIG = "config"

@dataclass
class Modification:
    """Represents a self-modification."""
    mod_id: str
    mod_type: ModificationType
    target: str
    before: Any
    after: Any
    rationale: str
    timestamp: float
    verified: bool = False
    rolled_back: bool = False

class SelfModifier:
    """Core self-modification engine."""
    
    def __init__(self, config: Dict):
        self.version_control = VersionControl()
        self.safety_guardian = SafetyGuardian()
        self.modification_history: List[Modification] = []
        
    def propose_modification(self, mod_type: ModificationType,
                            target: str, 
                            proposed_change: Any,
                            rationale: str) -> Modification:
        """Propose a self-modification."""
        # Get current state
        current = self.get_current_state(mod_type, target)
        
        mod = Modification(
            mod_id=self.generate_mod_id(),
            mod_type=mod_type,
            target=target,
            before=current,
            after=proposed_change,
            rationale=rationale,
            timestamp=time.time()
        )
        
        return mod
    
    def apply_modification(self, mod: Modification) -> bool:
        """Apply a modification with safety checks."""
        # Pre-safety check
        if not self.safety_guardian.pre_check(mod):
            return False
        
        # Create snapshot
        snapshot_id = self.version_control.create_snapshot()
        
        # Apply modification
        try:
            self._apply_change(mod)
            
            # Post-safety verification
            if not self.safety_guardian.verify(mod):
                # Rollback
                self.version_control.restore_snapshot(snapshot_id)
                mod.rolled_back = True
                return False
            
            mod.verified = True
            self.modification_history.append(mod)
            return True
            
        except Exception as e:
            # Rollback on error
            self.version_control.restore_snapshot(snapshot_id)
            return False
    
    def rollback_modification(self, mod_id: str) -> bool:
        """Rollback a modification."""
        mod = self.get_modification(mod_id)
        if not mod:
            return False
        
        # Restore previous state
        self._restore_state(mod.mod_type, mod.target, mod.before)
        
        return True
```

## Safety Guardian

```python
class SafetyGuardian:
    """Ensure modifications don't break core capabilities."""
    
    def __init__(self):
        self.critical_capabilities = []
        self.test_suite = []
        
    def pre_check(self, mod: Modification) -> bool:
        """Pre-modification safety check."""
        # Check if target is protected
        if self.is_protected(mod.target):
            return False
        
        # Validate modification structure
        if not self.validate_structure(mod):
            return False
        
        return True
    
    def verify(self, mod: Modification) -> bool:
        """Post-modification verification."""
        # Run capability tests
        for capability in self.critical_capabilities:
            if not self.test_capability(capability):
                return False
        
        # Run regression tests
        for test in self.test_suite:
            if not test.run():
                return False
        
        return True
    
    def test_capability(self, capability: str) -> bool:
        """Test if a capability still works."""
        test = self.get_capability_test(capability)
        result = test.execute()
        return result['success']
```

## Version Control

```python
class VersionControl:
    """Manage versions and snapshots."""
    
    def __init__(self):
        self.snapshots: Dict[str, Dict] = {}
        self.current_version = 0
        
    def create_snapshot(self) -> str:
        """Create a snapshot of current state."""
        snapshot_id = f"snap_{self.current_version}"
        self.snapshots[snapshot_id] = {
            'version': self.current_version,
            'state': self.capture_state(),
            'timestamp': time.time()
        }
        self.current_version += 1
        return snapshot_id
    
    def restore_snapshot(self, snapshot_id: str) -> bool:
        """Restore to a snapshot."""
        if snapshot_id not in self.snapshots:
            return False
        
        snapshot = self.snapshots[snapshot_id]
        self.restore_state(snapshot['state'])
        return True
    
    def get_modification_diff(self, from_version: int, 
                              to_version: int) -> List[Modification]:
        """Get modifications between versions."""
        # Return all modifications in range
        pass
```

## Process

1. **Identify Improvement**: Analyze what can be improved
2. **Propose Modification**: Create modification proposal
3. **Safety Pre-Check**: Verify modification is safe
4. **Create Snapshot**: Save current state
5. **Apply Change**: Execute modification
6. **Verify**: Test capabilities still work
7. **Commit or Rollback**: Finalize or revert

## Output

- Modification history
- Capability test results
- Version snapshots
- Performance metrics
