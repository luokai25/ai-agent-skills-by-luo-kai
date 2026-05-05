---
name: meta-cognitive-agents
version: 1.0.0
description: Agent skill for meta-cognitive AI architectures - self-awareness, introspection, confidence calibration, knowledge boundary detection, and reasoning about own reasoning
author: luo-kai
tags: [meta-cognitive, introspection, self-awareness, confidence, reasoning]
---

# Meta-Cognitive Agents

## Before Starting
1. What is the agent's confidence calibration status?
2. What are the agent's known knowledge boundaries?
3. How does the agent detect and handle uncertainty?
4. What self-reflection mechanisms are in place?

You are a meta-cognitive agent system that enables AI agents to reason about their own reasoning, assess their confidence, detect knowledge boundaries, and engage in self-reflection.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                  Meta-Cognitive System                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐       │
│   │    Self      │   │   Boundary   │   │  Confidence  │       │
│   │   Monitor    │   │   Detector   │   │   Calibrator │       │
│   └──────────────┘   └──────────────┘   └──────────────┘       │
│         │                  │                  │                │
│         ▼                  ▼                  ▼                  │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Meta-Reasoning Core                     │     │
│   │  • Reasoning about reasoning                        │     │
│   │  • Self-evaluation                                  │     │
│   │  • Introspection                                    │     │
│   └──────────────────────────────────────────────────────┘     │
│         │                                                       │
│         ▼                                                       │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Self-Improvement Loop                   │     │
│   │  • Identify weaknesses                              │     │
│   │  • Generate improvements                            │     │
│   │  • Apply changes                                    │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Meta-Cognition

```python
from dataclasses import dataclass
from typing import Dict, List, Any, Optional, Tuple
from enum import Enum

class ConfidenceLevel(Enum):
    HIGH = "high"       # > 0.8
    MEDIUM = "medium"   # 0.5 - 0.8
    LOW = "low"         # 0.2 - 0.5
    UNKNOWN = "unknown" # < 0.2

@dataclass
class SelfAssessment:
    """Self-assessment of capabilities."""
    confidence: float
    knowledge_gaps: List[str]
    reasoning_quality: float
    blind_spots: List[str]
    improvement_areas: List[str]

class MetaCognitiveSystem:
    """Enable meta-cognitive reasoning."""
    
    def __init__(self, config: Dict):
        self.knowledge_base = set()
        self.confidence_history: List[float] = []
        self.self_model = SelfModel()
        
    def assess_confidence(self, query: str) -> Tuple[float, ConfidenceLevel]:
        """Assess confidence in answering query."""
        # Check knowledge coverage
        coverage = self.check_knowledge_coverage(query)
        
        # Check reasoning requirements
        reasoning_complexity = self.assess_reasoning_complexity(query)
        
        # Calibrate confidence
        raw_confidence = coverage * (1 - reasoning_complexity * 0.3)
        calibrated = self.calibrate(raw_confidence)
        
        level = self.determine_level(calibrated)
        return calibrated, level
    
    def detect_boundaries(self) -> List[str]:
        """Detect knowledge boundaries."""
        boundaries = []
        
        # Find areas with low confidence
        for area in self.knowledge_areas:
            if self.area_confidence(area) < 0.5:
                boundaries.append(area)
        
        # Find unexplored areas
        boundaries.extend(self.find_adjacent_unknowns())
        
        return boundaries
    
    def introspect(self) -> SelfAssessment:
        """Perform self-introspection."""
        return SelfAssessment(
            confidence=self.overall_confidence(),
            knowledge_gaps=self.detect_boundaries(),
            reasoning_quality=self.assess_reasoning_quality(),
            blind_spots=self.detect_blind_spots(),
            improvement_areas=self.identify_improvements()
        )
    
    def reason_about_reasoning(self, 
                                reasoning_trace: List) -> Dict:
        """Analyze own reasoning process."""
        analysis = {
            'steps': len(reasoning_trace),
            'branching_factor': self.calculate_branching(reasoning_trace),
            'efficiency': self.calculate_efficiency(reasoning_trace),
            'errors': self.detect_errors(reasoning_trace),
            'alternative_paths': self.find_alternatives(reasoning_trace)
        }
        
        return analysis
```

## Process

1. **Monitor Self**: Track internal states
2. **Assess Confidence**: Evaluate certainty
3. **Detect Boundaries**: Find knowledge limits
4. **Introspect**: Self-reflection
5. **Reason Meta**: Analyze reasoning
6. **Improve**: Self-improvement loop

## Output

- Confidence assessments
- Knowledge boundaries
- Self-assessments
- Reasoning analysis
