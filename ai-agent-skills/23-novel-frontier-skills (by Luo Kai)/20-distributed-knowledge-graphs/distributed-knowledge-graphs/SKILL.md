---
name: distributed-knowledge-graphs
version: 1.0.0
description: Agent skill for distributed knowledge graph management - decentralized knowledge storage, federated reasoning, knowledge synchronization, and consensus-based truth maintenance
author: luo-kai
tags: [knowledge-graph, distributed, federated, decentralized, consensus]
---

# Distributed Knowledge Graphs

## Before Starting
1. What is the knowledge domain?
2. What nodes participate in the network?
3. What consensus mechanism is used?
4. How are conflicts resolved?

You are a distributed knowledge graph system for AI agents. You manage decentralized knowledge storage, federated reasoning, knowledge synchronization, and consensus-based truth maintenance across multiple agent nodes.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                Distributed Knowledge Graph                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│      ┌──────────┐         ┌──────────┐         ┌──────────┐    │
│      │  Node A  │◄───────►│  Node B  │◄───────►│  Node C  │    │
│      │ Knowledge│         │ Knowledge│         │ Knowledge│    │
│      └────┬─────┘         └────┬─────┘         └────┬─────┘    │
│           │                    │                    │           │
│           └────────────────────┼────────────────────┘           │
│                                │                                │
│                                ▼                                │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Consensus Layer                         │     │
│   │  • Knowledge voting                                 │     │
│   │  • Conflict resolution                              │     │
│   │  • Truth maintenance                                │     │
│   └──────────────────────────────────────────────────────┘     │
│                                │                                │
│                                ▼                                │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Federated Reasoning                    │     │
│   │  • Distributed queries                              │     │
│   │  • Parallel inference                               │     │
│   │  • Knowledge aggregation                            │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Knowledge Node

```python
from dataclasses import dataclass, field
from typing import List, Dict, Set, Optional, Any, Tuple
from datetime import datetime
import hashlib
import json

@dataclass
class KnowledgeNode:
    """Node in distributed knowledge network."""
    node_id: str
    local_graph: 'KnowledgeGraph'
    neighbors: Set[str]
    sync_state: Dict[str, int]  # Vector clock
    reputation: float
    
    def __post_init__(self):
        self.local_graph = KnowledgeGraph()
        self.pending_updates: List[KnowledgeUpdate] = []

@dataclass
class Triple:
    """RDF-style triple."""
    subject: str
    predicate: str
    object: Any
    confidence: float = 1.0
    source: str = ""
    timestamp: datetime = field(default_factory=datetime.now)
    
    def hash(self) -> str:
        """Generate unique hash for triple."""
        content = f"{self.subject}|{self.predicate}|{self.object}"
        return hashlib.sha256(content.encode()).hexdigest()[:16]

@dataclass
class KnowledgeUpdate:
    """Update to knowledge graph."""
    update_id: str
    node_id: str
    triples_added: List[Triple]
    triples_removed: List[str]  # Triple hashes
    vector_clock: Dict[str, int]
    timestamp: datetime
    signature: Optional[str] = None

class KnowledgeGraph:
    """Local knowledge graph at a node."""
    
    def __init__(self):
        self.triples: Dict[str, Triple] = {}  # hash -> triple
        self.index_s: Dict[str, Set[str]] = {}  # subject -> triple hashes
        self.index_p: Dict[str, Set[str]] = {}  # predicate -> triple hashes
        self.index_o: Dict[str, Set[str]] = {}  # object -> triple hashes
        
    def add_triple(self, triple: Triple):
        """Add triple to graph."""
        h = triple.hash()
        
        if h in self.triples:
            # Merge confidence if exists
            existing = self.triples[h]
            existing.confidence = max(existing.confidence, triple.confidence)
            return
        
        self.triples[h] = triple
        
        # Update indices
        if triple.subject not in self.index_s:
            self.index_s[triple.subject] = set()
        self.index_s[triple.subject].add(h)
        
        if triple.predicate not in self.index_p:
            self.index_p[triple.predicate] = set()
        self.index_p[triple.predicate].add(h)
        
        obj_key = str(triple.object)
        if obj_key not in self.index_o:
            self.index_o[obj_key] = set()
        self.index_o[obj_key].add(h)
    
    def query(self, subject: str = None, predicate: str = None,
              obj: Any = None) -> List[Triple]:
        """Query triples by pattern."""
        candidate_hashes = None
        
        if subject:
            candidate_hashes = self.index_s.get(subject, set()).copy()
        if predicate:
            p_hashes = self.index_p.get(predicate, set())
            if candidate_hashes is None:
                candidate_hashes = p_hashes.copy()
            else:
                candidate_hashes &= p_hashes
        if obj is not None:
            o_hashes = self.index_o.get(str(obj), set())
            if candidate_hashes is None:
                candidate_hashes = o_hashes.copy()
            else:
                candidate_hashes &= o_hashes
        
        if candidate_hashes is None:
            return list(self.triples.values())
        
        return [self.triples[h] for h in candidate_hashes if h in self.triples]
```

## Distributed Synchronization

```python
class KnowledgeSynchronizer:
    """Synchronize knowledge across nodes."""
    
    def __init__(self, node: KnowledgeNode):
        self.node = node
        self.sync_interval = 60  # seconds
        
    def synchronize(self):
        """Synchronize with neighbors."""
        # Gather local updates since last sync
        updates = self.gather_local_updates()
        
        # Broadcast to neighbors
        for neighbor_id in self.node.neighbors:
            self.send_updates(neighbor_id, updates)
        
        # Receive updates from neighbors
        for neighbor_id in self.node.neighbors:
            received = self.receive_updates(neighbor_id)
            self.apply_received_updates(received)
    
    def gather_local_updates(self) -> KnowledgeUpdate:
        """Gather updates since last sync."""
        return KnowledgeUpdate(
            update_id=self.generate_update_id(),
            node_id=self.node.node_id,
            triples_added=list(self.node.pending_updates),
            triples_removed=[],
            vector_clock=self.node.sync_state.copy(),
            timestamp=datetime.now()
        )
    
    def apply_received_updates(self, update: KnowledgeUpdate):
        """Apply updates from another node."""
        # Check causality with vector clock
        if not self.is_causally_valid(update):
            return
        
        # Add new triples
        for triple in update.triples_added:
            # Check for conflicts
            conflicts = self.detect_conflicts(triple)
            
            if conflicts:
                resolved = self.resolve_conflicts(triple, conflicts)
                self.node.local_graph.add_triple(resolved)
            else:
                self.node.local_graph.add_triple(triple)
        
        # Remove deprecated triples
        for triple_hash in update.triples_removed:
            if triple_hash in self.node.local_graph.triples:
                del self.node.local_graph.triples[triple_hash]
        
        # Update vector clock
        self.update_vector_clock(update)
```

## Consensus Mechanism

```python
class KnowledgeConsensus:
    """Consensus mechanism for knowledge truth."""
    
    def __init__(self, node: KnowledgeNode):
        self.node = node
        self.voting_threshold = 0.6
        
    def propose_fact(self, triple: Triple) -> bool:
        """Propose a fact for consensus."""
        # Create proposal
        proposal = {
            'triple': triple,
            'proposer': self.node.node_id,
            'timestamp': datetime.now().isoformat()
        }
        
        # Broadcast proposal
        votes = self.collect_votes(proposal)
        
        # Check consensus
        if self.has_consensus(votes):
            # Commit to local graph
            self.node.local_graph.add_triple(triple)
            return True
        
        return False
    
    def collect_votes(self, proposal: Dict) -> Dict[str, bool]:
        """Collect votes from nodes."""
        votes = {self.node.node_id: self.evaluate_proposal(proposal)}
        
        for neighbor_id in self.node.neighbors:
            vote = self.request_vote(neighbor_id, proposal)
            votes[neighbor_id] = vote
        
        return votes
    
    def evaluate_proposal(self, proposal: Dict) -> bool:
        """Evaluate if proposal should be accepted."""
        triple = proposal['triple']
        
        # Check against local knowledge
        conflicts = self.check_local_conflicts(triple)
        if conflicts:
            # Weight by confidence
            if all(c.confidence > triple.confidence for c in conflicts):
                return False
        
        # Check source reputation
        source_rep = self.get_source_reputation(proposal['proposer'])
        if source_rep < 0.3:
            return False
        
        return True
    
    def has_consensus(self, votes: Dict[str, bool]) -> bool:
        """Check if votes reach consensus threshold."""
        total_weight = sum(self.get_node_weight(n) for n in votes)
        positive_weight = sum(
            self.get_node_weight(n) for n, v in votes.items() if v
        )
        
        return positive_weight / total_weight >= self.voting_threshold
```

## Conflict Resolution

```python
class ConflictResolver:
    """Resolve knowledge conflicts."""
    
    def __init__(self, strategy: str = "weighted_confidence"):
        self.strategy = strategy
        
    def resolve(self, triple: Triple, 
                conflicts: List[Triple]) -> Triple:
        """Resolve conflict between triples."""
        if self.strategy == "weighted_confidence":
            return self.weighted_confidence_resolve(triple, conflicts)
        elif self.strategy == "source_reputation":
            return self.source_reputation_resolve(triple, conflicts)
        elif self.strategy == "temporal":
            return self.temporal_resolve(triple, conflicts)
        elif self.strategy == "voting":
            return self.voting_resolve(triple, conflicts)
    
    def weighted_confidence_resolve(self, triple: Triple,
                                     conflicts: List[Triple]) -> Triple:
        """Resolve by weighted confidence."""
        all_triples = [triple] + conflicts
        
        # Calculate weighted average
        total_weight = sum(t.confidence for t in all_triples)
        
        # Return highest confidence
        return max(all_triples, key=lambda t: t.confidence)
    
    def detect_conflicts(self, new_triple: Triple,
                         graph: KnowledgeGraph) -> List[Triple]:
        """Detect conflicting triples."""
        conflicts = []
        
        # Find triples with same subject-predicate
        existing = graph.query(
            subject=new_triple.subject,
            predicate=new_triple.predicate
        )
        
        for t in existing:
            if t.object != new_triple.object:
                conflicts.append(t)
        
        return conflicts
```

## Federated Reasoning

```python
class FederatedReasoner:
    """Perform reasoning across distributed knowledge."""
    
    def __init__(self, node: KnowledgeNode):
        self.node = node
        self.query_planner = QueryPlanner()
        
    def federated_query(self, pattern: Tuple) -> List[Triple]:
        """Execute query across nodes."""
        # Plan distributed query
        plan = self.query_planner.plan(pattern, self.node.neighbors)
        
        # Execute local part
        local_results = self.node.local_graph.query(*pattern)
        
        # Execute remote parts
        remote_results = []
        for target_node, sub_pattern in plan:
            results = self.remote_query(target_node, sub_pattern)
            remote_results.extend(results)
        
        # Combine results
        combined = self.merge_results(local_results, remote_results)
        
        return combined
    
    def federated_inference(self, rule: Dict) -> List[Triple]:
        """Perform inference across nodes."""
        inferred = []
        
        # Match rule premises locally
        premise_matches = self.match_premises(rule['premises'])
        
        # Request remote premise matches
        for neighbor_id in self.node.neighbors:
            remote_matches = self.remote_match_premises(
                neighbor_id, rule['premises']
            )
            premise_matches.extend(remote_matches)
        
        # Derive conclusions
        for match in premise_matches:
            conclusion = self.derive_conclusion(rule, match)
            inferred.append(conclusion)
        
        return inferred
    
    def merge_results(self, local: List[Triple],
                      remote: List[Triple]) -> List[Triple]:
        """Merge results from multiple sources."""
        merged = {}
        
        for triple in local + remote:
            h = triple.hash()
            if h in merged:
                # Combine confidence
                merged[h].confidence = max(
                    merged[h].confidence, triple.confidence
                )
            else:
                merged[h] = triple
        
        return list(merged.values())
```

## Process

1. **Initialize Node**: Set up local knowledge graph
2. **Connect Network**: Establish connections to neighbors
3. **Sync Knowledge**: Exchange updates with nodes
4. **Resolve Conflicts**: Handle contradictory facts
5. **Reach Consensus**: Validate knowledge through voting
6. **Federated Reasoning**: Query and infer across nodes

## Output

- Distributed knowledge graph
- Consensus reports
- Conflict resolutions
- Query results
- Inference chains
