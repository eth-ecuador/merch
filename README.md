# Merch Project

A Base-native Proof of Attendance protocol that uses Digital Merch NFTs, demonstrating a **low-cost, friction-minimized user experience** and validating the **freemium monetization model** on the **Base Sepolia Testnet**.

## 🎯 Project Overview

Merch is a revolutionary Proof of Attendance Protocol (POAP) built specifically for Base L2, featuring:

- **Creator-Driven Content**: Creators upload merch images to IPFS for community use
- **Event Management**: Users create events with claim codes for attendance tracking
- **Dual Token Model**: Soulbound Tokens (SBT) for permanent attendance proof + ERC-721 NFTs for collectible value
- **Low-Cost UX**: Minimal L2 gas fees with off-chain reservation options
- **Signature-Based Security**: Public minting with backend signature verification
- **EAS Integration**: Immutable attestation records for verifiable attendance
- **Freemium Monetization**: Free basic claims with premium upgrade options

## 🏗️ Repository Structure

This project is organized across three specialized repositories:

| Repository | Purpose | Technology |
|------------|---------|------------|
| [**merch-contracts**](https://github.com/eth-ecuador/merch-contracts) | Smart contract implementation | Solidity, Foundry |
| [**merch-backend**](https://github.com/eth-ecuador/merch-backend) | API/Backend service | Node.js, TypeScript |
| [**merch-miniapp**](https://github.com/eth-ecuador/merch-miniapp) | Frontend Mini-App | React, MiniKit |

## 🚀 Quick Start

### For Developers

1. **Clone all repositories**:
   ```bash
   git clone https://github.com/eth-ecuador/merch-contracts.git
   git clone https://github.com/eth-ecuador/merch-backend.git
   git clone https://github.com/eth-ecuador/merch-miniapp.git
   ```

2. **Follow setup guides** in each repository's README

3. **Deploy to Base Sepolia** following the [Development Guide](docs/DEVELOPMENT.md)

### For Users

- Access the Mini-App through Base App
- Enter your claim code to mint Basic Merch (SBT)
- Upgrade to Premium Merch (ERC-721) for collectible value

## 📋 Key Features

- ✅ **Creator Tools**: Upload and manage merch images on IPFS
- ✅ **Event Creation**: Create events with claim codes and images
- ✅ **Off-Chain Reservation**: Gasless entry for new users
- ✅ **Low-Cost Claiming**: Minimal L2 gas fees for immediate minting
- ✅ **Dual Asset Model**: SBT (permanent POA) + ERC-721 (tradable)
- ✅ **EAS Attestations**: Verifiable, immutable attendance records
- ✅ **Premium Upgrades**: Freemium monetization model
- ✅ **Base Native**: Optimized for Base L2 infrastructure

## 📚 Documentation

- [**Architecture Overview**](docs/ARCHITECTURE.md) - System design and component interactions
- [**Smart Contract Specification**](docs/SMART_CONTRACTS.md) - Contract design and functions
- [**API Specification**](docs/API_SPECIFICATION.md) - Backend endpoints and integration
- [**Frontend/UX Guide**](docs/FRONTEND_UX.md) - UI/UX design and implementation
- [**Project Scope**](docs/Project_SCOPE.md) - Project goals and success criteria
- [**Development Guide**](docs/DEVELOPMENT.md) - Setup, testing, and deployment

## 🤝 Contributing

1. Review the [Project Scope](docs/Project_SCOPE.md) to understand project boundaries
2. Check the [Development Guide](docs/DEVELOPMENT.md) for setup instructions
3. Follow the architecture patterns outlined in [Architecture Overview](docs/ARCHITECTURE.md)
4. Submit PRs to the appropriate repository (contracts, backend, or miniapp)

## 📄 License

This project is part of the ETH Ecuador initiative. See individual repositories for specific licensing terms.

---

**Built for Base. Built for the future of Proof of Attendance.**
