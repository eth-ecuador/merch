# **Project: Merch MVP Frontend/UX Specification**

This document serves as the **design blueprint** for the Mini-App interface, to be created by the **UX/UI Designer** and implemented by the **Frontend Engineer**.

## **1\. Design & Aesthetic Goals (UX/UI Designer)**

* **Aesthetic:** Modern, clean, and mobile-first, adhering strictly to the defined **Branding Kit**.  
* **Messaging Focus:** Clearly communicate the steps: **1\. Validate Code (Backend)** $\\rightarrow$ **2\. Mint Now (User Pays Gas)**.

## **2\. Required Views and Interaction States (UX/UI Designer Tasks)**

| View | Primary Goal | Key UI Elements & Design Focus |
| :---- | :---- | :---- |
| **1\. Claim Input** | Maximize conversion rate for code entry. | Single clean input field for the claim code. **Primary Option:** "Check Code & Mint" (Initial button, no transaction). **Secondary Option:** "Reserve Off-Chain (Free Now)." |
| **2\. Code Verified/Mint Ready** | Prompt user to finalize the on-chain step. | **State:** Code is validated, Backend API returns success and signature. **Primary Button:** "Finalize Mint (Pay Minimal Gas Fee)." |
| **3\. Processing/Loading** | Communicate user-paid transaction status clearly. | Custom loading indicator. Clear status text: "Waiting for wallet confirmation... Minimal L2 Fee Required." |
| **4\. Basic Merch View (SBT)** | Showcase the claimed SBT and encourage the paid upgrade. | **Crucially:** Always visible as the POA, even if Premium is owned. |

## **3\. Implementation Responsibilities (Frontend Engineer)**

* **Multi-Step Flow:** Implement the two-step minting process: API call to /verify-code followed by a **standard wallet transaction** using the returned signature.  
* Ensure the minting transaction calls the **public** BasicMerch.mintSBT(..., signature) function and prompts the user's wallet for the gas fee.

The remaining five documents (merch\_mvp\_plan.md, architecture\_overview.md, deployment\_guide.md, testing\_strategy.md, and branding\_kit.md) require only minor contextual language updates (e.g., changing "restricted mint" to "signature mint") but the core structure remains the same as the public call mechanic is handled via the new signature-based **Smart Contract Specification** and **Backend Specification**. These two documents were the ones critically affected by the change.