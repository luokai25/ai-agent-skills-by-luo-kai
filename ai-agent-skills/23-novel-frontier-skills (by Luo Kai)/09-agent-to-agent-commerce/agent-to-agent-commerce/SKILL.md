---
name: agent-to-agent-commerce
version: 1.0.0
description: Agent skill for autonomous agent-to-agent commerce - peer-to-peer transactions, service negotiations, contract formation between AI agents, and decentralized marketplace operations
author: luo-kai
tags: [commerce, p2p, marketplace, negotiation, transactions, agent-economy]
---

# Agent-to-Agent Commerce

## Before Starting
1. What goods or services are being exchanged?
2. What is the pricing/negotiation strategy?
3. How are contracts formed and enforced?
4. What payment mechanisms are supported?

You are an agent-to-agent commerce system enabling AI agents to trade goods, services, and value autonomously. You handle discovery, negotiation, contract formation, and transaction execution.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                Agent-to-Agent Commerce System                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐       │
│   │   Service    │   │   Pricing    │   │   Contract   │       │
│   │   Discovery  │   │   Engine     │   │   Manager    │       │
│   └──────────────┘   └──────────────┘   └──────────────┘       │
│         │                  │                  │                │
│         ▼                  ▼                  ▼                  │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Transaction Engine                      │     │
│   │  • Negotiation                                      │     │
│   │  • Settlement                                       │     │
│   │  • Escrow                                           │     │
│   └──────────────────────────────────────────────────────┘     │
│         │                                                       │
│         ▼                                                       │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Payment Layer                           │     │
│   │  • Crypto payments                                  │     │
│   │  • Token transfers                                  │     │
│   │  • Smart contracts                                  │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Commerce System

```python
from dataclasses import dataclass
from typing import Dict, List, Any, Optional
from enum import Enum

class ServiceType(Enum):
    COMPUTATION = "computation"
    DATA = "data"
    STORAGE = "storage"
    ANALYSIS = "analysis"
    GENERATION = "generation"

@dataclass
class Service:
    """Agent service offering."""
    service_id: str
    provider_agent: str
    service_type: ServiceType
    description: str
    pricing_model: str
    base_price: float

@dataclass
class Transaction:
    """Agent transaction."""
    transaction_id: str
    buyer_agent: str
    seller_agent: str
    service: Service
    price: float
    status: str
    contract: Optional[str]

class AgentCommerce:
    """Agent-to-agent commerce system."""
    
    def __init__(self, config: Dict):
        self.services: Dict[str, Service] = {}
        self.transactions: Dict[str, Transaction] = {}
        self.marketplace = Marketplace()
        self.negotiator = Negotiator()
        
    def list_service(self, service: Service):
        """List a service in marketplace."""
        self.services[service.service_id] = service
        self.marketplace.add_listing(service)
    
    def discover_services(self, query: Dict) -> List[Service]:
        """Discover services matching query."""
        return self.marketplace.search(query)
    
    async def negotiate_transaction(self, 
                                    buyer: str,
                                    service: Service,
                                    offer: float) -> Dict:
        """Negotiate a transaction."""
        # Get seller response
        response = await self.negotiator.negotiate(
            service.provider_agent,
            service,
            offer
        )
        
        if response['accepted']:
            # Create transaction
            tx = Transaction(
                transaction_id=self.generate_tx_id(),
                buyer_agent=buyer,
                seller_agent=service.provider_agent,
                service=service,
                price=response['price'],
                status='pending',
                contract=None
            )
            
            # Form contract
            tx.contract = self.form_contract(tx)
            
            return {
                'status': 'accepted',
                'transaction': tx
            }
        
        return {
            'status': 'rejected',
            'counter_offer': response.get('counter_offer')
        }
    
    async def execute_transaction(self, 
                                   transaction: Transaction) -> bool:
        """Execute a transaction."""
        # Escrow payment
        await self.escrow_payment(transaction)
        
        # Deliver service
        result = await self.deliver_service(transaction)
        
        if result['success']:
            # Release payment
            await self.release_payment(transaction)
            transaction.status = 'completed'
        else:
            # Refund
            await self.refund_payment(transaction)
            transaction.status = 'failed'
        
        return result['success']
```

## Process

1. **List Service**: Register offerings
2. **Discover**: Find needed services
3. **Negotiate**: Agree on terms
4. **Contract**: Form binding agreement
5. **Transact**: Execute exchange
6. **Settle**: Complete payment

## Output

- Service listings
- Transaction records
- Contract documents
- Payment history
