 **Project: Merch MVP API/Backend Specification**  
The Merch MVP backend service is critical for managing secure claim verification, NFT metadata, and interaction with the Ethereum Attestation Service (EAS). All POST endpoints require a secure API key passed in the **X-API-KEY** header. Unauthorized requests (missing/invalid key) must return **HTTP 401** with the payload: {"error": "Unauthorized"}.

## **1\. Responsibilities**

* **Claim Management:** Securely store and verify single-use claim codes for events, managing both **on-chain and off-chain** claim status.  
* **CRITICAL:** **Signature Generation:** Generate the unique digital signature required by the public BasicMerch.mintSBT function.  
* **Off-Chain Reserve:** Manage claims associated with non-crypto identifiers (email) for later on-chain redemption.  
* **Metadata Hosting:** Serve the JSON metadata files for both SBT and ERC-721 tokens via IPFS/Arweave.  
* **EAS Attestation:** Handle the non-user-facing gas payment and execution for issuing EAS attestations.

## **2\. Endpoint Definitions**

| Endpoint | Method | Description | Input Parameters | Output Payload |
| :---- | :---- | :---- | :---- | :---- |
| /verify-code | POST | Verifies claim code. **Returns Signature for public contract call.** **Requires X-API-KEY.** | code (string), walletAddress (string) | eventId (string (hex-encoded bytes32)), tokenURI (string), **signature (bytes: ECDSA signature)**, is\_valid (boolean) |
| /claim-offchain | POST | Records a claim off-chain, reserving the Merch for later on-chain redemption. Used for gas-less or new users. **Requires X-API-KEY.** | code (string), userIdentifier (string: wallet or email), type (string: 'wallet' or 'email') | reservationId (string), message (string) |
| /attest-claim | POST | **Critical EAS Endpoint.** Issues the EAS Attestation after a successful on-chain transaction. **Requires X-API-KEY.** | txHash (string), walletAddress (string), eventId (string (hex-encoded bytes32)), isPremium (bool) | attestationUID (string) |
| /token-metadata/:id | GET | Serves the JSON metadata for a specific Merch NFT ID. **Does not require API key.** | id (string) | Standard ERC-721 JSON metadata. |

## **3\. Basic Claim Flow (Meta-Transaction Style)**

1. **Frontend Request:** Mini-App calls /verify-code with user wallet address and claim code.  
2. **Backend Verification:** Backend validates the claim code against the DB.  
3. **Signature Generation:** If valid, the Backend Issuer Wallet signs a unique message that includes: eventId, walletAddress, and tokenURI.  
4. **Frontend Execution:** The Frontend receives the signature and initiates a standard transaction calling the **public** BasicMerch.mintSBT(..., signature).  
5. **Contract Execution:** The contract verifies the signature against the trusted Issuer's address (using ecrecover). If valid, the SBT is minted. **The user pays the gas for this public transaction.**

