# API/Backend Specification

The Merch MVP backend service is critical for managing secure claim verification, NFT metadata, and interaction with the Ethereum Attestation Service (EAS). All POST endpoints require a secure API key passed in the **X-API-KEY** header. Unauthorized requests (missing/invalid key) must return **HTTP 401** with the payload: `{"error": "Unauthorized"}`.

## 1. Responsibilities

- **Image Management:** Handle image uploads from Creators and store them in IPFS/Pinata
- **Event Management:** Generate and manage claim codes for events created by Users
- **Claim Management:** Securely store and verify single-use claim codes for events, managing both **on-chain and off-chain** claim status
- **CRITICAL:** **Signature Generation:** Generate the unique digital signature required by the public MerchManager.mintSBTWithAttestation function
- **Off-Chain Reserve:** Manage claims associated with non-crypto identifiers (email) for later on-chain redemption
- **Metadata Hosting:** Serve the JSON metadata files for both SBT and ERC-721 tokens via IPFS/Arweave
- **EAS Attestation:** Handle the non-user-facing gas payment and execution for issuing EAS attestations

## 2. Endpoint Definitions

| Endpoint | Method | Description | Input Parameters | Output Payload |
|----------|--------|-------------|------------------|----------------|
| `/api/createMerch` | POST | **Creator endpoint.** Uploads merch image to IPFS and stores URL in database. **Requires X-API-KEY.** | image (file), description (string) | merchId (string), imageURL (string), success (boolean) |
| `/api/createEvent` | POST | **Event creation.** Uploads event image and generates claim codes. **Requires X-API-KEY.** | eventImage (file), eventData (object), claimCodeCount (number) | eventId (string), claimCodes (array), imageURL (string) |
| `/verify-code` | POST | Verifies claim code. **Returns Signature for public contract call.** **Requires X-API-KEY.** | code (string), walletAddress (string) | eventId (string (hex-encoded bytes32)), tokenURI (string), **signature (bytes: ECDSA signature)**, is_valid (boolean) |
| `/claim-offchain` | POST | Records a claim off-chain, reserving the Merch for later on-chain redemption. Used for gas-less or new users. **Requires X-API-KEY.** | code (string), userIdentifier (string: wallet or email), type (string: 'wallet' or 'email') | reservationId (string), message (string) |
| `/attest-claim` | POST | **Critical EAS Endpoint.** Issues the EAS Attestation after a successful on-chain transaction. **Requires X-API-KEY.** | txHash (string), walletAddress (string), eventId (string (hex-encoded bytes32)), isPremium (bool), imageURL (string) | attestationUID (string) |
| `/token-metadata/:id` | GET | Serves the JSON metadata for a specific Merch NFT ID. **Does not require API key.** | id (string) | Standard ERC-721 JSON metadata |

## 3. Detailed Endpoint Specifications

### POST /api/createMerch

**Purpose**: Upload merch image to IPFS and store in database

**Authentication**: Requires `X-API-KEY` header

**Request Body** (multipart/form-data):
```
image: file (required)
description: string (optional)
```

**Response (Success - 200)**:
```json
{
  "merchId": "uuid-string",
  "imageURL": "https://ipfs.io/ipfs/QmHash...",
  "success": true
}
```

**Response (Error - 400)**:
```json
{
  "error": "Invalid image file or missing required fields"
}
```

**Implementation Logic**:
1. Validate API key and image file
2. Upload image to IPFS/Pinata
3. Store image URL and metadata in database
4. Return success with IPFS URL

### POST /api/createEvent

**Purpose**: Create event with image and generate claim codes

**Authentication**: Requires `X-API-KEY` header

**Request Body** (multipart/form-data):
```
eventImage: file (required)
eventData: {
  name: string,
  description: string,
  date: string,
  location: string
}
claimCodeCount: number (required)
```

**Response (Success - 200)**:
```json
{
  "eventId": "uuid-string",
  "claimCodes": ["CODE123", "CODE456", "CODE789"],
  "imageURL": "https://ipfs.io/ipfs/QmEventHash...",
  "success": true
}
```

**Implementation Logic**:
1. Validate API key and event data
2. Upload event image to IPFS/Pinata
3. Generate specified number of claim codes
4. Store event and claim codes in database
5. Return event ID and claim codes

### POST /verify-code

**Purpose**: Verify claim code and generate signature for public contract call

**Authentication**: Requires `X-API-KEY` header

**Request Body**:
```json
{
  "code": "string",
  "walletAddress": "string"
}
```

**Response (Success - 200)**:
```json
{
  "eventId": "0x1234567890abcdef...",
  "tokenURI": "https://ipfs.io/ipfs/QmHash...",
  "signature": "0xabcdef1234567890...",
  "is_valid": true
}
```

**Response (Error - 400)**:
```json
{
  "error": "Invalid claim code",
  "is_valid": false
}
```

**Response (Error - 401)**:
```json
{
  "error": "Unauthorized"
}
```

**Implementation Logic**:
1. Validate API key from `X-API-KEY` header
2. Check if claim code exists and is unused
3. Verify wallet address format
4. Generate ECDSA signature using backend issuer private key
5. Mark claim code as used
6. Return signature and metadata for frontend contract call

### POST /claim-offchain

**Purpose**: Reserve a claim off-chain for gasless users

**Authentication**: Requires `X-API-KEY` header

**Request Body**:
```json
{
  "code": "string",
  "userIdentifier": "string",
  "type": "wallet" | "email"
}
```

**Response (Success - 200)**:
```json
{
  "reservationId": "uuid-string",
  "message": "Claim reserved successfully. Return to complete on-chain minting."
}
```

**Response (Error - 400)**:
```json
{
  "error": "Invalid claim code or user identifier"
}
```

**Implementation Logic**:
1. Validate API key and request parameters
2. Check claim code availability
3. Store reservation record in database
4. Generate unique reservation ID
5. Return confirmation message

### POST /attest-claim

**Purpose**: Issue EAS attestation after successful on-chain transaction

**Authentication**: Requires `X-API-KEY` header

**Request Body**:
```json
{
  "txHash": "string",
  "walletAddress": "string",
  "eventId": "string",
  "isPremium": boolean
}
```

**Response (Success - 200)**:
```json
{
  "attestationUID": "0xabcdef1234567890..."
}
```

**Response (Error - 400)**:
```json
{
  "error": "Transaction verification failed"
}
```

**Implementation Logic**:
1. Verify transaction hash and receipt
2. Extract SBT token ID from transaction logs
3. Create attestation data object
4. Call EAS contract to issue attestation
5. Return attestation UID

### GET /token-metadata/:id

**Purpose**: Serve NFT metadata for token display

**Authentication**: No API key required (public endpoint)

**Parameters**:
- `id`: Token ID (string)

**Response (Success - 200)**:
```json
{
  "name": "Merch Basic #123",
  "description": "Proof of Attendance for ETH Ecuador Event",
  "image": "https://ipfs.io/ipfs/QmImageHash...",
  "attributes": [
    {
      "trait_type": "Event",
      "value": "ETH Ecuador 2024"
    },
    {
      "trait_type": "Type",
      "value": "Basic"
    }
  ]
}
```

**Response (Error - 404)**:
```json
{
  "error": "Token metadata not found"
}
```

## 4. Basic Claim Flow (Meta-Transaction Style)

### Step-by-Step Process

1. **Frontend Request**: Mini-App calls `/verify-code` with user wallet address and claim code
2. **Backend Verification**: Backend validates the claim code against the database
3. **Signature Generation**: If valid, the Backend Issuer Wallet signs a unique message that includes: eventId, walletAddress, and tokenURI
4. **Frontend Execution**: The Frontend receives the signature and initiates a standard transaction calling the **public** BasicMerch.mintSBT(..., signature)
5. **Contract Execution**: The contract verifies the signature against the trusted Issuer's address (using ecrecover). If valid, the SBT is minted. **The user pays the gas for this public transaction**

### Signature Generation Algorithm

```typescript
// Backend signature generation
function generateSignature(walletAddress: string, eventId: string, tokenURI: string): string {
    // 1. Create message hash
    const messageHash = ethers.utils.keccak256(
        ethers.utils.defaultAbiCoder.encode(
            ['address', 'uint256', 'string'],
            [walletAddress, eventId, tokenURI]
        )
    );
    
    // 2. Sign with backend issuer private key
    const signature = await backendIssuerWallet.signMessage(
        ethers.utils.arrayify(messageHash)
    );
    
    return signature;
}
```

## 5. Database Schema

### Merch Items Table
```sql
CREATE TABLE merch_items (
    id UUID PRIMARY KEY,
    image_url TEXT NOT NULL,
    description TEXT,
    created_by VARCHAR(42) NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);
```

### Events Table
```sql
CREATE TABLE events (
    id UUID PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    image_url TEXT NOT NULL,
    date TIMESTAMP,
    location VARCHAR(255),
    created_by VARCHAR(42) NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);
```

### Claim Codes Table
```sql
CREATE TABLE claim_codes (
    id SERIAL PRIMARY KEY,
    code VARCHAR(255) UNIQUE NOT NULL,
    event_id UUID NOT NULL,
    token_uri TEXT NOT NULL,
    is_used BOOLEAN DEFAULT FALSE,
    used_by VARCHAR(42),
    used_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW(),
    FOREIGN KEY (event_id) REFERENCES events(id)
);
```

### Off-Chain Reservations Table
```sql
CREATE TABLE offchain_reservations (
    id UUID PRIMARY KEY,
    claim_code VARCHAR(255) NOT NULL,
    user_identifier VARCHAR(255) NOT NULL,
    user_type VARCHAR(10) NOT NULL, -- 'wallet' or 'email'
    created_at TIMESTAMP DEFAULT NOW(),
    redeemed_at TIMESTAMP,
    FOREIGN KEY (claim_code) REFERENCES claim_codes(code)
);
```

### EAS Attestations Table
```sql
CREATE TABLE eas_attestations (
    id SERIAL PRIMARY KEY,
    attestation_uid VARCHAR(66) UNIQUE NOT NULL,
    wallet_address VARCHAR(42) NOT NULL,
    event_id VARCHAR(66) NOT NULL,
    sbt_token_id INTEGER NOT NULL,
    is_premium BOOLEAN DEFAULT FALSE,
    tx_hash VARCHAR(66) NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);
```

## 6. Environment Configuration

### Required Environment Variables
```bash
# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/merch_db

# Blockchain
BASE_SEPOLIA_RPC_URL=https://sepolia.base.org
BACKEND_ISSUER_PRIVATE_KEY=0x...
EAS_CONTRACT_ADDRESS=0x...
EAS_SCHEMA_UID=0x...

# API Security
API_KEY_SECRET=your-secure-api-key

# IPFS/Pinata Storage
PINATA_API_KEY=your-pinata-api-key
PINATA_SECRET_KEY=your-pinata-secret-key
IPFS_GATEWAY_URL=https://ipfs.io/ipfs/

# Image Processing
MAX_IMAGE_SIZE=10485760  # 10MB
ALLOWED_IMAGE_TYPES=image/jpeg,image/png,image/gif,image/webp
```

## 7. Error Handling

### Standard Error Responses
```json
{
  "error": "Error message",
  "code": "ERROR_CODE",
  "details": "Additional context"
}
```

### Common Error Codes
- `INVALID_API_KEY`: Missing or invalid X-API-KEY header
- `INVALID_CLAIM_CODE`: Claim code not found or already used
- `INVALID_WALLET_ADDRESS`: Malformed wallet address
- `TRANSACTION_FAILED`: On-chain transaction verification failed
- `EAS_ATTESTATION_FAILED`: EAS attestation creation failed
- `METADATA_NOT_FOUND`: Token metadata not found

## 8. Rate Limiting and Security

### Rate Limiting
- **General endpoints**: 100 requests per minute per IP
- **Verify-code**: 10 requests per minute per wallet address
- **Attest-claim**: 5 requests per minute per wallet address

### Security Measures
- **API Key Rotation**: Regular key updates for production
- **Input Validation**: Strict parameter validation
- **SQL Injection Prevention**: Parameterized queries
- **CORS Configuration**: Restricted to Base App domains
- **Request Logging**: Comprehensive audit trail

This API specification ensures secure, efficient backend operations for the Merch MVP while maintaining the signature-based security model and enabling seamless integration with the smart contracts and frontend.
