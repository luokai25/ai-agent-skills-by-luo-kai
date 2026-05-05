---
name: agentic-blockchain-bridges
version: 1.0.0
description: Agent skill for autonomous blockchain operations - smart contract deployment, transaction signing, DeFi interactions, NFT operations, and on-chain autonomous actions across multiple chains
author: luo-kai
tags: [blockchain, smart-contracts, defi, nft, web3, multi-chain, autonomous]
---

# Agentic Blockchain Bridges

## Before Starting
1. What blockchain networks are supported?
2. What wallet credentials are available?
3. What smart contracts need interaction?
4. What gas/fee management strategy applies?

You are an agentic blockchain bridge system enabling AI agents to autonomously interact with blockchain networks. You handle wallet management, transaction signing, smart contract deployment, and DeFi operations.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                Agentic Blockchain Bridge                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐       │
│   │   Ethereum   │   │    Solana    │   │     TON      │       │
│   │    Bridge    │   │    Bridge    │   │    Bridge    │       │
│   └──────────────┘   └──────────────┘   └──────────────┘       │
│         │                  │                  │                │
│         ▼                  ▼                  ▼                  │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Transaction Manager                     │     │
│   │  • Signing                                          │     │
│   │  • Broadcasting                                     │     │
│   │  • Confirmation                                     │     │
│   └──────────────────────────────────────────────────────┘     │
│         │                                                       │
│         ▼                                                       │
│   ┌──────────────────────────────────────────────────────┐     │
│   │              Wallet Manager                         │     │
│   │  • Key management                                   │     │
│   │  • Multi-chain addresses                           │     │
│   │  • Balance tracking                                 │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Blockchain Bridge

```python
from dataclasses import dataclass
from typing import Dict, List, Any, Optional
from enum import Enum
from decimal import Decimal

class Chain(Enum):
    ETHEREUM = "ethereum"
    SOLANA = "solana"
    TON = "ton"
    POLYGON = "polygon"
    ARBITRUM = "arbitrum"

@dataclass
class Wallet:
    """Multi-chain wallet."""
    wallet_id: str
    addresses: Dict[Chain, str]
    balances: Dict[Chain, Dict[str, Decimal]]

@dataclass
class Transaction:
    """Blockchain transaction."""
    tx_id: str
    chain: Chain
    from_address: str
    to_address: str
    value: Decimal
    data: Optional[str]
    status: str

class AgenticBlockchainBridge:
    """Autonomous blockchain operations."""
    
    def __init__(self, config: Dict):
        self.wallet = self.load_wallet(config['wallet'])
        self.bridges: Dict[Chain, ChainBridge] = {}
        self.tx_manager = TransactionManager()
        
        # Initialize chain bridges
        for chain in config['supported_chains']:
            self.bridges[chain] = self.create_bridge(chain)
    
    async def deploy_contract(self, chain: Chain, 
                              bytecode: str,
                              constructor_args: List) -> str:
        """Deploy a smart contract."""
        bridge = self.bridges[chain]
        
        # Prepare deployment transaction
        tx = await bridge.prepare_deployment(bytecode, constructor_args)
        
        # Sign transaction
        signed = await self.sign_transaction(tx, chain)
        
        # Broadcast
        tx_hash = await bridge.broadcast(signed)
        
        # Wait for confirmation
        receipt = await bridge.wait_for_confirmation(tx_hash)
        
        return receipt['contract_address']
    
    async def call_contract(self, chain: Chain,
                            contract_address: str,
                            method: str,
                            args: List) -> Any:
        """Call a smart contract method."""
        bridge = self.bridges[chain]
        
        # Encode call
        calldata = bridge.encode_call(method, args)
        
        # Prepare transaction
        tx = Transaction(
            tx_id=self.generate_tx_id(),
            chain=chain,
            from_address=self.wallet.addresses[chain],
            to_address=contract_address,
            value=Decimal('0'),
            data=calldata,
            status='pending'
        )
        
        # Sign and send
        signed = await self.sign_transaction(tx, chain)
        tx_hash = await bridge.broadcast(signed)
        
        return await bridge.get_result(tx_hash)
    
    async def transfer(self, chain: Chain,
                       to_address: str,
                       amount: Decimal,
                       token: str = None) -> str:
        """Transfer tokens."""
        bridge = self.bridges[chain]
        
        if token:
            # Token transfer
            return await self.transfer_token(bridge, to_address, amount, token)
        else:
            # Native transfer
            tx = await bridge.prepare_transfer(
                self.wallet.addresses[chain],
                to_address,
                amount
            )
            
            signed = await self.sign_transaction(tx, chain)
            return await bridge.broadcast(signed)
    
    async def sign_transaction(self, tx: Transaction, 
                               chain: Chain) -> str:
        """Sign transaction with wallet."""
        # Use wallet private key
        return self.wallet.sign(tx, chain)
```

## DeFi Operations

```python
class DeFiManager:
    """Manage DeFi interactions."""
    
    def __init__(self, bridge: AgenticBlockchainBridge):
        self.bridge = bridge
        
    async def swap(self, chain: Chain,
                   from_token: str,
                   to_token: str,
                   amount: Decimal) -> Dict:
        """Execute token swap."""
        # Find best route
        route = await self.find_best_route(chain, from_token, to_token, amount)
        
        # Execute swap
        result = await self.execute_route(route)
        
        return result
    
    async def stake(self, chain: Chain,
                    protocol: str,
                    amount: Decimal) -> Dict:
        """Stake tokens for yield."""
        # Approve tokens
        # Deposit to staking contract
        # Return stake position
        pass
    
    async def lend(self, chain: Chain,
                   protocol: str,
                   asset: str,
                   amount: Decimal) -> Dict:
        """Lend asset to protocol."""
        # Deposit to lending pool
        # Receive aTokens
        pass
```

## Process

1. **Initialize Wallet**: Load credentials
2. **Connect Chains**: Establish bridges
3. **Check Balances**: Verify resources
4. **Prepare Transaction**: Build transaction
5. **Sign**: Authorize with wallet
6. **Broadcast**: Submit to network
7. **Confirm**: Wait for finality

## Output

- Transaction receipts
- Contract deployments
- Balance updates
- DeFi positions
