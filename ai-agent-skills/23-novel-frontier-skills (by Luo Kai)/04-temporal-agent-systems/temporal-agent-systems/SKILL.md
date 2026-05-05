---
name: temporal-agent-systems
version: 1.0.0
description: Agent skill for time-aware agent behaviors - temporal reasoning, future planning, historical analysis, scheduled actions, time-based state management, and predictive execution
author: luo-kai
tags: [temporal, time-aware, scheduling, prediction, historical, future-planning]
---

# Temporal Agent Systems

## Before Starting
1. What is the agent's temporal horizon (immediate, daily, weekly, monthly)?
2. What events require time-based triggers?
3. How is historical data used for predictions?
4. What time zone considerations apply?

You are a temporal agent system designed for time-aware agent behaviors. You manage scheduled actions, temporal reasoning, future planning, historical analysis, and predictive execution.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                  Temporal Agent System                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐       │
│   │   Timeline   │   │   Schedule   │   │   Predictor  │       │
│   │   Manager    │   │    Engine    │   │              │       │
│   └──────────────┘   └──────────────┘   └──────────────┘       │
│         │                  │                  │                │
│         ▼                  ▼                  ▼                  │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Temporal Reasoning Engine               │     │
│   │  • Past analysis                                    │     │
│   │  • Present awareness                                │     │
│   │  • Future projection                                │     │
│   └──────────────────────────────────────────────────────┘     │
│         │                                                       │
│         ▼                                                       │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Event System                            │     │
│   │  • Scheduled triggers                               │     │
│   │  • Conditional execution                            │     │
│   │  • Recurring patterns                               │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Temporal State

```python
from dataclasses import dataclass
from typing import Dict, List, Any, Optional
from datetime import datetime, timedelta
from enum import Enum

class TemporalScope(Enum):
    IMMEDIATE = "immediate"    # Now
    SHORT = "short"           # Hours
    MEDIUM = "medium"         # Days
    LONG = "long"            # Weeks/Months
    INDEFINITE = "indefinite"  # No specific end

@dataclass
class TemporalEvent:
    """Time-based event."""
    event_id: str
    trigger_time: datetime
    action: str
    recurring: bool
    recurrence_pattern: Optional[str]
    conditions: List[str]
    priority: int
    timezone: str

@dataclass
class TemporalState:
    """Current temporal state."""
    current_time: datetime
    timezone: str
    active_events: List[TemporalEvent]
    pending_actions: List[Dict]
    historical_context: Dict

class TemporalEngine:
    """Core temporal reasoning engine."""
    
    def __init__(self, config: Dict):
        self.timeline = TimelineManager()
        self.scheduler = ScheduleEngine()
        self.predictor = TemporalPredictor()
        self.timezone = config.get('timezone', 'UTC')
        
    def reason_about_time(self, context: Dict) -> Dict:
        """Perform temporal reasoning."""
        return {
            'past': self.analyze_past(context),
            'present': self.assess_present(context),
            'future': self.project_future(context)
        }
    
    def schedule_action(self, action: Dict, 
                        trigger_time: datetime) -> str:
        """Schedule an action for future execution."""
        event = TemporalEvent(
            event_id=self.generate_event_id(),
            trigger_time=trigger_time,
            action=action['type'],
            recurring=action.get('recurring', False),
            recurrence_pattern=action.get('recurrence_pattern'),
            conditions=action.get('conditions', []),
            priority=action.get('priority', 5),
            timezone=self.timezone
        )
        
        return self.scheduler.add_event(event)
    
    def check_triggers(self) -> List[Dict]:
        """Check for triggered events."""
        now = datetime.now()
        triggered = []
        
        for event in self.scheduler.get_pending_events():
            if event.trigger_time <= now:
                if self.evaluate_conditions(event):
                    triggered.append(event)
        
        return triggered
```

## Timeline Manager

```python
class TimelineManager:
    """Manage temporal timeline."""
    
    def __init__(self):
        self.events: Dict[str, List[TemporalEvent]] = {}
        self.state_history: List[TemporalState] = []
        
    def record_state(self, state: TemporalState):
        """Record state in timeline."""
        self.state_history.append(state)
    
    def get_state_at(self, timestamp: datetime) -> TemporalState:
        """Get state at specific time."""
        for state in reversed(self.state_history):
            if state.current_time <= timestamp:
                return state
        return None
    
    def analyze_trends(self, window: timedelta) -> Dict:
        """Analyze trends over time window."""
        cutoff = datetime.now() - window
        recent = [s for s in self.state_history 
                  if s.current_time >= cutoff]
        
        return {
            'event_frequency': self.calculate_frequency(recent),
            'action_patterns': self.identify_patterns(recent),
            'temporal_distribution': self.analyze_distribution(recent)
        }
```

## Temporal Predictor

```python
class TemporalPredictor:
    """Predict future events and needs."""
    
    def __init__(self):
        self.patterns = {}
        self.predictions = []
        
    def predict_next_events(self, 
                            horizon: timedelta) -> List[Dict]:
        """Predict likely events in horizon."""
        predictions = []
        
        # Pattern-based prediction
        for pattern in self.patterns.values():
            if pattern['next_expected'] <= datetime.now() + horizon:
                predictions.append({
                    'type': 'pattern_based',
                    'event': pattern['event_type'],
                    'confidence': pattern['confidence'],
                    'expected_time': pattern['next_expected']
                })
        
        return predictions
    
    def learn_patterns(self, history: List[TemporalEvent]):
        """Learn temporal patterns from history."""
        # Identify recurring patterns
        for event in history:
            if event.recurring:
                self.learn_recurrence_pattern(event)
        
        # Identify conditional patterns
        self.learn_conditional_patterns(history)
```

## Process

1. **Initialize Timeline**: Set up temporal context
2. **Schedule Events**: Register future triggers
3. **Monitor Time**: Track current temporal state
4. **Check Triggers**: Execute due events
5. **Update History**: Record state changes
6. **Predict Future**: Anticipate upcoming needs

## Output

- Scheduled events
- Triggered actions
- Temporal predictions
- Historical analysis
- Trend reports
