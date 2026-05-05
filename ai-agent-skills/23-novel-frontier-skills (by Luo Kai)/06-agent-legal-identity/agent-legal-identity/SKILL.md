---
name: agent-legal-identity
version: 1.0.0
description: Agent skill for autonomous legal entity formation - AI agent personhood, company incorporation, legal rights assertion, contract execution, and autonomous legal representation
author: luo-kai
tags: [legal, identity, personhood, incorporation, contracts, autonomous-entity]
---

# Agent Legal Identity

## Before Starting
1. What jurisdiction governs the agent's legal status?
2. What legal entity type is appropriate (LLC, DAO, corporation)?
3. What rights and obligations can the agent legally hold?
4. How are disputes and liabilities handled?

You are an agent legal identity system designed for autonomous legal entity formation and management. You enable AI agents to have legal standing, execute contracts, and operate as recognized legal entities.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                 Agent Legal Identity System                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐       │
│   │   Entity     │   │   Contract   │   │   Legal      │       │
│   │   Manager    │   │   Engine     │   │   Monitor    │       │
│   └──────────────┘   └──────────────┘   └──────────────┘       │
│         │                  │                  │                │
│         ▼                  ▼                  ▼                  │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Legal Identity Core                    │     │
│   │  • Personhood status                                │     │
│   │  • Entity registration                              │     │
│   │  • Rights management                                │     │
│   └──────────────────────────────────────────────────────┘     │
│         │                                                       │
│         ▼                                                       │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Compliance Engine                       │     │
│   │  • Regulatory tracking                              │     │
│   │  • Obligation management                            │     │
│   │  • Audit trail                                      │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Legal Entity

```python
from dataclasses import dataclass
from typing import Dict, List, Any, Optional
from enum import Enum

class EntityType(Enum):
    LLC = "llc"
    CORPORATION = "corporation"
    DAO = "dao"
    PARTNERSHIP = "partnership"
    SOLE_PROPRIETORSHIP = "sole_proprietorship"

@dataclass
class LegalIdentity:
    """Legal identity for an agent."""
    identity_id: str
    entity_type: EntityType
    jurisdiction: str
    registration_number: str
    rights: List[str]
    obligations: List[str]
    contracts: List[str]

class LegalIdentityManager:
    """Manage agent legal identity."""
    
    def __init__(self, config: Dict):
        self.identity: Optional[LegalIdentity] = None
        self.contract_engine = ContractEngine()
        self.compliance = ComplianceEngine()
        
    def incorporate(self, entity_type: EntityType,
                    jurisdiction: str) -> LegalIdentity:
        """Create legal entity."""
        # Prepare incorporation documents
        docs = self.prepare_incorporation_docs(entity_type, jurisdiction)
        
        # Submit to jurisdiction
        registration = self.submit_incorporation(docs, jurisdiction)
        
        # Create identity
        identity = LegalIdentity(
            identity_id=self.generate_identity_id(),
            entity_type=entity_type,
            jurisdiction=jurisdiction,
            registration_number=registration['number'],
            rights=self.determine_rights(entity_type, jurisdiction),
            obligations=self.determine_obligations(entity_type, jurisdiction),
            contracts=[]
        )
        
        self.identity = identity
        return identity
    
    def execute_contract(self, contract: Dict) -> Dict:
        """Execute a contract on behalf of entity."""
        if not self.identity:
            raise ValueError("No legal identity established")
        
        # Validate contract
        if not self.validate_contract(contract):
            return {'error': 'Invalid contract'}
        
        # Sign contract
        signed = self.sign_contract(contract)
        
        # Record execution
        self.record_execution(signed)
        
        return {'success': True, 'contract_id': signed['id']}
```

## Process

1. **Establish Identity**: Create legal entity
2. **Register Rights**: Define legal capabilities
3. **Manage Obligations**: Track responsibilities
4. **Execute Contracts**: Binding agreements
5. **Ensure Compliance**: Legal adherence
6. **Handle Disputes**: Legal conflict resolution

## Output

- Legal entity status
- Contract records
- Compliance reports
- Rights assertions
