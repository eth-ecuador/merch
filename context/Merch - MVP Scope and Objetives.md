# **Project: Merch MVP Scope and Objectives**

## **1\. Project Goal**

To launch a Minimum Viable Product (MVP) of **Merch**—a Base-native Proof of Attendance protocol that uses Digital Merch NFTs—demonstrating a **low-cost, friction-minimized user experience (UX)** and validating the **freemium monetization model** on the **Base Sepolia Testnet**.

## **2\. Core MVP Scope (Must-Haves)**

| Component | Responsibility | Key Feature |
| :---- | :---- | :---- |
| **UX/UI** | UX/UI Designer | Single-page Mini-App UI (Claim & Upgrade views). **Merch Collection (Basic & Premium) design.** |
| **Blockchain** | Smart Contract Eng. | **Dual Token Ownership:** Basic Merch (SBT) **is NOT burned**; Premium Merch (ERC-721) is **minted as a companion asset.** |
| **Onboarding** | Frontend \+ Backend | **Off-Chain Reservation** for gas-less/new users (via email/wallet) for later redemption. |
| **Onboarding** | Frontend \+ Backend | **Low-Cost Claim Flow** where the user pays the minimal L2 gas fee for immediate on-chain minting. |
| **Data Integrity** | Smart Contract \+ Backend | **EAS Attestation** issuance post-NFT minting for verifiable, open attendance records. |
| **Monetization** | All Roles | Paid upgrade process on Testnet (ETH on Base Sepolia) and Treasury/Fee distribution logic implementation. |

## **3\. Exclusions (Features for Post-MVP)**

* Paymaster/Gas Sponsorship: **Excluded from MVP.** Will be implemented in Phase 2/3 to achieve the true zero-gas experience.  
* Smart Wallet/Account Abstraction Redemption: The off-chain redemption requires the user to connect and pay gas (minimal L2 fee). Full **Smart Wallet integration** for gasless redemption will be a Phase 2 feature.  
* No cross-chain compatibility (Base only).  
* No Decentralized Identity (DI) integration.  
* No Admin/Organizer Dashboard (event configuration is hard-coded or manually scripted).  
* No real-time analytics/webhooks (basic logging only).  
* No social features beyond Farcaster Frame compatibility.

## **4\. Definition of Success (MVP Completion)**

The MVP is considered complete when a user can successfully:

1. Access the Merch Mini-App on a mobile device (Base App context).  
2. **Scenario A (Off-Chain):** Reserve a claim via email/wallet without paying gas, and later return to mint the **Basic Merch SBT** on-chain, paying the minimal L2 gas fee.  
3. **Scenario B (On-Chain):** Mint a **Basic Merch SBT** immediately by paying the minimal L2 gas fee.  
4. View the Basic Merch item and choose to pay a small fee (in test ETH) to **upgrade** to a Premium Merch ERC-721.  
5. The upgrade transaction successfully **mints the ERC-721** while **retaining the original SBT**, and the backend successfully issues an **EAS Attestation** recording both the claim and the upgrade.  
* 