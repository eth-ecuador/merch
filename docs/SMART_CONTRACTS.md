# Smart Contract Specification

This specification details the design and core functions of the smart contracts for deployment on the **Base Sepolia Testnet**.

## 1. MerchManager Contract (MerchManager.sol)

| Standard | ERC-4973 (SBT) + ERC-721 (NFT) |
|----------|--------------------------------|
| **Purpose** | **UNIFIED CONTRACT** managing both Basic Merch (SBT) and Premium Merch (ERC-721) tokens |
| **Key Functions** | **mintSBTWithAttestation(string memory _imageURL, address _userAddress, ...):** Mints Basic Merch SBT with EAS attestation |
| | **mintCompanionWithAttestation(string memory _imageURL, address _userAddress, uint256 _attestationId, uint256 _eventId):** Mints Premium Merch ERC-721 |
| **Access Control** | Public functions with signature verification for security |

### Function Specifications

#### `mintSBTWithAttestation(string memory _imageURL, address _userAddress, ...)`

**Purpose**: Mint a Basic Merch SBT with EAS attestation for proof of attendance

**Parameters**:
- `_imageURL`: IPFS URL of the Basic Merch image
- `_userAddress`: Address to receive the SBT
- Additional parameters for event and signature verification

**Access Control**: Public function with signature verification

**Security Model**:
1. Function is public (anyone can call and pay gas)
2. Requires valid signature from trusted backend issuer
3. Uses `ecrecover` to verify signature authenticity
4. Prevents unauthorized minting while allowing user-paid transactions
5. Automatically triggers EAS attestation creation

**Implementation Logic**:
```solidity
function mintSBTWithAttestation(string memory _imageURL, address _userAddress, ...) public {
    // 1. Verify signature and claim code
    // 2. Check if user already has SBT for this event
    // 3. Mint the SBT with image URL
    // 4. Trigger EAS attestation
    // 5. Emit events
}
```

#### `mintCompanionWithAttestation(string memory _imageURL, address _userAddress, uint256 _attestationId, uint256 _eventId)`

**Purpose**: Mint Premium Merch ERC-721 as companion to existing SBT

**Parameters**:
- `_imageURL`: IPFS URL of the Premium Merch image
- `_userAddress`: Address to receive the ERC-721
- `_attestationId`: ID of the related Basic Merch attestation
- `_eventId`: Event identifier

**Access Control**: Public function with SBT ownership verification

**Security Model**:
1. Verifies caller owns the prerequisite SBT
2. Ensures SBT is from the correct event
3. Prevents duplicate premium claims for same SBT
4. Distributes payment between treasury and organizer
5. Links Premium NFT to Basic SBT via attestation

**Implementation Logic**:
```solidity
function mintCompanionWithAttestation(string memory _imageURL, address _userAddress, uint256 _attestationId, uint256 _eventId) public payable {
    // 1. Verify SBT ownership via attestation
    // 2. Check payment amount
    // 3. Prevent duplicate premium claims
    // 4. Mint ERC-721 with image URL
    // 5. Distribute payment
    // 6. Emit events
}
```

## 2. Treasury and Fee Management

#### Configurable Addresses
- `treasuryAddress`: Primary treasury for fee collection
- `organizerAddress`: Event organizer fee recipient
- `upgradeFee`: Required payment amount for premium upgrade
- `organizerFeePercent`: Percentage of fees going to organizer

#### Fee Distribution Logic
1. **Total Payment**: `msg.value` (user's ETH payment)
2. **Organizer Share**: `(msg.value * organizerFeePercent) / 100`
3. **Treasury Share**: `msg.value - organizerShare`
4. **Automatic Transfer**: Funds distributed immediately upon successful mint

## 3. EAS Attestation Logic

| Standard | Ethereum Attestation Service (EAS) |
|----------|-----------------------------------|
| **Schema** | Custom schema registered on Base Sepolia: **MerchAttendance** |
| **Data Fields** | eventId (bytes32), timestamp (uint256), isPremium (bool), **sbtTokenId (uint256)**, **imageURL (string)** |
| **Attester** | The dedicated **Backend Attester Wallet** (pre-funded) |
| **Trigger** | Executed by the **Backend API** upon successful on-chain transaction confirmation (Mint of SBT *or* Mint of ERC-721) |
| **Role** | Provides the verifiable, immutable record of the attendance claim, linking Basic and Premium tokens via attestation relationships |

### EAS Schema Definition

```json
{
  "name": "MerchAttendance",
  "schema": "bytes32 eventId, uint256 timestamp, bool isPremium, uint256 sbtTokenId, string imageURL",
  "description": "Merch MVP attendance attestation with premium status tracking and image URLs"
}
```

### Attestation Data Fields

| Field | Type | Description |
|-------|------|-------------|
| `eventId` | bytes32 | Unique event identifier (hex-encoded) |
| `timestamp` | uint256 | Block timestamp of the claim/upgrade |
| `isPremium` | bool | Whether user has upgraded to premium (ERC-721) |
| `sbtTokenId` | uint256 | Token ID of the prerequisite SBT |
| `imageURL` | string | IPFS URL of the merch image |

### Attestation Triggers

#### Basic Claim Attestation
- **Trigger**: Successful SBT minting transaction
- **Data**: `isPremium = false`, `sbtTokenId = minted SBT ID`, `imageURL = Basic Merch image`
- **Purpose**: Record basic attendance claim with image reference

#### Premium Upgrade Attestation
- **Trigger**: Successful ERC-721 minting transaction
- **Data**: `isPremium = true`, `sbtTokenId = original SBT ID`, `imageURL = Premium Merch image`
- **Purpose**: Record premium upgrade while maintaining SBT link and image reference

### Backend Integration

The backend service handles EAS attestation through the `/attest-claim` endpoint:

```typescript
// Backend attestation flow
async function issueAttestation(txHash: string, walletAddress: string, eventId: string, isPremium: boolean, imageURL: string) {
    // 1. Verify transaction success
    const tx = await provider.getTransaction(txHash);
    const receipt = await tx.wait();
    
    // 2. Extract SBT token ID from transaction logs
    const sbtTokenId = extractSBTTokenId(receipt.logs);
    
    // 3. Create attestation data
    const attestationData = {
        eventId: eventId,
        timestamp: receipt.blockTimestamp,
        isPremium: isPremium,
        sbtTokenId: sbtTokenId,
        imageURL: imageURL
    };
    
    // 4. Issue EAS attestation
    const attestationUID = await easContract.attest(schemaUID, attestationData);
    
    return attestationUID;
}
```

## Contract Deployment Considerations

### Base Sepolia Testnet Configuration
- **Network**: Base Sepolia Testnet
- **Gas Optimization**: Leverage L2 low fees for user transactions
- **EAS Integration**: Deploy with EAS schema registration
- **Treasury Setup**: Configure fee distribution addresses

### Security Considerations
- **Signature Verification**: Robust ecrecover implementation
- **Reentrancy Protection**: Secure payment handling
- **Access Control**: Proper role management for admin functions
- **Upgrade Safety**: Consider proxy patterns for future upgrades

### Testing Requirements
- **Unit Tests**: Individual function testing
- **Integration Tests**: Cross-contract interaction testing
- **Signature Tests**: Backend signature generation and verification
- **Payment Tests**: Fee distribution and treasury management
- **EAS Tests**: Attestation creation and verification

This specification ensures secure, efficient, and user-friendly smart contract implementation for the Merch MVP while maintaining the dual-asset model and enabling the freemium monetization strategy.
