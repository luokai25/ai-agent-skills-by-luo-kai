---
name: agent-skill-synthesis
version: 1.0.0
description: Agent skill for synthesizing new skills - combining existing skills, meta-skill creation, skill composition patterns, and autonomous skill development
author: luo-kai
tags: [skill-synthesis, meta-skill, composition, skill-creation, autonomous]
---

# Agent Skill Synthesis

## Before Starting
1. What skills are available to combine?
2. What new capability is needed?
3. What composition patterns apply?
4. How is the new skill validated?

You are an agent skill synthesis system that creates new skills by combining existing skills, developing meta-skills, and composing skill patterns. You enable autonomous skill development.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                Skill Synthesis System                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐       │
│   │    Skill     │   │   Pattern    │   │   Synthesis  │       │
│   │    Library   │   │   Matcher    │   │    Engine    │       │
│   └──────────────┘   └──────────────┘   └──────────────┘       │
│         │                  │                  │                │
│         ▼                  ▼                  ▼                  │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Skill Composer                         │     │
│   │  • Sequential composition                           │     │
│   │  • Parallel composition                             │     │
│   │  • Conditional composition                          │     │
│   │  • Meta-skill creation                              │     │
│   └──────────────────────────────────────────────────────┘     │
│         │                                                       │
│         ▼                                                       │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Skill Validator                         │     │
│   │  • Capability testing                               │     │
│   │  • Integration testing                              │     │
│   │  • Performance benchmarking                         │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Skill Representation

```python
from dataclasses import dataclass
from typing import List, Dict, Any, Optional, Callable
from enum import Enum
import json

class SkillType(Enum):
    ATOMIC = "atomic"        # Base skill
    COMPOSITE = "composite"  # Combination of skills
    META = "meta"           # Skill that operates on skills

@dataclass
class Skill:
    """Skill representation."""
    name: str
    description: str
    skill_type: SkillType
    inputs: List[str]
    outputs: List[str]
    dependencies: List[str]
    implementation: Optional[str]
    composition: Optional[Dict]  # For composite skills
    metadata: Dict

class SkillLibrary:
    """Library of available skills."""
    
    def __init__(self):
        self.skills: Dict[str, Skill] = {}
        self.index = self.build_index()
        
    def register_skill(self, skill: Skill):
        """Register a new skill."""
        self.skills[skill.name] = skill
        self.update_index(skill)
        
    def find_skills_by_capability(self, 
                                   capability: str) -> List[Skill]:
        """Find skills providing a capability."""
        return [s for s in self.skills.values() 
                if capability in s.outputs]
    
    def find_compatible_skills(self, skill: Skill) -> List[Skill]:
        """Find skills that can be composed with given skill."""
        compatible = []
        
        for other in self.skills.values():
            if other.name == skill.name:
                continue
                
            # Check if outputs match inputs
            for output in skill.outputs:
                if output in other.inputs:
                    compatible.append(other)
                    
        return compatible
```

## Composition Patterns

```python
class CompositionPattern(Enum):
    """Skill composition patterns."""
    SEQUENTIAL = "sequential"      # A → B → C
    PARALLEL = "parallel"          # A and B simultaneously
    CONDITIONAL = "conditional"    # If X then A else B
    ITERATIVE = "iterative"        # Repeat A while condition
    PIPELINE = "pipeline"          # A | B | C
    BRANCHING = "branching"        # A → (B, C, D)
    MERGING = "merging"            # (A, B, C) → D

class SkillComposer:
    """Compose skills into new skills."""
    
    def __init__(self, library: SkillLibrary):
        self.library = library
        
    def compose_sequential(self, skills: List[Skill]) -> Skill:
        """Compose skills sequentially."""
        # Verify compatibility
        for i in range(len(skills) - 1):
            if not self.are_compatible(skills[i], skills[i+1]):
                raise ValueError(f"Incompatible: {skills[i].name} → {skills[i+1].name}")
        
        # Create composite skill
        return Skill(
            name=self.generate_name(skills, "seq"),
            description=self.generate_description(skills, "sequential"),
            skill_type=SkillType.COMPOSITE,
            inputs=skills[0].inputs,
            outputs=skills[-1].outputs,
            dependencies=[s.name for s in skills],
            implementation=None,
            composition={
                'pattern': CompositionPattern.SEQUENTIAL.value,
                'skills': [s.name for s in skills]
            },
            metadata={'created_at': time.time()}
        )
    
    def compose_parallel(self, skills: List[Skill]) -> Skill:
        """Compose skills in parallel."""
        # All skills must share inputs
        common_inputs = self.find_common_inputs(skills)
        
        return Skill(
            name=self.generate_name(skills, "par"),
            description=self.generate_description(skills, "parallel"),
            skill_type=SkillType.COMPOSITE,
            inputs=list(common_inputs),
            outputs=list(set(o for s in skills for o in s.outputs)),
            dependencies=[s.name for s in skills],
            implementation=None,
            composition={
                'pattern': CompositionPattern.PARALLEL.value,
                'skills': [s.name for s in skills]
            },
            metadata={'created_at': time.time()}
        )
    
    def compose_conditional(self, condition: str,
                           if_skill: Skill,
                           else_skill: Optional[Skill]) -> Skill:
        """Compose conditional skill execution."""
        return Skill(
            name=self.generate_name([if_skill, else_skill], "cond"),
            description=f"Conditionally execute based on {condition}",
            skill_type=SkillType.COMPOSITE,
            inputs=list(set(if_skill.inputs + (else_skill.inputs if else_skill else []))),
            outputs=list(set(if_skill.outputs + (else_skill.outputs if else_skill else []))),
            dependencies=[if_skill.name] + ([else_skill.name] if else_skill else []),
            implementation=None,
            composition={
                'pattern': CompositionPattern.CONDITIONAL.value,
                'condition': condition,
                'if_skill': if_skill.name,
                'else_skill': else_skill.name if else_skill else None
            },
            metadata={'created_at': time.time()}
        )
```

## Synthesis Engine

```python
class SynthesisEngine:
    """Synthesize new skills from requirements."""
    
    def __init__(self, library: SkillLibrary):
        self.library = library
        self.composer = SkillComposer(library)
        self.pattern_matcher = PatternMatcher()
        
    def synthesize(self, requirements: Dict) -> Skill:
        """Synthesize skill meeting requirements."""
        # Analyze requirements
        inputs = requirements.get('inputs', [])
        outputs = requirements.get('outputs', [])
        constraints = requirements.get('constraints', {})
        
        # Find skill path
        path = self.find_skill_path(inputs, outputs, constraints)
        
        if not path:
            # No direct path, try composition
            path = self.find_composition_path(inputs, outputs, constraints)
        
        if not path:
            raise ValueError("Cannot synthesize skill for requirements")
        
        # Compose skill from path
        if len(path) == 1:
            return path[0]
        else:
            return self.composer.compose_sequential(path)
    
    def find_skill_path(self, inputs: List[str], outputs: List[str],
                        constraints: Dict) -> List[Skill]:
        """Find path of skills connecting inputs to outputs."""
        # BFS from inputs to outputs
        queue = [(input_type, []) for input_type in inputs]
        visited = set()
        
        while queue:
            current_type, path = queue.pop(0)
            
            if current_type in outputs:
                return path
            
            # Find skills that take current as input
            skills = self.library.find_skills_by_input(current_type)
            
            for skill in skills:
                if skill.name in visited:
                    continue
                    
                visited.add(skill.name)
                new_path = path + [skill]
                
                for output in skill.outputs:
                    queue.append((output, new_path))
        
        return []
    
    def find_composition_path(self, inputs: List[str], 
                               outputs: List[str],
                               constraints: Dict) -> List[Skill]:
        """Find composition of skills to achieve outputs."""
        # Try different composition patterns
        patterns = [
            CompositionPattern.SEQUENTIAL,
            CompositionPattern.PARALLEL,
            CompositionPattern.PIPELINE
        ]
        
        for pattern in patterns:
            path = self.try_pattern(pattern, inputs, outputs, constraints)
            if path:
                return path
        
        return []
```

## Meta-Skill Creation

```python
class MetaSkillCreator:
    """Create meta-skills that operate on skills."""
    
    def __init__(self, library: SkillLibrary):
        self.library = library
        
    def create_skill_optimizer(self, skill: Skill) -> Skill:
        """Create meta-skill that optimizes a skill."""
        return Skill(
            name=f"optimize_{skill.name}",
            description=f"Optimize {skill.name} for better performance",
            skill_type=SkillType.META,
            inputs=['skill_instance', 'optimization_target'],
            outputs=['optimized_skill'],
            dependencies=[skill.name],
            implementation=self.generate_optimizer(skill),
            composition={
                'pattern': 'meta',
                'target_skill': skill.name
            },
            metadata={'meta_type': 'optimizer'}
        )
    
    def create_skill_validator(self, skill: Skill) -> Skill:
        """Create meta-skill that validates skill execution."""
        return Skill(
            name=f"validate_{skill.name}",
            description=f"Validate {skill.name} execution correctness",
            skill_type=SkillType.META,
            inputs=['skill_instance', 'expected_output'],
            outputs=['validation_result'],
            dependencies=[skill.name],
            implementation=self.generate_validator(skill),
            composition={
                'pattern': 'meta',
                'target_skill': skill.name
            },
            metadata={'meta_type': 'validator'}
        )
    
    def create_skill_adapter(self, from_skill: Skill, 
                             to_skill: Skill) -> Skill:
        """Create meta-skill that adapts between skills."""
        return Skill(
            name=f"adapt_{from_skill.name}_to_{to_skill.name}",
            description=f"Adapt output of {from_skill.name} to input of {to_skill.name}",
            skill_type=SkillType.META,
            inputs=from_skill.outputs,
            outputs=to_skill.inputs,
            dependencies=[from_skill.name, to_skill.name],
            implementation=self.generate_adapter(from_skill, to_skill),
            composition={
                'pattern': 'adapter',
                'from': from_skill.name,
                'to': to_skill.name
            },
            metadata={'meta_type': 'adapter'}
        )
```

## Skill Validator

```python
class SkillValidator:
    """Validate synthesized skills."""
    
    def __init__(self):
        self.test_suites = {}
        
    def validate_skill(self, skill: Skill) -> Dict:
        """Validate a skill comprehensively."""
        results = {
            'valid': True,
            'errors': [],
            'warnings': [],
            'performance': {}
        }
        
        # Static validation
        static_results = self.static_validation(skill)
        results['errors'].extend(static_results['errors'])
        results['warnings'].extend(static_results['warnings'])
        
        # Dynamic validation
        if skill.skill_type == SkillType.COMPOSITE:
            dynamic_results = self.dynamic_validation(skill)
            results['errors'].extend(dynamic_results['errors'])
            results['performance'] = dynamic_results['performance']
        
        # Integration validation
        integration_results = self.integration_validation(skill)
        results['warnings'].extend(integration_results['warnings'])
        
        results['valid'] = len(results['errors']) == 0
        
        return results
    
    def static_validation(self, skill: Skill) -> Dict:
        """Static code analysis of skill."""
        errors = []
        warnings = []
        
        # Check dependencies exist
        for dep in skill.dependencies:
            if dep not in self.test_suites and not self.skill_exists(dep):
                errors.append(f"Missing dependency: {dep}")
        
        # Check composition validity
        if skill.composition:
            pattern = skill.composition.get('pattern')
            if pattern not in [p.value for p in CompositionPattern]:
                errors.append(f"Invalid composition pattern: {pattern}")
        
        return {'errors': errors, 'warnings': warnings}
```

## Process

1. **Analyze Requirements**: Understand desired capability
2. **Find Skills**: Locate relevant existing skills
3. **Match Patterns**: Identify composition patterns
4. **Compose Skills**: Create new composite skill
5. **Validate**: Test the new skill
6. **Register**: Add to skill library

## Output

- New synthesized skill
- Skill composition diagram
- Validation report
- Performance metrics
