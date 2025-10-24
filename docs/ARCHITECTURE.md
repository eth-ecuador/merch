# Merch MVP Architecture

## Core Principle: The Low-Friction Funnel with Dual Assets

The Merch architecture leverages minimal transaction fees on Base and provides a fully off-chain reserve option. The key innovation is that the **SBT remains as the visible Proof of Attendance** after the Premium ERC-721 is minted, creating a dual-asset ownership model.

## System Flows

| Flow Name | Goal | Token Type | Status |
|-----------|------|------------|--------|
| **1. Creator Creates Merch** | Upload and store digital merch images | **IPFS Storage** | Available |
| **2. User Creates Event** | Create event with claim codes | **Event + Claim Codes** | Active |
| **3. User Claims Basic Merch** | Low-cost, visible POA asset acquisition | **SBT (ERC-4973)** | Owned (POA) |
| **4. Premium Upgrade** | Monetization and collectible acquisition | **SBT + ERC-721** | Owned (POA + Tradable Asset) |

## Component Overview

| Layer | Component | Function in MVP |
|-------|-----------|-----------------|
| **L2 Blockchain** | Base Sepolia Testnet | Execution and settlement environment (Smart Contracts and EAS) |
| **Frontend/UX** | Base App Mini-App (React) | User interface, Wallet integration, and transaction initiation via MiniKit |
| **Smart Contracts** | MerchManager (SBT & ERC-721) | SBT maintains POA; ERC-721 is the tradable companion asset. **No burn mechanism used** |
| **Backend/API** | Node.js/Serverless | Image storage, claim code generation, signature verification, and EAS Attestation triggering |
| **Storage** | IPFS/Pinata | Decentralized storage for merch images and metadata |
| **Core Base Tool** | Base L2 Fees | The foundational infrastructure advantage enabling the low-cost mint |
| **Data Integrity** | Ethereum Attestation Service (EAS) | **Primary Immutable Proof Layer** recording both attendance (SBT) and premium status (ERC-721) |

## Architectural Flow Diagrams

### 1. Creator Creates Merch

```
Creator → Frontend → Backend API → IPFS → Database
   ↓         ↓           ↓         ↓        ↓
Upload   /createMerch  Store     Pinata   Store
Image    API          Image     Upload   URL
```

**Process:**
1. Creator uploads image to frontend
2. Frontend sends image to backend via `/api/createMerch`
3. Backend stores image in IPFS
4. Backend stores URL in database
5. Backend responds with success

### 2. User Creates Event

```
User → Frontend → Backend API → Smart Contract → Database
  ↓        ↓           ↓            ↓          ↓
Upload   Image      Generate     Event       Claim
Event    to IPFS    Claim       Creation    Codes
Image    Upload     Codes       Function    Storage
```

**Process:**
1. User uploads event image to frontend
2. Frontend uploads image to IPFS via backend
3. Backend uploads to Pinata and returns IPFS URI
4. Frontend calls smart contract event creation function
5. Smart contract requests claim codes from backend
6. Backend generates and stores claim codes
7. Smart contract emits event creation confirmation

### 3. User Claims Basic Merch

```
User → Mini-App → Backend API → Smart Contract → EAS
  ↓        ↓           ↓            ↓          ↓
Enter   /verify-   Signature    mintSBT()   Attestation
Code    code      Generation   (User pays   Recording
        API       & Return     minimal gas)
```

**Process:**
1. User enters claim code in Mini-App
2. Mini-App calls `/verify-code` with user wallet address
3. Backend validates code and generates signature
4. Mini-App initiates standard transaction calling `MerchManager.mintSBTWithAttestation()`
5. User pays minimal L2 gas fee
6. Contract verifies signature and mints SBT
7. Backend issues EAS attestation

### 4. Premium Upgrade (Monetization)

```
User → Mini-App → Smart Contract → Treasury → EAS
  ↓        ↓           ↓            ↓        ↓
View    Upgrade    mintCompanion()  Fee     Attestation
SBT     Button     (Verify SBT      Split   Update
        Click      ownership)       (Org/  (isPremium=true)
                                   Treasury)
```

**Process:**
1. User views Basic Merch SBT
2. User clicks "Upgrade" and pays fee (test ETH)
3. Mini-App calls `MerchManager.mintCompanionWithAttestation()` with SBT ID
4. Contract verifies SBT ownership and payment
5. ERC-721 is minted while SBT is retained
6. Payment is distributed to Treasury/Organizer
7. Backend updates EAS attestation (isPremium=true)

### 5. Off-Chain Reservation Flow

```
User → Mini-App → Backend API → Database
  ↓        ↓           ↓           ↓
Enter   /claim-    Store         Reservation
Email   offchain   Reservation   Record
/Code   API        Record        (No gas paid)
```

**Process:**
1. User enters email/wallet without crypto setup
2. Mini-App calls `/claim-offchain` API
3. Backend stores reservation record
4. User receives confirmation
5. Later: User returns to complete on-chain minting

## Integration Points

### Smart Contract ↔ Backend
- **Signature Generation**: Backend signs messages for public contract functions
- **EAS Triggering**: Backend monitors transactions and issues attestations
- **Image Storage**: Backend handles IPFS uploads and URL management
- **Claim Code Generation**: Backend generates and manages claim codes for events

### Frontend ↔ Backend
- **API Authentication**: X-API-KEY header for secure endpoints
- **Image Upload**: `/api/createMerch` endpoint for creator image uploads
- **Code Verification**: `/verify-code` endpoint with signature return
- **Reservation Management**: Off-chain claim storage and redemption

### Frontend ↔ Smart Contracts
- **MiniKit Integration**: Wallet connection and transaction initiation
- **Public Minting**: Direct contract calls with backend signatures
- **Payment Handling**: ETH transfers for premium upgrades
- **Event Creation**: Smart contract integration for event setup

## Security Model

### Signature-Based Access Control
- **Public Functions**: Contract functions are public but require valid signatures
- **Backend Signing**: Only authorized backend can generate valid signatures
- **ecrecover Verification**: Contracts verify signatures against trusted issuer address

### EAS Attestation Layer
- **Immutable Records**: All claims and upgrades recorded on EAS
- **Verifiable Proof**: Third parties can verify attendance without contracts
- **Decoupled Storage**: Historical proof separate from tradable assets

## Technology Stack

| Component | Technology | Purpose |
|-----------|------------|---------|
| **Blockchain** | Base Sepolia Testnet | L2 execution environment |
| **Smart Contracts** | Solidity, Foundry | Contract development and testing |
| **Backend** | Node.js, TypeScript | API service, EAS integration, and image handling |
| **Frontend** | React, MiniKit | Base App Mini-App interface |
| **Attestations** | EAS (Ethereum Attestation Service) | Immutable proof layer |
| **Storage** | IPFS/Pinata | Decentralized image and metadata storage |
| **Database** | PostgreSQL | Claim codes, events, and user data |

## Scalability Considerations

### Current MVP Limitations
- **Base Only**: No cross-chain compatibility
- **Manual Event Setup**: No admin dashboard for event configuration
- **Basic Analytics**: No real-time webhooks or advanced analytics

### Future Enhancements (Post-MVP)
- **Paymaster Integration**: True gasless experience
- **Smart Wallet Support**: Account abstraction for seamless UX
- **Cross-Chain Support**: Multi-chain attendance verification
- **Admin Dashboard**: Event management and analytics
- **Social Features**: Farcaster Frame integration

## Data Flow Summary

1. **Creator Setup**: Creators upload merch images to IPFS via backend
2. **Event Creation**: Users create events with claim codes via smart contracts
3. **Entry Point**: User accesses Mini-App via Base App
4. **Claim Verification**: Backend validates codes and generates signatures
5. **On-Chain Execution**: User pays gas for SBT minting
6. **Attestation**: Backend issues EAS attestation for immutable proof
7. **Monetization**: Optional premium upgrade with ERC-721 minting
8. **Verification**: Third parties can verify attendance via EAS records

This architecture ensures a low-friction user experience while maintaining security and enabling the freemium monetization model through the dual-asset approach with creator-driven content.
