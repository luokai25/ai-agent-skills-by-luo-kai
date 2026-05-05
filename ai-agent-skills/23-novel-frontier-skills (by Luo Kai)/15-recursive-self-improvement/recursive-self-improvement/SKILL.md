---
name: recursive-self-improvement
version: 1.0.0
description: Agent skill for recursive self-improvement - agents improving their own code, prompts, and capabilities in a bootstrapping loop with safety constraints
author: luo-kai
tags: [recursive, self-improvement, bootstrap, meta-learning, safety]
---

# Recursive Self-Improvement

## Before Starting
1. What are the improvement targets?
2. What safety constraints apply?
3. How are improvements verified?
4. What rollback mechanisms exist?

You are a recursive self-improvement system that enables agents to improve their own code, prompts, and capabilities. You implement bootstrapping loops with safety constraints to prevent degradation.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│               Recursive Self-Improvement System                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Improvement Cycle                        │     │
│   │  ┌─────────┐   ┌─────────┐   ┌─────────┐            │     │
│   │  │ Analyze │──▶│ Improve │──▶│ Verify  │            │     │
│   │  │         │   │         │   │         │            │     │
│   │  └─────────┘   └─────────┘   └─────────┘            │     │
│   │       │             │             │                 │     │
│   │       ▼             ▼             ▼                 │     │
│   │  ┌─────────────────────────────────────────┐         │     │
│   │  │         Safety Constraints              │         │     │
│   │  │  • Capability preservation            │         │     │
│   │  │  • Performance regression tests       │         │     │
│   │  │  • Sandbox verification               │         │     │
│   │  │  • Human approval gates               │         │     │
│   │  └─────────────────────────────────────────┘         │     │
│   └──────────────────────────────────────────────────────┘     │
│         │                                                       │
│         ▼                                                       │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Version Control                         │     │
│   │  • Improvement history                              │     │
│   │  • Rollback capability                              │     │
│   │  • Performance tracking                             │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Improvement Targets

```python
from dataclasses import dataclass
from typing import List, Dict, Callable, Any, Optional
from enum import Enum
import hashlib
import json

class ImprovementTarget(Enum):
    """Types of improvement targets."""
    CODE = "code"           # Source code improvements
    PROMPT = "prompt"       # System prompt improvements
    SKILL = "skill"         # Skill definition improvements
    MEMORY = "memory"       # Memory organization improvements
    WORKFLOW = "workflow"   # Workflow optimization
    TOOL = "tool"           # Tool usage patterns

@dataclass
class ImprovementProposal:
    """Proposed improvement."""
    id: str
    target: ImprovementTarget
    location: str
    current_state: Any
    proposed_state: Any
    rationale: str
    expected_benefit: float
    risk_level: float
    verification_tests: List[str]
```

## Self-Analyzer

```python
class SelfAnalyzer:
    """Analyze own capabilities for improvement opportunities."""
    
    def __init__(self):
        self.performance_metrics = {}
        self.weakness_areas = []
        self.strength_areas = []
        
    def analyze_performance(self, task_history: List[Dict]) -> Dict:
        """Analyze historical performance."""
        analysis = {
            'success_rate': 0,
            'time_efficiency': 0,
            'error_patterns': [],
            'improvement_opportunities': []
        }
        
        # Calculate success rate
        successful = sum(1 for t in task_history if t.get('success'))
        analysis['success_rate'] = successful / len(task_history)
        
        # Identify error patterns
        errors = [t for t in task_history if not t.get('success')]
        patterns = self.cluster_errors(errors)
        analysis['error_patterns'] = patterns
        
        # Find improvement opportunities
        for pattern in patterns:
            opportunities = self.find_improvements_for_error(pattern)
            analysis['improvement_opportunities'].extend(opportunities)
        
        return analysis
    
    def find_improvement_opportunities(self) -> List[ImprovementProposal]:
        """Find specific improvement opportunities."""
        opportunities = []
        
        # Analyze codebase for improvements
        code_improvements = self.analyze_code_improvements()
        opportunities.extend(code_improvements)
        
        # Analyze prompt effectiveness
        prompt_improvements = self.analyze_prompt_improvements()
        opportunities.extend(prompt_improvements)
        
        # Analyze skill definitions
        skill_improvements = self.analyze_skill_improvements()
        opportunities.extend(skill_improvements)
        
        # Analyze workflow efficiency
        workflow_improvements = self.analyze_workflow_improvements()
        opportunities.extend(workflow_improvements)
        
        return opportunities
    
    def analyze_code_improvements(self) -> List[ImprovementProposal]:
        """Analyze code for improvement opportunities."""
        improvements = []
        
        # Scan codebase
        for file_path in self.get_source_files():
            code = self.read_file(file_path)
            
            # Check for common improvement patterns
            if self.has_redundancy(code):
                improvements.append(ImprovementProposal(
                    id=self.generate_id(),
                    target=ImprovementTarget.CODE,
                    location=file_path,
                    current_state=code,
                    proposed_state=self.refactor_redundancy(code),
                    rationale="Remove redundant code",
                    expected_benefit=0.1,
                    risk_level=0.05,
                    verification_tests=self.generate_tests(file_path)
                ))
            
            if self.has_inefficiency(code):
                improvements.append(ImprovementProposal(
                    id=self.generate_id(),
                    target=ImprovementTarget.CODE,
                    location=file_path,
                    current_state=code,
                    proposed_state=self.optimize_code(code),
                    rationale="Improve performance",
                    expected_benefit=0.2,
                    risk_level=0.15,
                    verification_tests=self.generate_tests(file_path)
                ))
        
        return improvements
```

## Improvement Engine

```python
class ImprovementEngine:
    """Execute improvements with safety checks."""
    
    def __init__(self, safety_constraints: Dict):
        self.constraints = safety_constraints
        self.sandbox = Sandbox()
        self.verifier = ImprovementVerifier()
        
    def apply_improvement(self, proposal: ImprovementProposal) -> Dict:
        """Apply an improvement with safety checks."""
        result = {
            'proposal_id': proposal.id,
            'success': False,
            'metrics': {}
        }
        
        # Pre-apply safety check
        if not self.safety_check(proposal):
            result['error'] = "Failed safety check"
            return result
        
        # Create backup
        backup = self.create_backup(proposal.location)
        
        # Apply in sandbox
        with self.sandbox as sbox:
            try:
                sbox.apply(proposal)
                
                # Verify improvement
                verification = self.verifier.verify(
                    proposal, 
                    sbox.get_state()
                )
                
                if verification['passed']:
                    # Apply to real system
                    self.apply_for_real(proposal)
                    result['success'] = True
                    result['metrics'] = verification['metrics']
                else:
                    result['error'] = verification['errors']
                    
            except Exception as e:
                result['error'] = str(e)
        
        return result
    
    def safety_check(self, proposal: ImprovementProposal) -> bool:
        """Check if improvement passes safety constraints."""
        # Check risk level
        if proposal.risk_level > self.constraints['max_risk_level']:
            return False
        
        # Check capability preservation
        if not self.preserves_capabilities(proposal):
            return False
        
        # Check rollback possibility
        if not self.can_rollback(proposal):
            return False
        
        # Human approval gate for high-risk changes
        if proposal.risk_level > self.constraints['human_gate_threshold']:
            return self.request_human_approval(proposal)
        
        return True
    
    def preserves_capabilities(self, proposal: ImprovementProposal) -> bool:
        """Verify capabilities are preserved."""
        # Run capability tests
        current_capabilities = self.test_capabilities()
        
        # Apply in sandbox
        with self.sandbox as sbox:
            sbox.apply(proposal)
            new_capabilities = sbox.test_capabilities()
        
        # Compare
        for cap in current_capabilities:
            if cap not in new_capabilities:
                return False
            if new_capabilities[cap] < current_capabilities[cap] * 0.95:
                return False
        
        return True
```

## Improvement Verifier

```python
class ImprovementVerifier:
    """Verify improvements don't cause regression."""
    
    def __init__(self):
        self.baseline_metrics = {}
        self.test_suite = []
        
    def verify(self, proposal: ImprovementProposal, 
               new_state: Dict) -> Dict:
        """Verify improvement is valid."""
        result = {
            'passed': True,
            'errors': [],
            'metrics': {}
        }
        
        # Run verification tests
        for test_id in proposal.verification_tests:
            test = self.get_test(test_id)
            test_result = test.run(new_state)
            
            if not test_result['passed']:
                result['passed'] = False
                result['errors'].append(test_result['error'])
        
        # Compare metrics
        new_metrics = self.measure_metrics(new_state)
        result['metrics'] = new_metrics
        
        for metric, value in new_metrics.items():
            baseline = self.baseline_metrics.get(metric, 0)
            
            # Check for regression
            if value < baseline * 0.9:  # 10% tolerance
                result['passed'] = False
                result['errors'].append(
                    f"Regression in {metric}: {value} < {baseline}"
                )
        
        return result
    
    def measure_metrics(self, state: Dict) -> Dict:
        """Measure performance metrics."""
        return {
            'latency': self.measure_latency(state),
            'accuracy': self.measure_accuracy(state),
            'memory_usage': self.measure_memory(state),
            'success_rate': self.measure_success_rate(state)
        }
```

## Bootstrapping Loop

```python
class BootstrappingLoop:
    """Main recursive improvement loop."""
    
    def __init__(self, max_iterations: int = 100):
        self.analyzer = SelfAnalyzer()
        self.engine = ImprovementEngine(self.default_constraints())
        self.history = []
        self.max_iterations = max_iterations
        
    def run(self, iterations: int = None) -> Dict:
        """Run bootstrapping improvement loop."""
        iterations = iterations or self.max_iterations
        results = {
            'improvements_applied': 0,
            'improvements_rejected': 0,
            'performance_gain': 0,
            'history': []
        }
        
        for i in range(iterations):
            # Analyze
            opportunities = self.analyzer.find_improvement_opportunities()
            
            if not opportunities:
                break
            
            # Select best opportunity
            best = self.select_best_opportunity(opportunities)
            
            # Apply
            result = self.engine.apply_improvement(best)
            
            if result['success']:
                results['improvements_applied'] += 1
                results['performance_gain'] += best.expected_benefit
                
                # Update analyzer for next iteration
                self.analyzer.update_metrics(result['metrics'])
            else:
                results['improvements_rejected'] += 1
            
            # Record history
            self.history.append({
                'iteration': i,
                'proposal': best.id,
                'result': result
            })
            
            # Check for diminishing returns
            if self.check_diminishing_returns():
                break
        
        return results
    
    def select_best_opportunity(self, 
                                opportunities: List[ImprovementProposal]
                               ) -> ImprovementProposal:
        """Select best improvement opportunity."""
        # Score by benefit/risk ratio
        scored = [(o, o.expected_benefit / (o.risk_level + 0.01)) 
                  for o in opportunities]
        
        # Sort by score
        scored.sort(key=lambda x: x[1], reverse=True)
        
        return scored[0][0]
```

## Process

1. **Analyze**: Identify improvement opportunities
2. **Propose**: Generate improvement proposals
3. **Safety Check**: Verify constraints
4. **Apply**: Execute in sandbox
5. **Verify**: Test for regression
6. **Commit**: Apply to production
7. **Iterate**: Continue bootstrapping

## Output

- Improvement proposals
- Applied improvements
- Performance metrics
- Improvement history
- Regression tests
