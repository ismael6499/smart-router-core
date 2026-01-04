# 💱 Smart Router Core: DeFi Composability & Integration

![Solidity](https://img.shields.io/badge/Solidity-0.8.24-363636?style=flat-square&logo=solidity)
![Integration](https://img.shields.io/badge/Integration-Uniswap_V2-ff007a?style=flat-square&logo=uniswap)
![Testing](https://img.shields.io/badge/Testing-Mainnet_Forking-bf4904?style=flat-square)

A composable routing primitive for executing decentralized token swaps on the EVM.

This project focuses on **DeFi Composability**—interacting with existing on-chain protocols (Uniswap V2) to build higher-level financial logic. Unlike isolated unit tests, the Quality Assurance strategy relies on **Mainnet Forking**, allowing the contract to be tested against real market liquidity and production-deployed router logic.

## 🏗 Architecture & Design Decisions

### 1. High-Fidelity Integration Testing (Forking)
- **Simulation Strategy:**
  - Instead of mocking the Uniswap Router (which risks behavior drift from production), the test suite utilizes Foundry's `vm.createSelectFork()` to clone the state of the Ethereum Mainnet.
  - **Benefit:** Validates the integration against the *actual* bytecode and liquidity pools of Uniswap, ensuring that slippage calculations, token approval mechanics, and fee logic function correctly in a live environment context.

### 2. Protocol Composability
- **Interface-Driven Interaction:**
  - Implements `IV2Router02` to abstract the complexity of the underlying AMM (Automated Market Maker). This creates a modular "Money Lego" that can be plugged into more complex systems (like Yield Aggregators or Portfolio Managers) without exposing the low-level swap mechanics.
- **Token Agnostic Design:**
  - The architecture is generalized to handle standard ERC-20 interactions (Approve-Swap flow), establishing a reusable pattern for asset liquidation or rebalancing.

## 🛠 Tech Stack

* **Core:** Solidity `^0.8.24`
* **Integration:** Uniswap V2 Router
* **Testing:** Foundry (Mainnet Forking, Cheatcodes)

## 📝 Integration Test Pattern

The testing strategy involves impersonating a "Whale" (rich account) on a forked chain to fund the test scenarios with real tokens (USDC):

```solidity
function setUp() public {
    // 1. Fork Mainnet State
    string memory rpcUrl = vm.envString("MAINNET_RPC_URL");
    vm.createSelectFork(rpcUrl);
    
    // 2. Fund Test User (Steal USDC from a Whale)
    address usdcWhale = 0x7713974908Be4BEd47172370171a8bf907a9aD1C;
    vm.prank(usdcWhale);
    usdc.transfer(user, 1000 * 10**6);
}
