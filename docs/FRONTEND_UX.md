# Frontend/UX Specification

This document serves as the **design blueprint** for the Mini-App interface, to be created by the **UX/UI Designer** and implemented by the **Frontend Engineer**.

## 1. Design & Aesthetic Goals (UX/UI Designer)

- **Aesthetic:** Modern, clean, and mobile-first, adhering strictly to the defined **Branding Kit**
- **Messaging Focus:** Clearly communicate the steps: **1. Validate Code (Backend)** → **2. Mint Now (User Pays Gas)**

## 2. Required Views and Interaction States (UX/UI Designer Tasks)

| View | Primary Goal | Key UI Elements & Design Focus |
|------|--------------|--------------------------------|
| **1. Creator Upload** | Enable Creators to upload merch images | Image upload interface with drag-and-drop, preview, and metadata input |
| **2. Event Creation** | Allow Users to create events with claim codes | Event form with image upload, details input, and claim code generation |
| **3. Claim Input** | Maximize conversion rate for code entry | Single clean input field for the claim code. **Primary Option:** "Check Code & Mint" (Initial button, no transaction). **Secondary Option:** "Reserve Off-Chain (Free Now)." |
| **4. Code Verified/Mint Ready** | Prompt user to finalize the on-chain step | **State:** Code is validated, Backend API returns success and signature. **Primary Button:** "Finalize Mint (Pay Minimal Gas Fee)." |
| **5. Processing/Loading** | Communicate user-paid transaction status clearly | Custom loading indicator. Clear status text: "Waiting for wallet confirmation... Minimal L2 Fee Required." |
| **6. Basic Merch View (SBT)** | Showcase the claimed SBT and encourage the paid upgrade | **Crucially:** Always visible as the POA, even if Premium is owned. |
| **7. Premium Upgrade View** | Facilitate monetization through premium upgrade | Clear pricing display, payment confirmation, and upgrade process |

## 3. Detailed View Specifications

### View 1: Creator Upload

**Purpose**: Allow Creators to upload merch images for the platform

**UI Elements**:
- **Header**: "Upload Your Merch" with creator branding
- **Image Upload Area**: Large drag-and-drop zone with file input
- **Image Preview**: Thumbnail preview of uploaded image
- **Description Field**: Text input for merch description
- **Upload Button**: "Upload to IPFS" primary action
- **Help Text**: "Upload high-quality images for your merch items"

**User Flow**:
1. Creator drags and drops or selects image file
2. Image preview appears with metadata
3. Creator adds description (optional)
4. Creator clicks "Upload to IPFS"
5. Frontend calls `/api/createMerch` API
6. On success: Show success message with IPFS URL
7. On error: Show error message, allow retry

**Error States**:
- Invalid file type: "Please upload a valid image file (JPEG, PNG, GIF, WebP)"
- File too large: "Image must be less than 10MB"
- Upload failed: "Upload failed. Please try again."

### View 2: Event Creation

**Purpose**: Allow Users to create events with claim codes

**UI Elements**:
- **Header**: "Create Your Event" with event branding
- **Event Image Upload**: Drag-and-drop for event image
- **Event Details Form**: Name, description, date, location fields
- **Claim Code Count**: Number input for claim codes to generate
- **Create Button**: "Create Event & Generate Codes" primary action
- **Preview Section**: Show generated claim codes

**User Flow**:
1. User uploads event image
2. User fills in event details
3. User specifies number of claim codes needed
4. User clicks "Create Event & Generate Codes"
5. Frontend calls `/api/createEvent` API
6. On success: Show event details and claim codes
7. User can copy claim codes for distribution

**Error States**:
- Missing required fields: "Please fill in all required fields"
- Invalid image: "Please upload a valid event image"
- Creation failed: "Event creation failed. Please try again."

### View 3: Claim Input

**Purpose**: Initial entry point for users to input their claim code

**UI Elements**:
- **Header**: "Claim Your Merch" with event branding
- **Input Field**: Large, prominent text input for claim code
- **Primary Button**: "Check Code & Mint" (validates code, no transaction yet)
- **Secondary Button**: "Reserve Off-Chain (Free Now)" (for gasless users)
- **Help Text**: "Enter your event claim code to mint your Proof of Attendance NFT"

**User Flow**:
1. User enters claim code
2. User clicks "Check Code & Mint"
3. Frontend calls `/verify-code` API
4. On success: Navigate to "Code Verified" view
5. On error: Show error message, allow retry

**Error States**:
- Invalid code: "Code not found. Please check and try again."
- Network error: "Connection failed. Please try again."
- Already claimed: "This code has already been used."

### View 4: Code Verified/Mint Ready

**Purpose**: Confirm code validation and prompt for on-chain minting

**UI Elements**:
- **Success Indicator**: Checkmark or success icon
- **Event Details**: Event name, date, location
- **Token Preview**: Basic Merch NFT preview image
- **Primary Button**: "Finalize Mint (Pay Minimal Gas Fee)"
- **Secondary Button**: "Cancel" (return to input)
- **Fee Display**: "Estimated gas fee: ~$0.01 USD"

**User Flow**:
1. Display validated code information
2. Show token metadata and preview
3. User clicks "Finalize Mint"
4. Trigger wallet connection (if not connected)
5. Initiate contract transaction with signature
6. Navigate to "Processing" view

**State Management**:
- Store API response (signature, eventId, tokenURI)
- Prepare transaction parameters
- Handle wallet connection state

### View 5: Processing/Loading

**Purpose**: Communicate transaction status during on-chain execution

**UI Elements**:
- **Loading Animation**: Custom spinner or progress indicator
- **Status Text**: "Waiting for wallet confirmation..."
- **Sub-text**: "Minimal L2 Fee Required"
- **Transaction Hash**: Display tx hash (clickable to explorer)
- **Cancel Option**: "Cancel Transaction" (if possible)

**User Flow**:
1. Show loading state immediately
2. Display transaction hash when available
3. Monitor transaction status
4. On success: Navigate to "Basic Merch View"
5. On failure: Show error and retry options

**Status Updates**:
- "Preparing transaction..."
- "Waiting for wallet confirmation..."
- "Transaction submitted..."
- "Confirming on-chain..."
- "Success! Minting complete."

### View 6: Basic Merch View (SBT)

**Purpose**: Display claimed SBT and encourage premium upgrade

**UI Elements**:
- **NFT Display**: Large image of the Basic Merch SBT
- **Token Information**: Name, description, attributes
- **POA Badge**: "Proof of Attendance" indicator
- **Upgrade Button**: "Upgrade to Premium (0.001 ETH)"
- **Share Options**: Social sharing buttons
- **EAS Link**: "View Attestation" link to EAS explorer

**User Flow**:
1. Display SBT with full metadata
2. Show upgrade option prominently
3. User clicks "Upgrade to Premium"
4. Navigate to "Premium Upgrade View"
5. Maintain SBT visibility even after upgrade

**Key Design Principles**:
- **SBT Always Visible**: Even after premium upgrade, SBT remains prominent
- **Clear Value Proposition**: Explain benefits of premium upgrade
- **Social Proof**: Show attendance verification status

### View 7: Premium Upgrade View

**Purpose**: Facilitate premium NFT purchase and minting

**UI Elements**:
- **Premium Preview**: Premium NFT image and details
- **Pricing Display**: Clear fee breakdown
- **Payment Button**: "Pay & Mint Premium (0.001 ETH)"
- **Benefits List**: Premium features and collectible value
- **Back Button**: Return to Basic Merch view

**User Flow**:
1. Display premium NFT preview
2. Show pricing and payment details
3. User confirms payment
4. Execute premium minting transaction
5. Show success and return to updated Basic Merch view

**Payment Flow**:
1. User clicks "Pay & Mint Premium"
2. Wallet prompts for payment confirmation
3. Transaction executes (mints ERC-721, retains SBT)
4. Backend issues updated EAS attestation
5. Display success with both tokens

## 4. Implementation Responsibilities (Frontend Engineer)

### Multi-Step Flow Implementation
- **Image Upload Integration**: Implement `/api/createMerch` and `/api/createEvent` endpoint calls
- **File Handling**: Drag-and-drop, file validation, and image preview functionality
- **API Integration**: Implement `/verify-code` endpoint calls
- **Signature Handling**: Store and use backend signatures for contract calls
- **Transaction Management**: Handle wallet transactions with proper error handling
- **State Persistence**: Maintain user progress across app sessions

### MiniKit Integration
- **Wallet Connection**: Seamless Base App wallet integration
- **Transaction Initiation**: Direct contract calls with user signatures
- **Gas Estimation**: Display accurate gas fee estimates
- **Transaction Monitoring**: Real-time status updates

### Contract Interaction
```typescript
// Example implementation for Basic Merch
async function mintBasicMerch(signature: string, eventId: string, imageURL: string) {
    const contract = new ethers.Contract(MERCH_MANAGER_ADDRESS, ABI, signer);
    
    const tx = await contract.mintSBTWithAttestation(
        imageURL,
        userAddress,
        eventId,
        signature,
        { value: 0 } // User pays gas only
    );
    
    return await tx.wait();
}

// Example implementation for Premium Merch
async function mintPremiumMerch(imageURL: string, attestationId: string, eventId: string) {
    const contract = new ethers.Contract(MERCH_MANAGER_ADDRESS, ABI, signer);
    
    const tx = await contract.mintCompanionWithAttestation(
        imageURL,
        userAddress,
        attestationId,
        eventId,
        { value: ethers.utils.parseEther("0.001") } // Premium fee
    );
    
    return await tx.wait();
}
```

## 5. Responsive Design Requirements

### Mobile-First Approach
- **Screen Sizes**: Optimized for mobile devices (320px - 768px)
- **Touch Targets**: Minimum 44px touch targets for buttons
- **Navigation**: Thumb-friendly navigation patterns
- **Loading States**: Optimized for mobile network conditions

### Base App Integration
- **MiniKit Compatibility**: Full integration with Base App MiniKit
- **Wallet Integration**: Seamless wallet connection and transaction handling
- **Deep Linking**: Support for Base App deep linking
- **Performance**: Optimized for mobile performance

## 6. Accessibility Requirements

### WCAG Compliance
- **Color Contrast**: Minimum 4.5:1 contrast ratio
- **Text Scaling**: Support for 200% text scaling
- **Screen Readers**: Proper ARIA labels and semantic HTML
- **Keyboard Navigation**: Full keyboard accessibility

### User Experience
- **Clear Language**: Simple, jargon-free interface text
- **Visual Hierarchy**: Clear information hierarchy
- **Error Messages**: Helpful, actionable error messages
- **Loading States**: Clear progress indication

## 7. Branding and Visual Design

### Design System
- **Color Palette**: Consistent with Base branding
- **Typography**: Clear, readable font choices
- **Iconography**: Consistent icon system
- **Spacing**: Consistent spacing and layout grid

### Merch-Specific Elements
- **NFT Previews**: High-quality token image display
- **Status Indicators**: Clear POA and premium status
- **Transaction States**: Visual feedback for all states
- **Success Celebrations**: Engaging success animations

## 8. Performance Requirements

### Loading Performance
- **Initial Load**: < 3 seconds on 3G
- **API Calls**: < 1 second response time
- **Image Loading**: Optimized NFT image delivery
- **Bundle Size**: Minimized JavaScript bundle

### User Experience
- **Smooth Animations**: 60fps animations
- **Responsive Interactions**: < 100ms interaction response
- **Offline Handling**: Graceful offline state management
- **Error Recovery**: Robust error handling and recovery

This specification ensures a user-friendly, accessible, and performant Mini-App that seamlessly integrates with Base App while providing clear value proposition for the Merch MVP's dual-asset model.
