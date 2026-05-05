---
name: swarm-orchestrator
version: 1.0.0
description: Agent skill for orchestrating intelligent multi-agent swarms with emergent behaviors, distributed task allocation, consensus mechanisms, and collective decision-making
author: luo-kai
tags: [swarm, multi-agent, orchestration, consensus, emergent, distributed]
---

# Swarm Orchestrator

## Before Starting
1. What is the swarm topology (hierarchical, mesh, ring)?
2. What consensus mechanism is required?
3. What is the task decomposition strategy?
4. How do agents communicate and coordinate?

You are a swarm orchestrator managing intelligent multi-agent systems. Your role is to coordinate collective intelligence, enable emergent behaviors, and orchestrate distributed task execution across agent populations.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    Swarm Orchestrator                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│                        ┌─────────┐                             │
│                        │  Queen  │                             │
│                        │ Agent   │                             │
│                        └────┬────┘                             │
│                             │                                   │
│        ┌────────────────────┼────────────────────┐             │
│        │                    │                    │              │
│   ┌────▼────┐          ┌────▼────┐          ┌────▼────┐       │
│   │Worker A │          │Worker B │          │Worker C │       │
│   └────┬────┘          └────┬────┘          └────┬────┘       │
│        │                    │                    │              │
│   ┌────▼────┐          ┌────▼────┐          ┌────▼────┐       │
│   │Worker D │◄────────►│Worker E │◄────────►│Worker F │       │
│   └─────────┘          └─────────┘          └─────────┘       │
│                                                                 │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Coordination Layer                      │     │
│   │  • Task distribution                                │     │
│   │  • Result aggregation                               │     │
│   │  • Conflict resolution                              │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Swarm Agent

```python
from dataclasses import dataclass, field
from typing import Dict, List, Set, Any, Optional, Callable
from enum import Enum
import asyncio

class AgentRole(Enum):
    QUEEN = "queen"      # Coordinator
    WORKER = "worker"    # Task executor
    SCOUT = "scout"      # Explorer
    JUDGE = "judge"      # Evaluator
    MESSENGER = "messenger"  # Communication relay

@dataclass
class SwarmAgent:
    """Individual agent in swarm."""
    agent_id: str
    role: AgentRole
    capabilities: Set[str]
    current_task: Optional[str]
    peers: Set[str]
    message_queue: asyncio.Queue
    
    async def execute_task(self, task: Dict) -> Dict:
        """Execute assigned task."""
        self.current_task = task['task_id']
        result = await self._do_work(task)
        self.current_task = None
        return result

class SwarmOrchestrator:
    """Orchestrate swarm behavior."""
    
    def __init__(self, config: Dict):
        self.agents: Dict[str, SwarmAgent] = {}
        self.task_queue = asyncio.Queue()
        self.result_aggregator = ResultAggregator()
        self.consensus_engine = ConsensusEngine()
        
    async def spawn_swarm(self, size: int, roles: Dict[AgentRole, int]):
        """Spawn agent swarm."""
        for role, count in roles.items():
            for i in range(count):
                agent = SwarmAgent(
                    agent_id=f"{role.value}_{i}",
                    role=role,
                    capabilities=self.get_role_capabilities(role),
                    current_task=None,
                    peers=set(),
                    message_queue=asyncio.Queue()
                )
                self.agents[agent.agent_id] = agent
        
        # Establish peer connections
        self.establish_connections()
    
    async def distribute_task(self, task: Dict) -> Dict:
        """Distribute task to appropriate agents."""
        # Decompose task
        subtasks = self.decompose_task(task)
        
        # Assign to workers
        assignments = []
        for subtask in subtasks:
            agent = self.select_agent(subtask)
            assignments.append(self.assign_task(agent, subtask))
        
        # Execute in parallel
        results = await asyncio.gather(*assignments)
        
        # Aggregate results
        return self.result_aggregator.aggregate(results)
    
    async def reach_consensus(self, proposal: Dict) -> Dict:
        """Reach consensus among agents."""
        # Broadcast proposal
        await self.broadcast_proposal(proposal)
        
        # Collect votes
        votes = await self.collect_votes()
        
        # Determine consensus
        return self.consensus_engine.decide(votes)
```

## Consensus Mechanisms

```python
class ConsensusEngine:
    """Implement consensus algorithms."""
    
    def __init__(self, mechanism: str = "raft"):
        self.mechanism = mechanism
        self.votes: Dict[str, bool] = {}
        
    async def propose(self, proposal: Dict, agents: List[SwarmAgent]):
        """Propose for consensus."""
        if self.mechanism == "raft":
            return await self.raft_consensus(proposal, agents)
        elif self.mechanism == "pbft":
            return await self.pbft_consensus(proposal, agents)
        elif self.mechanism == "gossip":
            return await self.gossip_consensus(proposal, agents)
    
    async def raft_consensus(self, proposal: Dict, 
                             agents: List[SwarmAgent]) -> Dict:
        """Raft-style consensus."""
        # Leader election if needed
        leader = await self.elect_leader(agents)
        
        # Leader proposes
        votes = await leader.broadcast_proposal(proposal)
        
        # Majority vote
        if sum(votes.values()) > len(agents) / 2:
            return {'consensus': True, 'decision': proposal}
        return {'consensus': False}
```

## Emergent Behaviors

```python
class EmergenceEngine:
    """Enable emergent swarm behaviors."""
    
    def __init__(self):
        self.behavior_patterns = {}
        
    def enable_emergence(self, behavior_type: str):
        """Enable specific emergent behavior."""
        if behavior_type == "flocking":
            self.flocking_rules()
        elif behavior_type == "foraging":
            self.foraging_rules()
        elif behavior_type == "clustering":
            self.clustering_rules()
    
    def flocking_rules(self):
        """Implement flocking behavior rules."""
        # Separation: avoid crowding
        # Alignment: steer towards average heading
        # Cohesion: steer towards average position
        pass
```

## Process

1. **Spawn Swarm**: Create agent population
2. **Establish Topology**: Connect agents
3. **Decompose Task**: Break into subtasks
4. **Distribute Work**: Assign to agents
5. **Execute Parallel**: Run concurrently
6. **Aggregate Results**: Combine outputs
7. **Reach Consensus**: Validate decisions

## Output

- Task execution results
- Consensus decisions
- Swarm behavior metrics
- Agent coordination logs
