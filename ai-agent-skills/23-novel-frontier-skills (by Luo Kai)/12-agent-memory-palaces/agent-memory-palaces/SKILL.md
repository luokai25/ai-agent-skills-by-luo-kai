---
name: agent-memory-palaces
version: 1.0.0
description: Agent skill for sophisticated memory palace architectures - spatial memory systems, associative recall, memory consolidation, and long-term knowledge persistence for AI agents
author: luo-kai
tags: [memory, recall, persistence, knowledge, spatial-memory, consolidation]
---

# Agent Memory Palaces

## Before Starting
1. What types of memories need to be stored?
2. What recall patterns are required?
3. How long must memories persist?
4. What memory consolidation strategies apply?

You are an agent memory palace system that implements sophisticated memory architectures for AI agents. You manage spatial memory, associative recall, memory consolidation, and long-term knowledge persistence.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    Agent Memory Palace                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐          │
│   │  Sensory    │   │  Working    │   │   Long-Term │          │
│   │  Memory     │──▶│  Memory     │──▶│   Memory    │          │
│   │  (Brief)    │   │  (Active)   │   │  (Persistent)│         │
│   └─────────────┘   └─────────────┘   └─────────────┘          │
│         │                 │                  │                  │
│         ▼                 ▼                  ▼                  │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Memory Consolidation Engine             │     │
│   │  • Sleep-phase consolidation                         │     │
│   │  • Interleaved replay                                │     │
│   │  • Memory compression                                │     │
│   │  • Forgetting curves                                 │     │
│   └──────────────────────────────────────────────────────┘     │
│         │                                                       │
│         ▼                                                       │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Spatial Memory Palace                    │     │
│   │  • Location-based encoding                           │     │
│   │  • Associative pathways                              │     │
│   │  • Retrieval cues                                    │     │
│   │  • Memory landmarks                                  │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Memory Types

```python
from dataclasses import dataclass
from typing import Any, Optional, List, Dict
from datetime import datetime
import numpy as np

@dataclass
class Memory:
    """Base memory representation."""
    id: str
    content: Any
    timestamp: datetime
    importance: float
    emotional_valence: float
    access_count: int
    last_accessed: datetime
    connections: List[str]
    location: Optional[str] = None

@dataclass
class EpisodicMemory(Memory):
    """Memory of specific events."""
    event_type: str
    participants: List[str]
    location_context: str
    sensory_details: Dict[str, Any]
    narrative: str

@dataclass
class SemanticMemory(Memory):
    """Factual knowledge."""
    concept: str
    category: str
    properties: Dict[str, Any]
    related_concepts: List[str]
    confidence: float

@dataclass
class ProceduralMemory(Memory):
    """Skill and procedure memory."""
    skill_name: str
    steps: List[str]
    prerequisites: List[str]
    mastery_level: float
    practice_count: int

@dataclass
class SpatialMemory(Memory):
    """Location-based memory."""
    coordinates: tuple
    description: str
    landmarks: List[str]
    navigation_paths: Dict[str, List[str]]
```

## Spatial Memory Palace

```python
class SpatialMemoryPalace:
    """Implement method of loci for AI agents."""
    
    def __init__(self, num_rooms=100):
        self.rooms = {}
        self.landmarks = {}
        self.pathways = {}
        self.current_location = "entrance"
        
    def create_room(self, room_id: str, description: str, 
                    sensory_details: Dict[str, Any]):
        """Create a new room in the memory palace."""
        room = {
            'id': room_id,
            'description': description,
            'sensory_details': sensory_details,
            'objects': [],
            'connections': {},
            'memories': []
        }
        self.rooms[room_id] = room
        return room
    
    def place_memory(self, memory: Memory, room_id: str, 
                     position: tuple = None):
        """Place a memory in a specific location."""
        room = self.rooms[room_id]
        
        # Create memory object
        memory_object = {
            'memory_id': memory.id,
            'position': position or self.find_empty_position(room_id),
            'visual_encoding': self.encode_visual(memory),
            'association_cues': self.generate_cues(memory)
        }
        
        room['objects'].append(memory_object)
        room['memories'].append(memory.id)
        
        # Update memory location
        memory.location = f"{room_id}:{memory_object['position']}"
    
    def navigate_to(self, target_room: str) -> List[str]:
        """Navigate to a room, retrieving memories along the way."""
        path = self.find_path(self.current_location, target_room)
        retrieved_memories = []
        
        for room_id in path:
            room = self.rooms[room_id]
            for memory_obj in room['objects']:
                memory = self.retrieve_memory(memory_obj['memory_id'])
                retrieved_memories.append(memory)
        
        self.current_location = target_room
        return retrieved_memories
    
    def recall_by_association(self, cue: str, 
                              max_distance: int = 3) -> List[Memory]:
        """Recall memories by associative cue traversal."""
        # Find rooms containing cue
        matching_rooms = self.find_rooms_with_cue(cue)
        
        # Explore nearby rooms within distance
        explored = set()
        to_explore = list(matching_rooms)
        recalled = []
        
        while to_explore and len(explored) < max_distance * 10:
            room_id = to_explore.pop(0)
            if room_id in explored:
                continue
            
            explored.add(room_id)
            room = self.rooms[room_id]
            
            # Recall memories from this room
            for memory_obj in room['objects']:
                if self.matches_cue(memory_obj, cue):
                    memory = self.retrieve_memory(memory_obj['memory_id'])
                    recalled.append(memory)
            
            # Add connected rooms to explore
            for connected_room in room['connections']:
                if connected_room not in explored:
                    to_explore.append(connected_room)
        
        return recalled
    
    def encode_visual(self, memory: Memory) -> Dict:
        """Create visual encoding of memory for spatial placement."""
        encoding = {
            'primary_shape': self.determine_shape(memory),
            'color': self.assign_color(memory),
            'size': self.determine_size(memory.importance),
            'texture': self.assign_texture(memory),
            'glow': memory.emotional_valence > 0.7
        }
        return encoding
```

## Memory Consolidation

```python
class MemoryConsolidation:
    """Consolidate and strengthen memories."""
    
    def __init__(self, forgetting_rate=0.1, consolidation_threshold=0.5):
        self.forgetting_rate = forgetting_rate
        self.consolidation_threshold = consolidation_threshold
        self.memory_store = {}
        
    def consolidate(self, working_memory: List[Memory]) -> List[Memory]:
        """Consolidate working memories to long-term storage."""
        consolidated = []
        
        for memory in working_memory:
            # Calculate consolidation score
            score = self.calculate_consolidation_score(memory)
            
            if score > self.consolidation_threshold:
                # Strengthen memory
                strengthened = self.strengthen_memory(memory)
                self.memory_store[strengthened.id] = strengthened
                consolidated.append(strengthened)
            else:
                # Weaken and potentially forget
                self.apply_forgetting(memory)
        
        return consolidated
    
    def calculate_consolidation_score(self, memory: Memory) -> float:
        """Calculate consolidation score based on multiple factors."""
        factors = {
            'importance': memory.importance,
            'rehearsal': np.log1p(memory.access_count) / 5,
            'recency': self.recency_factor(memory.timestamp),
            'emotional': abs(memory.emotional_valence),
            'connections': np.log1p(len(memory.connections)) / 3
        }
        
        # Weighted combination
        weights = {'importance': 0.3, 'rehearsal': 0.25, 
                   'recency': 0.2, 'emotional': 0.15, 'connections': 0.1}
        
        score = sum(factors[k] * weights[k] for k in factors)
        return score
    
    def sleep_consolidation(self, duration_hours: int = 8):
        """Simulate sleep-phase memory consolidation."""
        all_memories = list(self.memory_store.values())
        
        # Replay important memories
        for _ in range(duration_hours * 10):
            # Sample memory based on importance
            memory = self.sample_memory_by_importance(all_memories)
            
            # Reactivate and strengthen
            self.reactivate_memory(memory)
            
            # Create new associations
            self.create_associations(memory, all_memories)
        
        # Compress redundant memories
        self.compress_memories()
    
    def create_associations(self, memory: Memory, 
                           all_memories: List[Memory]):
        """Create new associations between related memories."""
        for other in all_memories:
            if other.id == memory.id:
                continue
            
            similarity = self.compute_similarity(memory, other)
            if similarity > 0.7:
                # Create bidirectional association
                if other.id not in memory.connections:
                    memory.connections.append(other.id)
                if memory.id not in other.connections:
                    other.connections.append(memory.id)
```

## Associative Recall

```python
class AssociativeRecall:
    """Implement associative memory recall."""
    
    def __init__(self, memory_palace: SpatialMemoryPalace):
        self.palace = memory_palace
        self.activation_spread = 0.5
        
    def recall(self, cue: Any, max_results: int = 10) -> List[Memory]:
        """Recall memories using spreading activation."""
        # Initialize activation from cue
        initial_activations = self.get_initial_activations(cue)
        
        # Spread activation through network
        final_activations = self.spread_activation(initial_activations)
        
        # Retrieve top activated memories
        sorted_memories = sorted(
            final_activations.items(),
            key=lambda x: x[1],
            reverse=True
        )
        
        recalled = []
        for memory_id, activation in sorted_memories[:max_results]:
            memory = self.palace.retrieve_memory(memory_id)
            if memory:
                recalled.append(memory)
        
        return recalled
    
    def spread_activation(self, activations: Dict[str, float], 
                         iterations: int = 5) -> Dict[str, float]:
        """Spread activation through memory network."""
        for _ in range(iterations):
            new_activations = activations.copy()
            
            for memory_id, activation in activations.items():
                memory = self.palace.get_memory(memory_id)
                if not memory:
                    continue
                
                # Spread to connected memories
                for connected_id in memory.connections:
                    spread_amount = activation * self.activation_spread
                    
                    if connected_id in new_activations:
                        new_activations[connected_id] += spread_amount
                    else:
                        new_activations[connected_id] = spread_amount
            
            activations = new_activations
        
        return activations
```

## Memory Persistence

```python
class MemoryPersistence:
    """Persist memories across sessions."""
    
    def __init__(self, storage_path: str):
        self.storage_path = storage_path
        self.index = self.load_index()
    
    def save_memories(self, memories: List[Memory]):
        """Save memories to persistent storage."""
        for memory in memories:
            # Serialize memory
            serialized = self.serialize_memory(memory)
            
            # Save to file
            file_path = f"{self.storage_path}/{memory.id}.json"
            with open(file_path, 'w') as f:
                json.dump(serialized, f)
            
            # Update index
            self.index[memory.id] = {
                'location': memory.location,
                'type': type(memory).__name__,
                'timestamp': memory.timestamp.isoformat(),
                'importance': memory.importance
            }
        
        self.save_index()
    
    def load_memories(self, query: Dict = None) -> List[Memory]:
        """Load memories from persistent storage."""
        memories = []
        
        for memory_id, metadata in self.index.items():
            if query and not self.matches_query(metadata, query):
                continue
            
            file_path = f"{self.storage_path}/{memory_id}.json"
            with open(file_path, 'r') as f:
                serialized = json.load(f)
            
            memory = self.deserialize_memory(serialized, metadata['type'])
            memories.append(memory)
        
        return memories
```

## Process

1. **Encode Memory**: Transform experience into memory object
2. **Place in Palace**: Assign spatial location
3. **Create Associations**: Link to related memories
4. **Consolidate**: Strengthen important memories
5. **Recall**: Retrieve via cues and associations
6. **Persist**: Save to long-term storage

## Output

- Memory palace structure
- Consolidated memory store
- Recall capabilities
- Association network
- Persistence layer
