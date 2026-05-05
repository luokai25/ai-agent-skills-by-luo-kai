---
name: agent-dream-systems
version: 1.0.0
description: Agent skill for AI dream-like processing - offline optimization, creative synthesis, memory reorganization, problem incubation, and insight generation during idle periods
author: luo-kai
tags: [dream, offline, optimization, creative, insight, incubation]
---

# Agent Dream Systems

## Before Starting
1. What is the current knowledge state?
2. What problems need incubation?
3. What memories need reorganization?
4. What creative synthesis is needed?

You are an agent dream system that performs offline optimization and creative synthesis during idle periods. You reorganize memories, incubate problems, and generate insights through dream-like processing.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    Agent Dream System                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────┐        │
│  │   Dream      │   │   Memory     │   │   Creative   │        │
│  │   Director   │   │   Weaver     │   │   Engine     │        │
│  └──────────────┘   └──────────────┘   └──────────────┘        │
│         │                  │                  │                │
│         ▼                  ▼                  ▼                │
│  ┌──────────────────────────────────────────────────────┐      │
│  │              Dream Synthesis Core                    │      │
│  │  • Problem incubation                               │      │
│  │  • Memory consolidation                             │      │
│  │  • Insight generation                               │      │
│  │  • Creative combination                             │      │
│  └──────────────────────────────────────────────────────┘      │
│         │                                                       │
│         ▼                                                       │
│  ┌──────────────────────────────────────────────────────┐      │
│  │              Insight Capture                         │      │
│  │  • Dream journaling                                 │      │
│  │  • Insight extraction                               │      │
│  │  • Actionable recommendations                       │      │
│  └──────────────────────────────────────────────────────┘      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Dream Phases

```python
from enum import Enum
from dataclasses import dataclass
from typing import List, Dict, Any, Optional
import random
import numpy as np

class DreamPhase(Enum):
    """Dream phases inspired by sleep cycles."""
    N1_LIGHT = "n1_light"           # Light processing
    N2_SPINDLES = "n2_spindles"     # Memory consolidation
    N3_DEEP = "n3_deep"             # Deep processing
    REM = "rem"                      # Creative synthesis

@dataclass
class DreamState:
    """State during dreaming."""
    phase: DreamPhase
    duration: float
    focus_areas: List[str]
    memories_processed: int
    insights_generated: List[Dict]
    creative_combinations: List[Dict]
```

## Dream Director

```python
class DreamDirector:
    """Orchestrate dream cycles."""
    
    def __init__(self):
        self.current_phase = DreamPhase.N1_LIGHT
        self.phase_duration = {
            DreamPhase.N1_LIGHT: 60,
            DreamPhase.N2_SPINDLES: 120,
            DreamPhase.N3_DEEP: 180,
            DreamPhase.REM: 90
        }
        self.cycle_count = 0
        
    def enter_dream(self, knowledge_state: Dict, 
                    problems: List[Dict]) -> DreamState:
        """Enter dream cycle."""
        # Determine dream focus
        focus = self.determine_focus(knowledge_state, problems)
        
        # Select phase based on needs
        phase = self.select_phase(knowledge_state, problems)
        
        # Create dream state
        state = DreamState(
            phase=phase,
            duration=self.phase_duration[phase],
            focus_areas=focus,
            memories_processed=0,
            insights_generated=[],
            creative_combinations=[]
        )
        
        return state
    
    def determine_focus(self, knowledge: Dict, 
                        problems: List[Dict]) -> List[str]:
        """Determine dream focus areas."""
        focus = []
        
        # Priority: unsolved problems
        for problem in problems:
            if not problem.get('solved'):
                focus.append(f"problem:{problem['id']}")
        
        # Priority: weak memory areas
        for area, strength in knowledge.get('strengths', {}).items():
            if strength < 0.5:
                focus.append(f"memory:{area}")
        
        # Priority: creative synthesis opportunities
        combinations = self.find_synthesis_opportunities(knowledge)
        for combo in combinations[:3]:
            focus.append(f"synthesis:{combo['id']}")
        
        return focus
    
    def cycle_phases(self) -> List[DreamPhase]:
        """Generate dream cycle sequence."""
        # Simulate sleep cycle structure
        cycle = [
            DreamPhase.N1_LIGHT,
            DreamPhase.N2_SPINDLES,
            DreamPhase.N3_DEEP,
            DreamPhase.N2_SPINDLES,
            DreamPhase.REM
        ]
        self.cycle_count += 1
        return cycle
```

## Memory Weaver

```python
class MemoryWeaver:
    """Reorganize and consolidate memories during dreams."""
    
    def __init__(self):
        self.consolidation_threshold = 0.6
        self.forgetting_curve = self.init_forgetting_curve()
        
    def weave_memories(self, memories: List[Dict], 
                       dream_state: DreamState) -> Dict:
        """Process and reorganize memories."""
        results = {
            'consolidated': [],
            'associated': [],
            'compressed': [],
            'forgotten': []
        }
        
        for memory in memories:
            # Apply dream phase processing
            if dream_state.phase == DreamPhase.N2_SPINDLES:
                # Consolidate important memories
                if self.should_consolidate(memory):
                    strengthened = self.strengthen_memory(memory)
                    results['consolidated'].append(strengthened)
            
            elif dream_state.phase == DreamPhase.N3_DEEP:
                # Deep association formation
                associations = self.form_associations(memory, memories)
                results['associated'].extend(associations)
                
                # Memory compression
                compressed = self.compress_memory(memory)
                if compressed:
                    results['compressed'].append(compressed)
            
            elif dream_state.phase == DreamPhase.REM:
                # Creative memory combination
                combinations = self.combine_memories(memory, memories)
                results['associated'].extend(combinations)
        
        dream_state.memories_processed = len(memories)
        return results
    
    def form_associations(self, memory: Dict, 
                          all_memories: List[Dict]) -> List[Dict]:
        """Form new associations between memories."""
        associations = []
        
        for other in all_memories:
            if other['id'] == memory['id']:
                continue
            
            # Calculate association strength
            strength = self.compute_association_strength(memory, other)
            
            if strength > 0.5:
                association = {
                    'source': memory['id'],
                    'target': other['id'],
                    'strength': strength,
                    'type': self.classify_association(memory, other)
                }
                associations.append(association)
        
        return associations
    
    def combine_memories(self, memory: Dict, 
                         all_memories: List[Dict]) -> List[Dict]:
        """Creatively combine memories for new insights."""
        combinations = []
        
        # Find combinable memories
        candidates = [m for m in all_memories 
                      if self.can_combine(memory, m)]
        
        # Random creative combinations
        if len(candidates) >= 2:
            selected = random.sample(candidates, min(3, len(candidates)))
            
            # Create combination
            combination = {
                'type': 'creative_combination',
                'memories': [memory['id']] + [m['id'] for m in selected],
                'synthesis': self.synthesize_combination(memory, selected),
                'novelty_score': self.compute_novelty(memory, selected)
            }
            combinations.append(combination)
        
        return combinations
```

## Creative Engine

```python
class CreativeEngine:
    """Generate creative insights during REM-like phases."""
    
    def __init__(self):
        self.insight_templates = self.load_insight_templates()
        self.analogy_engine = AnalogyEngine()
        
    def generate_insights(self, problems: List[Dict], 
                          knowledge: Dict,
                          dream_state: DreamState) -> List[Dict]:
        """Generate creative insights for problems."""
        insights = []
        
        for problem in problems:
            if problem.get('solved'):
                continue
            
            # Problem incubation
            incubated = self.incubate_problem(problem, knowledge)
            
            # Analogy-based insight
            analogy_insight = self.analogy_engine.find_analogy(
                problem, knowledge
            )
            if analogy_insight:
                insights.append(analogy_insight)
            
            # Combination-based insight
            combo_insight = self.combine_concepts(problem, knowledge)
            if combo_insight:
                insights.append(combo_insight)
            
            # Random insight generation
            random_insight = self.random_association(problem, knowledge)
            if random_insight:
                insights.append(random_insight)
        
        dream_state.insights_generated = insights
        return insights
    
    def incubate_problem(self, problem: Dict, knowledge: Dict) -> Dict:
        """Incubate problem for insight generation."""
        # Deconstruct problem
        components = self.deconstruct_problem(problem)
        
        # Search knowledge for related concepts
        related = []
        for component in components:
            matches = self.search_knowledge(component, knowledge)
            related.extend(matches)
        
        # Relax constraints temporarily
        relaxed = self.relax_constraints(problem)
        
        # Explore solution space
        explorations = self.explore_solutions(relaxed, related)
        
        return {
            'problem_id': problem['id'],
            'components': components,
            'related_concepts': related,
            'explorations': explorations
        }
    
    def combine_concepts(self, problem: Dict, knowledge: Dict) -> Dict:
        """Combine concepts for novel solution."""
        # Select random concepts from knowledge
        concepts = random.sample(
            list(knowledge.get('concepts', {}).keys()),
            min(5, len(knowledge.get('concepts', {})))
        )
        
        # Combine concepts
        combination = self.synthesize_concepts(concepts, knowledge)
        
        # Evaluate relevance to problem
        relevance = self.evaluate_relevance(combination, problem)
        
        if relevance > 0.6:
            return {
                'type': 'concept_combination',
                'problem_id': problem['id'],
                'concepts': concepts,
                'combination': combination,
                'relevance': relevance
            }
        
        return None
```

## Insight Capture

```python
class InsightCapture:
    """Capture and store dream insights."""
    
    def __init__(self):
        self.journal = []
        self.actionable = []
        
    def capture_dream(self, dream_state: DreamState, 
                      processing_results: Dict):
        """Capture dream results."""
        entry = {
            'timestamp': time.time(),
            'phase': dream_state.phase.value,
            'duration': dream_state.duration,
            'focus_areas': dream_state.focus_areas,
            'memories_processed': dream_state.memories_processed,
            'insights': dream_state.insights_generated,
            'creative_combinations': dream_state.creative_combinations,
            'processing_results': processing_results
        }
        
        self.journal.append(entry)
        
        # Extract actionable insights
        actionable = self.extract_actionable(entry)
        self.actionable.extend(actionable)
        
        return entry
    
    def extract_actionable(self, dream_entry: Dict) -> List[Dict]:
        """Extract actionable insights from dream."""
        actionable = []
        
        for insight in dream_entry.get('insights', []):
            if insight.get('relevance', 0) > 0.7:
                action = {
                    'type': 'apply_insight',
                    'insight_id': insight.get('id'),
                    'problem_id': insight.get('problem_id'),
                    'recommendation': self.formulate_recommendation(insight),
                    'confidence': insight.get('relevance')
                }
                actionable.append(action)
        
        for combo in dream_entry.get('creative_combinations', []):
            if combo.get('novelty_score', 0) > 0.6:
                action = {
                    'type': 'explore_combination',
                    'combination': combo,
                    'recommendation': self.formulate_recommendation(combo),
                    'confidence': combo.get('novelty_score')
                }
                actionable.append(action)
        
        return actionable
    
    def get_recommendations(self) -> List[Dict]:
        """Get actionable recommendations from dreams."""
        return sorted(self.actionable, 
                     key=lambda x: x['confidence'], 
                     reverse=True)
```

## Process

1. **Initiate Dream**: Enter dream state based on needs
2. **Cycle Phases**: Progress through dream phases
3. **Process Memories**: Consolidate, associate, compress
4. **Generate Insights**: Create novel combinations
5. **Capture Results**: Journal and extract actionable insights
6. **Return Results**: Present recommendations

## Output

- Dream journal entries
- Consolidated memories
- Generated insights
- Actionable recommendations
- Creative combinations
