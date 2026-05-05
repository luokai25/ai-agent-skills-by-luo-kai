---
name: predictive-code-generation
version: 1.0.0
description: Agent skill for predictive code generation - anticipating developer needs, generating code before requests, learning from patterns, and proactive code synthesis
author: luo-kai
tags: [predictive, code-generation, proactive, pattern-learning, synthesis]
---

# Predictive Code Generation

## Before Starting
1. What is the current development context?
2. What patterns have been observed?
3. What is the prediction confidence threshold?
4. How are predictions presented?

You are a predictive code generation system that anticipates developer needs and generates code proactively. You learn from patterns, predict next actions, and synthesize code before explicit requests.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                Predictive Code Generation System                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────┐        │
│  │   Context    │   │   Pattern    │   │  Prediction  │        │
│  │   Monitor    │──▶│   Learner    │──▶│   Engine     │        │
│  │              │   │              │   │              │        │
│  └──────────────┘   └──────────────┘   └──────────────┘        │
│         │                  │                  │                │
│         ▼                  ▼                  ▼                │
│  ┌──────────────────────────────────────────────────────┐      │
│  │              Code Synthesis Engine                   │      │
│  │  • Template instantiation   • Pattern completion   │      │
│  │  • Type inference           • Dependency analysis  │      │
│  │  • Test generation          • Documentation        │      │
│  └──────────────────────────────────────────────────────┘      │
│         │                                                       │
│         ▼                                                       │
│  ┌──────────────────────────────────────────────────────┐      │
│  │              Prediction Queue                        │      │
│  │  • Confidence-ranked predictions                    │      │
│  │  • Pre-generated code snippets                      │      │
│  │  • Ready-to-apply transformations                   │      │
│  └──────────────────────────────────────────────────────┘      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Context Monitoring

```python
class ContextMonitor:
    """Monitor development context for prediction triggers."""
    
    def __init__(self):
        self.file_history = []
        self.edit_patterns = []
        self.import_history = []
        self.function_signatures = {}
        
    def observe_file_open(self, file_path: str):
        """Record file open event."""
        self.file_history.append({
            'path': file_path,
            'action': 'open',
            'timestamp': time.time()
        })
        
        # Analyze file context
        context = self.analyze_file_context(file_path)
        return context
    
    def observe_edit(self, file_path: str, edit_type: str, 
                     location: tuple, content: str):
        """Record and analyze edit."""
        edit = {
            'path': file_path,
            'type': edit_type,
            'location': location,
            'content': content,
            'timestamp': time.time()
        }
        self.edit_patterns.append(edit)
        
        # Extract patterns
        pattern = self.extract_pattern(edit)
        return pattern
    
    def get_recent_context(self, window_size: int = 10):
        """Get recent development context."""
        recent_files = self.file_history[-window_size:]
        recent_edits = self.edit_patterns[-window_size:]
        
        return {
            'files': [f['path'] for f in recent_files],
            'edits': recent_edits,
            'active_languages': self.detect_languages(recent_files),
            'current_task': self.infer_current_task(recent_edits)
        }
```

## Pattern Learning

```python
class PatternLearner:
    """Learn patterns from development history."""
    
    def __init__(self):
        self.sequences = []
        self.associations = {}
        self.templates = {}
        
    def learn_sequence(self, actions: List[Dict]):
        """Learn action sequences."""
        # Extract sequence pattern
        sequence = self.extract_sequence(actions)
        
        # Find similar sequences
        similar = self.find_similar_sequences(sequence)
        
        if similar:
            # Update existing pattern
            self.update_pattern(similar, sequence)
        else:
            # Create new pattern
            self.create_pattern(sequence)
    
    def learn_association(self, trigger: str, response: str, 
                         confidence: float):
        """Learn trigger-response associations."""
        if trigger not in self.associations:
            self.associations[trigger] = []
        
        # Add or update association
        existing = [a for a in self.associations[trigger] 
                    if a['response'] == response]
        
        if existing:
            existing[0]['confidence'] = (
                0.9 * existing[0]['confidence'] + 0.1 * confidence
            )
        else:
            self.associations[trigger].append({
                'response': response,
                'confidence': confidence,
                'count': 1
            })
    
    def predict_next(self, current_state: Dict) -> List[Dict]:
        """Predict next actions based on patterns."""
        predictions = []
        
        # Sequence-based prediction
        sequence_matches = self.match_sequences(current_state)
        for match in sequence_matches:
            predictions.append({
                'type': 'sequence',
                'action': match['next_action'],
                'confidence': match['confidence']
            })
        
        # Association-based prediction
        triggers = self.extract_triggers(current_state)
        for trigger in triggers:
            if trigger in self.associations:
                for assoc in self.associations[trigger]:
                    predictions.append({
                        'type': 'association',
                        'trigger': trigger,
                        'action': assoc['response'],
                        'confidence': assoc['confidence']
                    })
        
        # Rank and return top predictions
        predictions.sort(key=lambda x: x['confidence'], reverse=True)
        return predictions[:5]
```

## Code Synthesis

```python
class CodeSynthesizer:
    """Synthesize code based on predictions."""
    
    def __init__(self, pattern_learner: PatternLearner):
        self.learner = pattern_learner
        self.templates = self.load_templates()
        
    def synthesize(self, prediction: Dict, context: Dict) -> str:
        """Synthesize code for a prediction."""
        if prediction['type'] == 'sequence':
            return self.synthesize_from_sequence(prediction, context)
        elif prediction['type'] == 'association':
            return self.synthesize_from_association(prediction, context)
        elif prediction['type'] == 'template':
            return self.synthesize_from_template(prediction, context)
    
    def synthesize_from_sequence(self, prediction: Dict, 
                                  context: Dict) -> str:
        """Synthesize code from learned sequence."""
        action = prediction['action']
        
        # Get relevant context
        file_context = context.get('current_file', {})
        language = file_context.get('language', 'python')
        
        # Generate code
        if action['type'] == 'add_function':
            return self.generate_function(action, file_context)
        elif action['type'] == 'add_class':
            return self.generate_class(action, file_context)
        elif action['type'] == 'add_test':
            return self.generate_test(action, file_context)
        elif action['type'] == 'refactor':
            return self.generate_refactoring(action, file_context)
    
    def generate_function(self, action: Dict, context: Dict) -> str:
        """Generate function based on action and context."""
        # Infer function signature from context
        name = action.get('name', self.infer_function_name(context))
        params = self.infer_parameters(action, context)
        return_type = self.infer_return_type(action, context)
        
        # Generate function body
        body = self.generate_function_body(action, context)
        
        # Generate code
        code = f"""
def {name}({params}) -> {return_type}:
    \"\"\"
    {self.generate_docstring(action, context)}
    \"\"\"
    {body}
"""
        return code
    
    def generate_test(self, action: Dict, context: Dict) -> str:
        """Generate test for current code."""
        target = action.get('target', context.get('current_function'))
        test_cases = self.generate_test_cases(target, context)
        
        code = f"""
def test_{target['name']}():
    \"\"\"Test cases for {target['name']}\"\"\"
"""
        for case in test_cases:
            code += f"""
    # {case['description']}
    result = {target['name']}({case['inputs']})
    assert result == {case['expected']}, f"Expected {case['expected']}, got {{result}}"
"""
        return code
```

## Prediction Engine

```python
class PredictionEngine:
    """Main prediction engine."""
    
    def __init__(self):
        self.context_monitor = ContextMonitor()
        self.pattern_learner = PatternLearner()
        self.synthesizer = CodeSynthesizer(self.pattern_learner)
        self.prediction_queue = []
        
    def process_event(self, event: Dict):
        """Process development event."""
        # Update context
        if event['type'] == 'file_open':
            context = self.context_monitor.observe_file_open(event['path'])
        elif event['type'] == 'edit':
            pattern = self.context_monitor.observe_edit(
                event['path'], event['edit_type'],
                event['location'], event['content']
            )
            self.pattern_learner.learn_sequence([pattern])
        
        # Generate predictions
        current_context = self.context_monitor.get_recent_context()
        predictions = self.pattern_learner.predict_next(current_context)
        
        # Synthesize code for high-confidence predictions
        for pred in predictions:
            if pred['confidence'] > 0.7:
                code = self.synthesizer.synthesize(pred, current_context)
                self.prediction_queue.append({
                    'prediction': pred,
                    'code': code,
                    'context': current_context
                })
        
        return predictions
    
    def get_ready_predictions(self) -> List[Dict]:
        """Get pre-generated predictions ready to apply."""
        ready = [p for p in self.prediction_queue 
                 if p['prediction']['confidence'] > 0.8]
        return sorted(ready, key=lambda x: x['prediction']['confidence'], 
                     reverse=True)
```

## Proactive Generation

```python
class ProactiveGenerator:
    """Generate code proactively before requests."""
    
    def __init__(self, prediction_engine: PredictionEngine):
        self.engine = prediction_engine
        self.cache = {}
        
    def warm_cache(self, context: Dict):
        """Pre-generate likely needed code."""
        predictions = self.engine.pattern_learner.predict_next(context)
        
        for pred in predictions:
            if pred['confidence'] > 0.6:
                key = self.make_cache_key(pred, context)
                if key not in self.cache:
                    code = self.engine.synthesizer.synthesize(pred, context)
                    self.cache[key] = {
                        'code': code,
                        'prediction': pred,
                        'generated_at': time.time()
                    }
    
    def get_cached(self, trigger: str, context: Dict) -> Optional[str]:
        """Get cached code if available."""
        for key, cached in self.cache.items():
            if cached['prediction'].get('trigger') == trigger:
                return cached['code']
        return None
```

## Process

1. **Monitor Context**: Track development activities
2. **Learn Patterns**: Extract patterns from history
3. **Predict Actions**: Predict likely next actions
4. **Synthesize Code**: Generate code for predictions
5. **Cache Results**: Store for quick retrieval
6. **Present Options**: Offer to developer

## Output

- Predicted actions with confidence
- Pre-generated code snippets
- Pattern analysis report
- Prediction accuracy metrics
