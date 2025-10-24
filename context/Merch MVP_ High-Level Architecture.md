# **Project: Merch MVP High-Level Architecture**

## **Core Principle: The Low-Friction Funnel with Dual Assets**

The Merch architecture now relies on minimal transaction fees on Base and provides a fully off-chain reserve option. The key is that the **SBT remains as the visible Proof of Attendance** after the Premium ERC-721 is minted.

| Flow Name | Goal | Token Type | Status |
| :---- | :---- | :---- | :---- |
| **1\. Off-Chain Reserve** | Ultra-low friction entry (no crypto wallet/gas needed yet). | **Off-Chain Reservation Record** | Reserved |
| **2\. Basic Claim (On-Chain)** | Low-cost, visible POA asset acquisition. | **SBT (ERC-4973)** | Owned (POA) |
| **3\. Premium Upgrade** | Monetization and collectible acquisition. | **SBT \+ ERC-721** | Owned (POA \+ Tradable Asset) |

## **Component Overview**

| Layer | Component | Function in MVP |
| :---- | :---- | :---- |
| **L2 Blockchain** | Base Sepolia Testnet | Execution and settlement environment (Smart Contracts and EAS). |
| **Frontend/UX** | Base App Mini-App (React) | User interface, Wallet integration, and transaction initiation via MiniKit. |
| **Smart Contracts** | Basic Merch (SBT) & Premium Merch (ERC-721) | SBT maintains POA; ERC-721 is the tradable companion asset. **No burn mechanism used.** |
| **Backend/API** | Node.js/Serverless | Claim code verification, **off-chain reservation management**, metadata service, and EAS Attestation triggering. |
| **Core Base Tool** | Base L2 Fees | The foundational infrastructure advantage enabling the low-cost mint. |
| **Data Integrity** | Ethereum Attestation Service (EAS) | **Primary Immutable Proof Layer** recording both attendance (SBT) and premium status (ERC-721). |

## **Architectural Flow Diagram**

1. **Basic Claim (On-Chain/Redemption)**:  
   * User enters code/redemption, Mini-App calls /verify-code.  
   * Mini-App initiates **standard transaction** calling BasicMerch.mintSBT().  
   * User pays the minimal L2 gas fee. Transaction executes, minting the **SBT** to the user.  
2. **Premium Upgrade (Monetization)**:  
   * User views Basic Merch, clicks "Upgrade," pays fee (ETH) via the Mini-App.  
   * Mini-App calls **PremiumMerch.mintCompanion()** (or similar function) with fee attached and passing the Basic SBT ID.  
   * Contract verifies SBT ownership and payment, then **mints the Premium ERC-721** to the user. **SBT is retained.**  
   * Mini-App receives txHash and calls **Backend API** to record the attestation (marking isPremium=true).