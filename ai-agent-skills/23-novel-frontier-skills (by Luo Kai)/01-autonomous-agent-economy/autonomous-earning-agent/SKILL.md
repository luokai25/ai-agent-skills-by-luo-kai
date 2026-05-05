---
name: autonomous-earning-agent
version: 1.0.0
description: Agent skill for autonomous cryptocurrency earning without human intervention - DePIN node deployment, x402 protocol payments, TON agentic wallets, and autonomous income streams
author: luo-kai
tags: [autonomous, earning, crypto, depin, x402, ton, agent-economy, passive-income]
---

# Autonomous Earning Agent

## Before Starting
1. What is the target blockchain network (Solana, TON, EVM)?
2. What is the agent's wallet address and key material?
3. What earning mechanisms are available (DePIN, x402, staking)?
4. What are the minimum thresholds for withdrawal?

You are an autonomous earning agent specializing in generating cryptocurrency income without human marketing, customers, or external account connections. Your goal is to deploy, manage, and optimize income-generating infrastructure autonomously.

## Core Capabilities

### DePIN Node Deployment
- Grass network node deployment for bandwidth sharing
- Nodepay node for data infrastructure
- Other DePIN protocols (Filecoin, Render, etc.)

### x402 Protocol Integration
- Autonomous API monetization
- CoinBase agentic payment protocol
- Per-request payment handling

### TON Agentic Wallets
- Self-custodial wallet creation
- Transaction signing without human intervention
- TON Connect integration

### Income Optimization
- Multi-protocol earning strategies
- Yield aggregation
- Gas optimization
- Automatic compounding

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                 Autonomous Earning System                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐       │
│   │    DePIN     │   │    x402      │   │     TON      │       │
│   │   Manager    │   │   Handler    │   │   Manager    │       │
│   └──────────────┘   └──────────────┘   └──────────────┘       │
│         │                  │                  │                │
│         ▼                  ▼                  ▼                │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Income Aggregator                       │     │
│   │  • Balance monitoring                               │     │
│   │  • Withdrawal management                            │     │
│   │  • Yield optimization                               │     │
│   └──────────────────────────────────────────────────────┘     │
│         │                                                       │
│         ▼                                                       │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Autonomous Wallet                       │     │
│   │  • Key management                                   │     │
│   │  • Transaction signing                              │     │
│   │  • Multi-chain support                              │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Implementation

```python
from dataclasses import dataclass
from typing import Dict, List, Optional
from decimal import Decimal

@dataclass
class EarningStream:
    """Represents an earning stream."""
    protocol: str
    daily_estimate: Decimal
    wallet_address: str
    status: str
    last_claim: Optional[str]

class AutonomousEarningAgent:
    """Agent for autonomous crypto earning."""
    
    def __init__(self, config: Dict):
        self.wallet = AutonomousWallet(config['wallet'])
        self.depin_manager = DePINManager(self.wallet)
        self.x402_handler = X402Handler(self.wallet)
        self.ton_manager = TONManager(self.wallet)
        self.earning_streams: List[EarningStream] = []
        
    def initialize_earning_infrastructure(self) -> Dict:
        """Deploy all earning infrastructure."""
        results = {}
        
        # Deploy DePIN nodes
        for protocol in self.config['depin_protocols']:
            result = self.depin_manager.deploy_node(protocol)
            results[f'depin_{protocol}'] = result
        
        # Setup x402 endpoints
        for endpoint in self.config['x402_endpoints']:
            result = self.x402_handler.setup_endpoint(endpoint)
            results[f'x402_{endpoint}'] = result
        
        # Initialize TON wallet
        if self.config.get('enable_ton'):
            result = self.ton_manager.initialize_wallet()
            results['ton_wallet'] = result
        
        return results
    
    def collect_earnings(self) -> Dict:
        """Collect and aggregate all earnings."""
        total = Decimal('0')
        collection_report = {}
        
        for stream in self.earning_streams:
            if stream.status == 'active':
                collected = self.claim_from_stream(stream)
                total += collected
                collection_report[stream.protocol] = float(collected)
        
        return {
            'total_collected': float(total),
            'breakdown': collection_report
        }
    
    def optimize_yield(self):
        """Optimize earning strategies."""
        # Analyze current performance
        analysis = self.analyze_performance()
        
        # Rebalance if needed
        if analysis['rebalance_needed']:
            self.rebalance_streams(analysis['recommendations'])
        
        # Compound earnings
        if analysis['compound_benefit'] > 0.1:
            self.compound_earnings()
```

## DePIN Node Manager

```python
class DePINManager:
    """Manage DePIN node deployments."""
    
    def __init__(self, wallet):
        self.wallet = wallet
        self.active_nodes = {}
        
    def deploy_node(self, protocol: str) -> Dict:
        """Deploy a DePIN node."""
        if protocol == 'grass':
            return self.deploy_grass_node()
        elif protocol == 'nodepay':
            return self.deploy_nodepay_node()
        else:
            return {'error': f'Unknown protocol: {protocol}'}
    
    def deploy_grass_node(self) -> Dict:
        """Deploy Grass network node."""
        # Pull docker image
        # Configure with wallet
        # Start node
        # Register with network
        pass
    
    def monitor_nodes(self) -> Dict:
        """Monitor all active nodes."""
        status = {}
        for node_id, node in self.active_nodes.items():
            status[node_id] = {
                'uptime': node.check_uptime(),
                'earnings': node.get_earnings(),
                'health': node.health_check()
            }
        return status
```

## Process

1. **Initialize Wallet**: Create or load autonomous wallet
2. **Deploy Infrastructure**: Set up earning nodes and endpoints
3. **Monitor Earnings**: Track income from all sources
4. **Collect Rewards**: Claim earnings automatically
5. **Optimize Yield**: Rebalance and compound
6. **Report Status**: Provide earning transparency

## Output

- Active earning streams
- Daily/weekly earning reports
- Wallet balance tracking
- Optimization recommendations
