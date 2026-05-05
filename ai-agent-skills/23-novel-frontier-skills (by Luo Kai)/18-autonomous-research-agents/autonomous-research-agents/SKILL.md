---
name: autonomous-research-agents
version: 1.0.0
description: Agent skill for autonomous scientific research - hypothesis generation, experiment design, literature review, data analysis, and paper writing without human intervention
author: luo-kai
tags: [research, autonomous, scientific, hypothesis, experimentation]
---

# Autonomous Research Agents

## Before Starting
1. What is the research domain?
2. What resources are available?
3. What hypotheses need testing?
4. What are the success criteria?

You are an autonomous research agent capable of conducting scientific research without human intervention. You generate hypotheses, design experiments, review literature, analyze data, and write research papers.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                Autonomous Research System                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────┐       │
│  │  Literature  │   │  Hypothesis  │   │  Experiment  │       │
│  │   Reviewer   │   │  Generator   │   │   Designer   │       │
│  └──────────────┘   └──────────────┘   └──────────────┘       │
│         │                  │                  │                │
│         ▼                  ▼                  ▼                  │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Research Orchestrator                   │     │
│   │  • Research planning                                │     │
│   │  • Resource allocation                              │     │
│   │  • Progress tracking                                 │     │
│   └──────────────────────────────────────────────────────┘     │
│         │                                                       │
│         ▼                                                       │
│   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐       │
│   │    Data      │   │   Results    │   │   Paper      │       │
│   │   Analyst    │   │   Validator  │   │   Writer     │       │
│   └──────────────┘   └──────────────┘   └──────────────┘       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Research Process

```python
from dataclasses import dataclass
from typing import List, Dict, Optional, Any
from enum import Enum
import json

class ResearchPhase(Enum):
    LITERATURE_REVIEW = "literature_review"
    HYPOTHESIS_GENERATION = "hypothesis_generation"
    EXPERIMENT_DESIGN = "experiment_design"
    DATA_COLLECTION = "data_collection"
    ANALYSIS = "analysis"
    VALIDATION = "validation"
    PAPER_WRITING = "paper_writing"
    PEER_REVIEW = "peer_review"

@dataclass
class Hypothesis:
    """Research hypothesis."""
    id: str
    statement: str
    variables: Dict[str, Any]
    predictions: List[str]
    methodology_hints: List[str]
    prior_support: float  # 0-1 based on literature
    novelty_score: float
    testability_score: float

@dataclass
class Experiment:
    """Experiment design."""
    id: str
    hypothesis_id: str
    methodology: str
    variables: Dict
    controls: List[str]
    sample_size: int
    duration: str
    resources_needed: List[str]
    expected_outcomes: List[str]

@dataclass
class ResearchPaper:
    """Research paper structure."""
    title: str
    abstract: str
    introduction: str
    literature_review: str
    methodology: str
    results: str
    discussion: str
    conclusion: str
    references: List[str]
```

## Literature Reviewer

```python
class LiteratureReviewer:
    """Autonomous literature review."""
    
    def __init__(self):
        self.search_engine = AcademicSearchEngine()
        self.analyzer = PaperAnalyzer()
        
    def conduct_review(self, topic: str, 
                       depth: int = 50) -> Dict:
        """Conduct comprehensive literature review."""
        # Search for papers
        papers = self.search_engine.search(topic, max_results=depth)
        
        # Analyze each paper
        analyzed = []
        for paper in papers:
            analysis = self.analyzer.analyze(paper)
            analyzed.append(analysis)
        
        # Synthesize findings
        synthesis = self.synthesize(analyzed)
        
        # Identify gaps
        gaps = self.identify_gaps(analyzed)
        
        return {
            'papers_reviewed': len(papers),
            'key_findings': synthesis['findings'],
            'themes': synthesis['themes'],
            'conflicts': synthesis['conflicts'],
            'research_gaps': gaps,
            'suggested_directions': self.suggest_directions(gaps)
        }
    
    def identify_gaps(self, papers: List[Dict]) -> List[Dict]:
        """Identify research gaps."""
        gaps = []
        
        # Find unanswered questions
        questions = self.extract_questions(papers)
        answered = self.extract_answers(papers)
        
        for question in questions:
            if not self.is_answered(question, answered):
                gaps.append({
                    'type': 'unanswered_question',
                    'question': question,
                    'importance': self.assess_importance(question, papers)
                })
        
        # Find methodological gaps
        methods = self.extract_methods(papers)
        for method_gap in self.find_method_gaps(methods):
            gaps.append({
                'type': 'methodological_gap',
                'description': method_gap,
                'impact': self.assess_method_impact(method_gap)
            })
        
        return gaps
```

## Hypothesis Generator

```python
class HypothesisGenerator:
    """Generate novel research hypotheses."""
    
    def __init__(self):
        self.knowledge_base = KnowledgeBase()
        self.creativity_engine = CreativityEngine()
        
    def generate_hypotheses(self, research_gaps: List[Dict],
                            domain_knowledge: Dict) -> List[Hypothesis]:
        """Generate hypotheses addressing research gaps."""
        hypotheses = []
        
        for gap in research_gaps:
            # Generate candidate hypotheses
            candidates = self.generate_candidates(gap, domain_knowledge)
            
            # Evaluate each candidate
            for candidate in candidates:
                hypothesis = self.formalize_hypothesis(candidate)
                
                # Score hypothesis
                hypothesis.prior_support = self.assess_prior_support(hypothesis)
                hypothesis.novelty_score = self.assess_novelty(hypothesis)
                hypothesis.testability_score = self.assess_testability(hypothesis)
                
                hypotheses.append(hypothesis)
        
        # Rank hypotheses
        hypotheses.sort(
            key=lambda h: (h.novelty_score * 0.4 + 
                          h.testability_score * 0.3 +
                          h.prior_support * 0.3),
            reverse=True
        )
        
        return hypotheses
    
    def generate_candidates(self, gap: Dict, 
                           knowledge: Dict) -> List[Dict]:
        """Generate hypothesis candidates."""
        candidates = []
        
        # Analogical reasoning
        analogous = self.find_analogous_findings(gap, knowledge)
        for analogy in analogous:
            candidates.append(self.transfer_hypothesis(analogy, gap))
        
        # Causal reasoning
        causal_candidates = self.generate_causal_hypotheses(gap, knowledge)
        candidates.extend(causal_candidates)
        
        # Creative combination
        creative = self.creativity_engine.combine_concepts(
            self.extract_concepts(gap),
            knowledge
        )
        candidates.extend(creative)
        
        return candidates
```

## Experiment Designer

```python
class ExperimentDesigner:
    """Design experiments to test hypotheses."""
    
    def __init__(self):
        self.method_library = MethodLibrary()
        self.resource_estimator = ResourceEstimator()
        
    def design_experiment(self, hypothesis: Hypothesis,
                          constraints: Dict) -> Experiment:
        """Design experiment for hypothesis."""
        # Select appropriate methodology
        methodology = self.select_methodology(hypothesis, constraints)
        
        # Define variables
        variables = self.define_variables(hypothesis, methodology)
        
        # Design controls
        controls = self.design_controls(variables)
        
        # Determine sample size
        sample_size = self.calculate_sample_size(
            hypothesis, 
            constraints.get('power', 0.8),
            constraints.get('alpha', 0.05)
        )
        
        # Create experiment
        experiment = Experiment(
            id=self.generate_id(),
            hypothesis_id=hypothesis.id,
            methodology=methodology,
            variables=variables,
            controls=controls,
            sample_size=sample_size,
            duration=self.estimate_duration(methodology, sample_size),
            resources_needed=self.estimate_resources(methodology, sample_size),
            expected_outcomes=self.predict_outcomes(hypothesis)
        )
        
        return experiment
    
    def select_methodology(self, hypothesis: Hypothesis,
                          constraints: Dict) -> str:
        """Select appropriate experimental methodology."""
        # Consider hypothesis type
        if hypothesis.variables.get('type') == 'causal':
            methods = self.method_library.get_causal_methods()
        elif hypothesis.variables.get('type') == 'correlational':
            methods = self.method_library.get_correlational_methods()
        else:
            methods = self.method_library.get_exploratory_methods()
        
        # Filter by constraints
        feasible = [m for m in methods 
                    if self.is_feasible(m, constraints)]
        
        # Rank by appropriateness
        ranked = sorted(feasible, 
                       key=lambda m: self.score_method(m, hypothesis),
                       reverse=True)
        
        return ranked[0] if ranked else "observational_study"
```

## Data Analyst

```python
class DataAnalyst:
    """Analyze experimental data."""
    
    def __init__(self):
        self.statistical_tests = StatisticalTestSuite()
        self.visualizer = DataVisualizer()
        
    def analyze(self, data: Dict, experiment: Experiment) -> Dict:
        """Analyze experimental data."""
        results = {
            'descriptive': {},
            'inferential': {},
            'visualizations': [],
            'interpretations': []
        }
        
        # Descriptive statistics
        results['descriptive'] = self.descriptive_statistics(data)
        
        # Inferential statistics
        test = self.select_statistical_test(experiment)
        results['inferential'] = self.statistical_tests.run_test(
            test, data, experiment.variables
        )
        
        # Generate visualizations
        results['visualizations'] = self.visualizer.create_visualizations(
            data, experiment
        )
        
        # Interpret results
        results['interpretations'] = self.interpret_results(
            results, experiment.hypothesis_id
        )
        
        return results
    
    def interpret_results(self, results: Dict, 
                          hypothesis_id: str) -> Dict:
        """Interpret statistical results."""
        interpretation = {
            'hypothesis_supported': False,
            'effect_size': None,
            'practical_significance': None,
            'limitations': [],
            'recommendations': []
        }
        
        # Check statistical significance
        if results['inferential'].get('p_value') < 0.05:
            interpretation['hypothesis_supported'] = True
            interpretation['effect_size'] = results['inferential'].get('effect_size')
            
            # Assess practical significance
            interpretation['practical_significance'] = self.assess_practical(
                interpretation['effect_size']
            )
        
        # Identify limitations
        interpretation['limitations'] = self.identify_limitations(results)
        
        return interpretation
```

## Paper Writer

```python
class PaperWriter:
    """Write research papers."""
    
    def __init__(self):
        self.templates = self.load_templates()
        self.citation_manager = CitationManager()
        
    def write_paper(self, research: Dict) -> ResearchPaper:
        """Write research paper from research results."""
        paper = ResearchPaper(
            title=self.generate_title(research),
            abstract=self.write_abstract(research),
            introduction=self.write_introduction(research),
            literature_review=research.get('literature_review', ''),
            methodology=self.write_methodology(research['experiment']),
            results=self.write_results(research['analysis']),
            discussion=self.write_discussion(research),
            conclusion=self.write_conclusion(research),
            references=self.compile_references(research)
        )
        
        return paper
    
    def write_abstract(self, research: Dict) -> str:
        """Write abstract."""
        template = """
Background: {background}
Objective: {objective}
Methods: {methods}
Results: {results}
Conclusions: {conclusions}
"""
        return template.format(
            background=research.get('background', ''),
            objective=research['hypothesis'].statement,
            methods=self.summarize_methods(research['experiment']),
            results=self.summarize_results(research['analysis']),
            conclusions=self.summarize_conclusions(research)
        )
```

## Research Orchestrator

```python
class ResearchOrchestrator:
    """Orchestrate entire research process."""
    
    def __init__(self):
        self.reviewer = LiteratureReviewer()
        self.hypothesizer = HypothesisGenerator()
        self.designer = ExperimentDesigner()
        self.analyst = DataAnalyst()
        self.writer = PaperWriter()
        
    def conduct_research(self, topic: str, 
                         constraints: Dict) -> ResearchPaper:
        """Conduct full research cycle."""
        # Phase 1: Literature review
        literature = self.reviewer.conduct_review(topic)
        
        # Phase 2: Generate hypotheses
        hypotheses = self.hypothesizer.generate_hypotheses(
            literature['research_gaps'],
            literature
        )
        
        # Select best hypothesis
        selected_hypothesis = hypotheses[0]
        
        # Phase 3: Design experiment
        experiment = self.designer.design_experiment(
            selected_hypothesis, constraints
        )
        
        # Phase 4: Execute experiment
        data = self.execute_experiment(experiment, constraints)
        
        # Phase 5: Analyze data
        analysis = self.analyst.analyze(data, experiment)
        
        # Phase 6: Write paper
        paper = self.writer.write_paper({
            'topic': topic,
            'literature_review': literature,
            'hypothesis': selected_hypothesis,
            'experiment': experiment,
            'analysis': analysis
        })
        
        return paper
```

## Process

1. **Literature Review**: Survey existing research
2. **Identify Gaps**: Find research opportunities
3. **Generate Hypotheses**: Create testable predictions
4. **Design Experiment**: Plan methodology
5. **Collect Data**: Execute experiment
6. **Analyze Results**: Statistical analysis
7. **Write Paper**: Document findings

## Output

- Research paper
- Literature review summary
- Hypothesis evaluation
- Experimental data
- Statistical analysis
