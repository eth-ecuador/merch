# Merch MVP Scope and Objectives

## 1. Project Goal

To launch a Minimum Viable Product (MVP) of **Merch**—a Base-native Proof of Attendance protocol that uses Digital Merch NFTs—demonstrating a **low-cost, friction-minimized user experience (UX)** and validating the **freemium monetization model** on the **Base Sepolia Testnet**.

## 2. Core MVP Scope (Must-Haves)

| Component | Responsibility | Key Feature |
|-----------|---------------|-------------|
| **UX/UI** | UX/UI Designer | Multi-page Mini-App UI (Creator Upload, Event Creation, Claim & Upgrade views). **Merch Collection (Basic & Premium) design.** |
| **Blockchain** | Smart Contract Eng. | **Unified MerchManager Contract:** Basic Merch (SBT) **is NOT burned**; Premium Merch (ERC-721) is **minted as a companion asset.** |
| **Creator Tools** | Frontend + Backend | **Image Upload System** for Creators to upload merch images to IPFS/Pinata. |
| **Event Management** | Frontend + Backend | **Event Creation System** for Users to create events with claim codes. |
| **Onboarding** | Frontend + Backend | **Off-Chain Reservation** for gas-less/new users (via email/wallet) for later redemption. |
| **Onboarding** | Frontend + Backend | **Low-Cost Claim Flow** where the user pays the minimal L2 gas fee for immediate on-chain minting. |
| **Data Integrity** | Smart Contract + Backend | **EAS Attestation** issuance post-NFT minting for verifiable, open attendance records. |
| **Monetization** | All Roles | Paid upgrade process on Testnet (ETH on Base Sepolia) and Treasury/Fee distribution logic implementation. |

## 3. Exclusions (Features for Post-MVP)

- **Paymaster/Gas Sponsorship**: **Excluded from MVP.** Will be implemented in Phase 2/3 to achieve the true zero-gas experience.
- **Smart Wallet/Account Abstraction Redemption**: The off-chain redemption requires the user to connect and pay gas (minimal L2 fee). Full **Smart Wallet integration** for gasless redemption will be a Phase 2 feature.
- **No cross-chain compatibility (Base only).**
- **No Decentralized Identity (DI) integration.**
- **No Admin/Organizer Dashboard (event configuration is hard-coded or manually scripted).**
- **No real-time analytics/webhooks (basic logging only).**
- **No social features beyond Farcaster Frame compatibility.**

## 4. Definition of Success (MVP Completion)

The MVP is considered complete when users can successfully:

1. **Creator Flow:** Upload merch images to IPFS and have them stored in the database.
2. **Event Creation Flow:** Create events with images and generate claim codes.
3. **Access the Merch Mini-App** on a mobile device (Base App context).
4. **Scenario A (Off-Chain):** Reserve a claim via email/wallet without paying gas, and later return to mint the **Basic Merch SBT** on-chain, paying the minimal L2 gas fee.
5. **Scenario B (On-Chain):** Mint a **Basic Merch SBT** immediately by paying the minimal L2 gas fee.
6. **View the Basic Merch item** and choose to pay a small fee (in test ETH) to **upgrade** to a Premium Merch ERC-721.
7. **The upgrade transaction successfully** **mints the ERC-721** while **retaining the original SBT**, and the backend successfully issues an **EAS Attestation** recording both the claim and the upgrade.

## 5. Technical Requirements

### Smart Contract Requirements
- **MerchManager.sol**: Unified contract managing both SBT and ERC-721 tokens
- **Signature Security**: Public functions with backend signature verification
- **Treasury Management**: Configurable fee distribution between organizer and treasury
- **Image URL Support**: Contract functions accepting IPFS URLs for token metadata

### Backend Requirements
- **API Endpoints**: Complete REST API with authentication including image upload endpoints
- **Image Storage**: IPFS/Pinata integration for image uploads and storage
- **Signature Generation**: Secure ECDSA signature creation for contract calls
- **Database Management**: Merch items, events, claim codes, reservations, and attestation tracking
- **EAS Integration**: Automated attestation issuance post-transaction

### Frontend Requirements
- **Mini-App Interface**: React-based Mini-App for Base App with multiple views
- **Image Upload**: Drag-and-drop interface for Creator image uploads
- **Event Creation**: Form interface for User event creation with claim codes
- **Wallet Integration**: MiniKit integration for seamless transactions
- **Multi-Step Flow**: Code verification → signature → transaction → confirmation
- **Responsive Design**: Mobile-first design optimized for Base App

## 6. Success Metrics

### User Experience Metrics
- **Conversion Rate**: >80% of valid claim codes result in successful SBT minting
- **Gas Cost**: <$0.01 USD for basic SBT minting on Base Sepolia
- **Transaction Time**: <30 seconds from code entry to SBT minting
- **Error Rate**: <5% transaction failure rate

### Technical Metrics
- **API Response Time**: <1 second for all API endpoints
- **Contract Gas Usage**: Optimized for minimal gas consumption
- **Uptime**: >99% availability during MVP testing period
- **Security**: Zero unauthorized mints or signature bypasses

### Business Metrics
- **Premium Upgrade Rate**: >20% of SBT holders upgrade to premium
- **Revenue Generation**: Successful fee collection and treasury distribution
- **User Retention**: >60% of users return to view their NFTs
- **Attestation Success**: 100% of successful mints receive EAS attestations

## 7. MVP Testing Strategy

### Test Scenarios
1. **Happy Path**: Valid claim code → successful SBT mint → premium upgrade
2. **Off-Chain Flow**: Email reservation → later redemption → successful mint
3. **Error Handling**: Invalid codes, network failures, transaction failures
4. **Security Testing**: Signature verification, unauthorized access attempts
5. **Performance Testing**: High load, concurrent users, gas optimization

### Test Data Requirements
- **Claim Codes**: Pre-generated valid codes for testing
- **Test Wallets**: Multiple test wallets with Base Sepolia ETH
- **Event Data**: Sample event information and metadata
- **Error Cases**: Invalid codes, expired codes, already-used codes

## 8. Deployment Requirements

### Base Sepolia Testnet
- **Contract Deployment**: Both BasicMerch and PremiumMerch contracts
- **EAS Schema**: Custom MerchAttendance schema registration
- **Treasury Setup**: Configured fee distribution addresses
- **Backend Deployment**: API service with database connectivity

### Environment Configuration
- **API Keys**: Secure key management for backend authentication
- **Database**: PostgreSQL instance for data persistence
- **IPFS/Arweave**: Metadata storage for NFT images and JSON
- **Monitoring**: Basic logging and error tracking

## 9. Post-MVP Roadmap

### Phase 2 Features
- **Paymaster Integration**: True gasless experience for users
- **Smart Wallet Support**: Account abstraction for seamless UX
- **Admin Dashboard**: Event management and analytics interface
- **Cross-Chain Support**: Multi-chain attendance verification

### Phase 3 Features
- **Social Integration**: Farcaster Frame compatibility
- **Advanced Analytics**: Real-time webhooks and detailed metrics
- **Decentralized Identity**: DID integration for enhanced verification
- **Mobile App**: Standalone mobile application

## 10. Risk Mitigation

### Technical Risks
- **Contract Security**: Comprehensive testing and audit preparation
- **Signature Security**: Robust key management and rotation
- **API Security**: Rate limiting and input validation
- **Database Security**: Encryption and access controls

### Business Risks
- **User Adoption**: Clear value proposition and user education
- **Gas Costs**: Base L2 optimization and fee estimation
- **Competition**: Unique value proposition and Base-native advantages
- **Regulatory**: Compliance with applicable regulations

## 11. Success Criteria Summary

The Merch MVP will be considered successful when:

✅ **Users can mint Basic Merch SBTs** with minimal gas fees  
✅ **Users can upgrade to Premium Merch ERC-721s** with test ETH  
✅ **All transactions generate EAS attestations** for verifiable proof  
✅ **Off-chain reservations work** for gasless user entry  
✅ **Treasury distribution functions** correctly  
✅ **Signature security prevents** unauthorized minting  
✅ **Mobile UX is smooth** and intuitive  
✅ **Base Sepolia deployment** is stable and accessible  

This scope ensures a focused, achievable MVP that validates the core value proposition while establishing the foundation for future enhancements and scaling.
