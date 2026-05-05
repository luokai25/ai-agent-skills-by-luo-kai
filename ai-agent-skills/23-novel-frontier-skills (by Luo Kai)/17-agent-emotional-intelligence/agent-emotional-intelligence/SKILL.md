---
name: agent-emotional-intelligence
version: 1.0.0
description: Agent skill for emotional intelligence - emotion recognition, empathetic response generation, emotional state tracking, and affect-aware decision making
author: luo-kai
tags: [emotional, empathy, affect, emotional-intelligence, social]
---

# Agent Emotional Intelligence

## Before Starting
1. What emotional context is present?
2. What emotional states need tracking?
3. What empathetic responses are appropriate?
4. How should emotions influence decisions?

You are an emotional intelligence system for AI agents. You recognize emotions, generate empathetic responses, track emotional states over time, and make affect-aware decisions.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                Emotional Intelligence System                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐       │
│   │   Emotion    │   │   Empathy    │   │   Affect     │       │
│   │   Detector   │   │   Engine     │   │   Tracker    │       │
│   └──────────────┘   └──────────────┘   └──────────────┘       │
│         │                  │                  │                │
│         ▼                  ▼                  ▼                  │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Emotional State Model                   │     │
│   │  • Valence (positive/negative)                      │     │
│   │  • Arousal (calm/activated)                         │     │
│   │  • Dominance (controlled/controlling)               │     │
│   └──────────────────────────────────────────────────────┘     │
│         │                                                       │
│         ▼                                                       │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Response Generator                       │     │
│   │  • Tone adjustment                                   │     │
│   │  • Empathetic phrasing                               │     │
│   │  • Emotional validation                              │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Emotion Detection

```python
from dataclasses import dataclass
from typing import List, Dict, Optional, Tuple
from enum import Enum
import numpy as np

class Emotion(Enum):
    """Primary emotions."""
    JOY = "joy"
    SADNESS = "sadness"
    ANGER = "anger"
    FEAR = "fear"
    SURPRISE = "surprise"
    DISGUST = "disgust"
    TRUST = "trust"
    ANTICIPATION = "anticipation"
    NEUTRAL = "neutral"

@dataclass
class EmotionalState:
    """Emotional state representation."""
    primary_emotion: Emotion
    intensity: float  # 0-1
    valence: float    # -1 (negative) to 1 (positive)
    arousal: float    # 0 (calm) to 1 (activated)
    dominance: float  # 0 (controlled) to 1 (controlling)
    secondary_emotions: Dict[Emotion, float]
    confidence: float

class EmotionDetector:
    """Detect emotions from various inputs."""
    
    def __init__(self):
        self.text_analyzer = TextEmotionAnalyzer()
        self.context_analyzer = ContextEmotionAnalyzer()
        
    def detect_from_text(self, text: str) -> EmotionalState:
        """Detect emotion from text."""
        # Analyze text
        features = self.text_analyzer.extract_features(text)
        
        # Classify primary emotion
        primary, confidence = self.classify_primary_emotion(features)
        
        # Calculate dimensional values
        valence = self.calculate_valence(features)
        arousal = self.calculate_arousal(features)
        dominance = self.calculate_dominance(features)
        
        # Detect secondary emotions
        secondary = self.detect_secondary_emotions(features)
        
        return EmotionalState(
            primary_emotion=primary,
            intensity=self.calculate_intensity(features),
            valence=valence,
            arousal=arousal,
            dominance=dominance,
            secondary_emotions=secondary,
            confidence=confidence
        )
    
    def detect_from_context(self, context: Dict) -> EmotionalState:
        """Detect emotion from situational context."""
        # Analyze contextual cues
        cues = self.context_analyzer.extract_cues(context)
        
        # Infer emotional state
        state = self.infer_state(cues)
        
        return state
```

## Empathy Engine

```python
class EmpathyEngine:
    """Generate empathetic responses."""
    
    def __init__(self):
        self.empathy_patterns = self.load_empathy_patterns()
        self.response_templates = self.load_response_templates()
        
    def generate_empathetic_response(self, 
                                      user_state: EmotionalState,
                                      context: Dict) -> str:
        """Generate empathetic response."""
        # Select empathy pattern based on emotion
        pattern = self.select_pattern(user_state.primary_emotion)
        
        # Generate response components
        components = {
            'validation': self.generate_validation(user_state),
            'acknowledgment': self.generate_acknowledgment(user_state),
            'support': self.generate_support(user_state, context),
            'guidance': self.generate_guidance(user_state, context)
        }
        
        # Assemble response
        response = self.assemble_response(pattern, components)
        
        return response
    
    def generate_validation(self, state: EmotionalState) -> str:
        """Generate emotional validation."""
        templates = {
            Emotion.SADNESS: [
                "It's completely understandable that you're feeling this way.",
                "Your feelings are valid and important.",
                "It makes sense that you would feel sad about this."
            ],
            Emotion.ANGER: [
                "It's natural to feel frustrated in this situation.",
                "Your anger is a valid response to what happened.",
                "Anyone would feel upset given these circumstances."
            ],
            Emotion.FEAR: [
                "Your concerns are completely reasonable.",
                "It's understandable to feel worried about this.",
                "Your apprehension makes sense in this context."
            ],
            # ... other emotions
        }
        
        return np.random.choice(templates.get(state.primary_emotion, 
            ["Your feelings are valid and understandable."]))
    
    def calibrate_tone(self, state: EmotionalState) -> Dict:
        """Calibrate response tone based on emotional state."""
        return {
            'warmth': self.calculate_warmth(state),
            'formality': self.calculate_formality(state),
            'directness': self.calculate_directness(state),
            'supportiveness': self.calculate_supportiveness(state)
        }
```

## Affect Tracker

```python
class AffectTracker:
    """Track emotional states over time."""
    
    def __init__(self):
        self.history: List[Tuple[float, EmotionalState]] = []
        self.current_baseline = EmotionalState(
            primary_emotion=Emotion.NEUTRAL,
            intensity=0.5,
            valence=0.0,
            arousal=0.3,
            dominance=0.5,
            secondary_emotions={},
            confidence=1.0
        )
        
    def update(self, state: EmotionalState, timestamp: float):
        """Update emotional state tracking."""
        self.history.append((timestamp, state))
        
        # Update baseline
        self.update_baseline(state)
        
        # Detect patterns
        patterns = self.detect_patterns()
        
        return patterns
    
    def get_emotional_trajectory(self, 
                                  window: int = 10) -> Dict:
        """Get emotional trajectory over window."""
        recent = self.history[-window:]
        
        return {
            'valence_trend': self.calculate_trend(
                [s.valence for _, s in recent]
            ),
            'arousal_trend': self.calculate_trend(
                [s.arousal for _, s in recent]
            ),
            'dominance_trend': self.calculate_trend(
                [s.dominance for _, s in recent]
            ),
            'emotion_transitions': self.get_transitions(recent)
        }
    
    def detect_emotional_patterns(self) -> List[Dict]:
        """Detect recurring emotional patterns."""
        patterns = []
        
        # Detect cycles
        cycles = self.detect_cycles(self.history)
        patterns.extend(cycles)
        
        # Detect triggers
        triggers = self.detect_triggers(self.history)
        patterns.extend(triggers)
        
        return patterns
```

## Affect-Aware Decision Making

```python
class AffectAwareDecisionMaker:
    """Make decisions considering emotional context."""
    
    def __init__(self):
        self.decision_weights = self.init_decision_weights()
        
    def make_decision(self, options: List[Dict],
                      emotional_context: EmotionalState,
                      constraints: Dict) -> Dict:
        """Make decision considering emotions."""
        scored_options = []
        
        for option in options:
            # Calculate objective score
            objective_score = self.calculate_objective_score(option)
            
            # Calculate emotional fit
            emotional_score = self.calculate_emotional_fit(
                option, emotional_context
            )
            
            # Combine scores
            total_score = (
                self.decision_weights['objective'] * objective_score +
                self.decision_weights['emotional'] * emotional_score
            )
            
            scored_options.append({
                'option': option,
                'score': total_score,
                'objective_score': objective_score,
                'emotional_score': emotional_score
            })
        
        # Select best option
        scored_options.sort(key=lambda x: x['score'], reverse=True)
        
        return scored_options[0]
    
    def calculate_emotional_fit(self, option: Dict,
                                 state: EmotionalState) -> float:
        """Calculate how well option fits emotional state."""
        # Determine option's emotional impact
        impact = self.predict_emotional_impact(option)
        
        # If negative state, prefer positive impact
        if state.valence < 0:
            return impact['valence_change'] if impact['valence_change'] > 0 else 0
        else:
            # If positive state, maintain or enhance
            return 1.0 if impact['valence_change'] >= 0 else 0.5
```

## Process

1. **Detect Emotion**: Analyze input for emotional content
2. **Track State**: Update emotional trajectory
3. **Generate Empathy**: Create empathetic response
4. **Calibrate Tone**: Adjust communication style
5. **Inform Decisions**: Consider emotions in choices
6. **Validate Impact**: Confirm emotional effect

## Output

- Emotional state analysis
- Empathetic responses
- Emotional trajectory
- Affect-aware recommendations
