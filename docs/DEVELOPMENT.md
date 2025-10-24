# Development Guide

This guide provides comprehensive instructions for setting up, testing, and deploying the Merch MVP across all three repositories.

## 1. Prerequisites

### Required Software
- **Node.js**: v18+ (LTS recommended)
- **Git**: Latest version
- **Docker**: v20+ (for containerized development)
- **Base Wallet**: For testing transactions
- **Base Sepolia ETH**: For gas fees during testing

### Required Accounts
- **Base Sepolia Faucet**: [Get test ETH](https://bridge.base.org/deposit)
- **Alchemy/Infura**: Base Sepolia RPC endpoint
- **Pinata**: IPFS storage service for images and metadata
- **PostgreSQL**: Database instance (local or cloud)

## 2. Repository Setup

### Clone All Repositories
```bash
# Clone the three implementation repositories
git clone https://github.com/eth-ecuador/merch-contracts.git
git clone https://github.com/eth-ecuador/merch-backend.git
git clone https://github.com/eth-ecuador/merch-miniapp.git

# Clone this documentation repository
git clone https://github.com/eth-ecuador/merch.git
```

### Directory Structure
```
merch/
├── docs/                    # This documentation
├── context/                 # Original specifications
├── merch-contracts/         # Smart contracts
├── merch-backend/          # API service
└── merch-miniapp/          # Frontend Mini-App
```

## 3. Smart Contracts Setup (merch-contracts)

### Prerequisites
```bash
# Install Foundry
curl -L https://foundry.paradigm.xyz | bash
foundryup

# Verify installation
forge --version
```

### Environment Setup
```bash
cd merch-contracts

# Copy environment template
cp .env.example .env

# Edit .env with your configuration
BASE_SEPOLIA_RPC_URL=https://sepolia.base.org
PRIVATE_KEY=your_private_key_here
ETHERSCAN_API_KEY=your_etherscan_key
```

### Development Commands
```bash
# Install dependencies
forge install

# Compile contracts
forge build

# Run tests
forge test

# Run tests with gas reporting
forge test --gas-report

# Deploy to Base Sepolia
forge script script/Deploy.s.sol --rpc-url $BASE_SEPOLIA_RPC_URL --broadcast --verify
```

### Contract Deployment
```bash
# Deploy MerchManager contract
forge script script/DeployMerchManager.s.sol --rpc-url $BASE_SEPOLIA_RPC_URL --broadcast

# Verify contract on BaseScan
forge verify-contract <MERCH_MANAGER_ADDRESS> MerchManager --etherscan-api-key $ETHERSCAN_API_KEY --chain base-sepolia
```

## 4. Backend Setup (merch-backend)

### Prerequisites
```bash
# Install Node.js dependencies
npm install

# Install PostgreSQL (if not using Docker)
# Ubuntu/Debian
sudo apt-get install postgresql postgresql-contrib

# macOS
brew install postgresql
```

### Environment Configuration
```bash
cd merch-backend

# Copy environment template
cp .env.example .env

# Edit .env with your configuration
DATABASE_URL=postgresql://user:password@localhost:5432/merch_db
BASE_SEPOLIA_RPC_URL=https://sepolia.base.org
BACKEND_ISSUER_PRIVATE_KEY=0x...
EAS_CONTRACT_ADDRESS=0x...
EAS_SCHEMA_UID=0x...
API_KEY_SECRET=your-secure-api-key
PINATA_API_KEY=your-pinata-api-key
PINATA_SECRET_KEY=your-pinata-secret-key
IPFS_GATEWAY_URL=https://ipfs.io/ipfs/
MAX_IMAGE_SIZE=10485760
ALLOWED_IMAGE_TYPES=image/jpeg,image/png,image/gif,image/webp
```

### Database Setup
```bash
# Create database
createdb merch_db

# Run migrations
npm run migrate

# Seed test data
npm run seed
```

### Development Commands
```bash
# Start development server
npm run dev

# Run tests
npm test

# Run tests with coverage
npm run test:coverage

# Build for production
npm run build

# Start production server
npm start
```

### API Testing
```bash
# Test merch creation endpoint
curl -X POST http://localhost:3000/api/createMerch \
  -H "X-API-KEY: your-api-key" \
  -F "image=@/path/to/image.jpg" \
  -F "description=Test merch item"

# Test event creation endpoint
curl -X POST http://localhost:3000/api/createEvent \
  -H "X-API-KEY: your-api-key" \
  -F "eventImage=@/path/to/event.jpg" \
  -F "eventData={\"name\":\"Test Event\",\"description\":\"Test Description\"}" \
  -F "claimCodeCount=5"

# Test verify-code endpoint
curl -X POST http://localhost:3000/verify-code \
  -H "Content-Type: application/json" \
  -H "X-API-KEY: your-api-key" \
  -d '{"code": "TEST123", "walletAddress": "0x..."}'

# Test off-chain reservation
curl -X POST http://localhost:3000/claim-offchain \
  -H "Content-Type: application/json" \
  -H "X-API-KEY: your-api-key" \
  -d '{"code": "TEST123", "userIdentifier": "user@example.com", "type": "email"}'
```

## 5. Frontend Setup (merch-miniapp)

### Prerequisites
```bash
# Install Node.js dependencies
npm install

# Install MiniKit dependencies
npm install @base/minkit
```

### Environment Configuration
```bash
cd merch-miniapp

# Copy environment template
cp .env.example .env.local

# Edit .env.local with your configuration
NEXT_PUBLIC_API_URL=http://localhost:3000
NEXT_PUBLIC_MERCH_MANAGER_ADDRESS=0x...
NEXT_PUBLIC_BASE_SEPOLIA_RPC_URL=https://sepolia.base.org
NEXT_PUBLIC_IPFS_GATEWAY_URL=https://ipfs.io/ipfs/
```

### Development Commands
```bash
# Start development server
npm run dev

# Build for production
npm run build

# Start production server
npm start

# Run tests
npm test

# Run linting
npm run lint

# Run type checking
npm run type-check
```

### MiniKit Integration
```typescript
// Example MiniKit integration
import { MiniKit } from '@base/minkit';

const minkit = new MiniKit({
  apiUrl: process.env.NEXT_PUBLIC_API_URL,
  contractAddresses: {
    merchManager: process.env.NEXT_PUBLIC_MERCH_MANAGER_ADDRESS
  }
});
```

## 6. Local Development Workflow

### 1. Start All Services
```bash
# Terminal 1: Start database
docker-compose up -d postgres

# Terminal 2: Start backend
cd merch-backend && npm run dev

# Terminal 3: Start frontend
cd merch-miniapp && npm run dev

# Terminal 4: Deploy contracts (one-time)
cd merch-contracts && forge script script/DeployMerchManager.s.sol --rpc-url $BASE_SEPOLIA_RPC_URL --broadcast
```

### 2. Test Complete Flow
1. **Access Mini-App**: Navigate to `http://localhost:3001`
2. **Creator Upload**: Test image upload functionality
3. **Event Creation**: Test event creation with claim codes
4. **Enter Test Code**: Use generated claim codes
5. **Connect Wallet**: Use Base App or MetaMask with Base Sepolia
6. **Mint SBT**: Complete basic claim transaction
7. **Upgrade Premium**: Test premium upgrade flow
8. **Verify Attestation**: Check EAS attestation creation

### 3. Debug Common Issues
```bash
# Check contract deployment
forge verify-contract <ADDRESS> <CONTRACT> --etherscan-api-key $ETHERSCAN_API_KEY

# Check database connection
psql $DATABASE_URL -c "SELECT * FROM claim_codes LIMIT 5;"

# Check API health
curl http://localhost:3000/health

# Check frontend build
npm run build && npm start
```

## 7. Testing Strategy

### Unit Testing
```bash
# Smart contracts
cd merch-contracts
forge test --match-test testBasicMerch
forge test --match-test testPremiumMerch

# Backend API
cd merch-backend
npm test -- --testNamePattern="verify-code"
npm test -- --testNamePattern="attest-claim"

# Frontend components
cd merch-miniapp
npm test -- --testNamePattern="ClaimInput"
npm test -- --testNamePattern="MintFlow"
```

### Integration Testing
```bash
# End-to-end flow testing
npm run test:e2e

# Cross-repository testing
npm run test:integration

# Performance testing
npm run test:load
```

### Security Testing
```bash
# Contract security
forge test --match-test testSecurity

# API security
npm run test:security

# Frontend security
npm run test:security
```

## 8. Deployment Guide

### Base Sepolia Deployment

#### 1. Contract Deployment
```bash
cd merch-contracts

# Deploy with verification
forge script script/Deploy.s.sol \
  --rpc-url $BASE_SEPOLIA_RPC_URL \
  --broadcast \
  --verify \
  --etherscan-api-key $ETHERSCAN_API_KEY
```

#### 2. Backend Deployment
```bash
cd merch-backend

# Build Docker image
docker build -t merch-backend .

# Deploy to cloud provider
# Example: Render, Railway, or AWS
```

#### 3. Frontend Deployment
```bash
cd merch-miniapp

# Build for production
npm run build

# Deploy to Vercel, Netlify, or similar
# Configure environment variables
```

### Production Configuration

#### Environment Variables
```bash
# Production .env
NODE_ENV=production
DATABASE_URL=postgresql://user:pass@prod-db:5432/merch_db
BASE_SEPOLIA_RPC_URL=https://sepolia.base.org
BACKEND_ISSUER_PRIVATE_KEY=0x...
EAS_CONTRACT_ADDRESS=0x...
API_KEY_SECRET=production-secure-key
```

#### Database Migration
```bash
# Run production migrations
npm run migrate:prod

# Seed production data
npm run seed:prod
```

## 9. Monitoring and Maintenance

### Health Checks
```bash
# API health
curl https://api.merch.eth-ecuador.org/health

# Database health
psql $DATABASE_URL -c "SELECT 1;"

# Contract health
cast call $BASIC_MERCH_ADDRESS "totalSupply()" --rpc-url $BASE_SEPOLIA_RPC_URL
```

### Logging
```bash
# Backend logs
docker logs merch-backend

# Database logs
docker logs merch-postgres

# Contract events
cast logs --from-block 0 --address $BASIC_MERCH_ADDRESS
```

### Performance Monitoring
```bash
# API response times
curl -w "@curl-format.txt" -o /dev/null -s https://api.merch.eth-ecuador.org/verify-code

# Database performance
psql $DATABASE_URL -c "SELECT * FROM pg_stat_activity;"

# Contract gas usage
cast call $BASIC_MERCH_ADDRESS "gasUsed()" --rpc-url $BASE_SEPOLIA_RPC_URL
```

## 10. Troubleshooting

### Common Issues

#### Contract Issues
```bash
# Transaction failed
cast tx <TX_HASH> --rpc-url $BASE_SEPOLIA_RPC_URL

# Signature verification failed
cast call $BASIC_MERCH_ADDRESS "verifySignature(bytes)" <SIGNATURE> --rpc-url $BASE_SEPOLIA_RPC_URL
```

#### Backend Issues
```bash
# API not responding
curl -v http://localhost:3000/health

# Database connection failed
psql $DATABASE_URL -c "SELECT version();"

# Signature generation failed
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

#### Frontend Issues
```bash
# Build failed
npm run build 2>&1 | grep -i error

# MiniKit connection failed
# Check Base App integration
# Verify contract addresses
# Check RPC endpoint
```

### Debug Commands
```bash
# Check all services
docker-compose ps

# View logs
docker-compose logs -f

# Restart services
docker-compose restart

# Clean rebuild
docker-compose down && docker-compose up --build
```

## 11. Contributing

### Development Workflow
1. **Fork repository** and create feature branch
2. **Follow coding standards** (ESLint, Prettier, Solidity style)
3. **Write tests** for new functionality
4. **Update documentation** as needed
5. **Submit pull request** with clear description

### Code Standards
```bash
# Solidity formatting
forge fmt

# TypeScript/JavaScript formatting
npm run format

# Linting
npm run lint

# Type checking
npm run type-check
```

This development guide ensures smooth setup, testing, and deployment of the Merch MVP across all three repositories while maintaining high code quality and security standards.
