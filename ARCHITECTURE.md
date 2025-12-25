# 🏗️ Architecture Documentation

## System Architecture Overview

The Cosmic Vibe Generator is built as a modern, serverless Next.js application with a focus on performance, scalability, and user experience.

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                             │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    Browser / Mobile                       │   │
│  │  • Web3 Wallet (MetaMask, Coinbase Wallet, etc.)        │   │
│  │  • React 19 + Next.js 15.3.8                            │   │
│  │  • Wagmi + Viem (Ethereum interactions)                 │   │
│  │  • Three.js (3D rendering)                              │   │
│  └──────────────────────────────────────────────────────────┘   │
└────────────┬────────────────────────────────────────────────────┘
             │
             │ HTTPS / WebSocket
             │
┌────────────▼────────────────────────────────────────────────────┐
│                      APPLICATION LAYER                           │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                  Next.js App Router                       │   │
│  │  • Server Components (RSC)                               │   │
│  │  • Client Components (Hydration)                         │   │
│  │  • API Routes (Serverless Functions)                     │   │
│  │  • Middleware (Auth, CORS, etc.)                         │   │
│  └──────────────────────────────────────────────────────────┘   │
└────────────┬────────────────────────────────────────────────────┘
             │
             │ Internal APIs
             │
┌────────────▼────────────────────────────────────────────────────┐
│                        SERVICE LAYER                             │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ /api/generate-vibe   │ /api/ipfs-upload │ /api/proxy    │   │
│  │ (Claude AI)           │ (Pinata IPFS)     │ (External)   │   │
│  └──────────────────────────────────────────────────────────┘   │
└────────────┬───────────┬─────────────┬──────────────────────────┘
             │           │             │
             │           │             │
┌────────────▼───────────▼─────────────▼──────────────────────────┐
│                       EXTERNAL SERVICES                          │
│  ┌──────────────┬──────────────┬──────────────┬─────────────┐   │
│  │ Anthropic    │ Pinata       │ Base Network │ CoinGecko   │   │
│  │ Claude API   │ IPFS Gateway │ RPC          │ Prices      │   │
│  └──────────────┴──────────────┴──────────────┴─────────────┘   │
└──────────────────────────────────────────────────────────────────┘
```

---

## Component Architecture

### Frontend Components Hierarchy

```
App Layout (providers.tsx)
├── WagmiProvider
│   ├── QueryClientProvider
│   │   └── OnchainKitProvider
│   │       └── FarcasterWrapper
│   │           ├── FarcasterManifestSigner
│   │           └── FarcasterToastManager
│   └── Page (page.tsx)
│       ├── WalletInfo
│       │   ├── AccountInfo
│       │   ├── ConnectButton
│       │   └── DisconnectButton
│       ├── CosmicVisual (Three.js)
│       │   ├── Scene
│       │   ├── ParticleSystem
│       │   ├── Camera
│       │   └── Lights
│       ├── CosmicReading
│       │   ├── VibeAttributes
│       │   ├── AIPrediction
│       │   └── CosmicNumber
│       ├── RealTokenPrices
│       │   ├── PriceCard (ETH, USDC, etc.)
│       │   └── PriceSource Indicator
│       ├── EnhancedAnalyticsCharts
│       │   ├── AreaChart (7-day trend)
│       │   ├── BarChart (daily breakdown)
│       │   ├── PieChart (distribution)
│       │   └── LineChart (cumulative)
│       ├── MetricsDashboard
│       │   ├── OverviewTab
│       │   ├── EngagementTab
│       │   └── OnchainTab
│       ├── AchievementSystem
│       │   ├── BadgeGrid
│       │   ├── ProgressBars
│       │   └── UnlockNotifications
│       ├── ShareableVibeCard
│       │   ├── CardPreview
│       │   ├── ExportButton
│       │   └── SocialShareButtons
│       ├── VibeMemes (Tenor API)
│       │   ├── MemeGrid
│       │   └── InfiniteScroll
│       ├── NFTMinter
│       │   ├── MintForm
│       │   ├── IPFSUpload
│       │   └── MintButton
│       ├── CosmicTokenLaunch (Memebase)
│       │   ├── TokenForm
│       │   └── DeployButton
│       ├── Leaderboard
│       │   └── UserRankings
│       └── GuidedTour
│           ├── TourStep
│           ├── Spotlight
│           └── ProgressDots
```

---

## Data Flow

### 1. User Authentication Flow

```
User Visits App
    ↓
Next.js Layout Loads
    ↓
Providers Initialize (WagmiProvider, QueryClient, OnchainKit)
    ↓
Check Wallet Connection Status
    ↓
    ├─→ Connected?
    │   ├─→ Yes: Fetch onchain data
    │   │   ├─→ Transaction history (Base RPC)
    │   │   ├─→ Token balances (OnchainKit)
    │   │   ├─→ Gas analytics (viem)
    │   │   └─→ Render dashboard
    │   └─→ No: Show Connect Wallet UI
    │       ├─→ User clicks "Connect"
    │       ├─→ Wallet modal opens
    │       ├─→ User approves connection
    │       ├─→ SIWE signature request
    │       ├─→ Signature verified
    │       └─→ Fetch onchain data
    └─→ Store connection state (wagmi)
```

### 2. Cosmic Vibe Generation Flow

```
User Clicks "Generate Vibe"
    ↓
Collect Wallet Data
    ├─→ Address
    ├─→ Transaction count
    ├─→ Last activity timestamp
    ├─→ Wallet age (days)
    ├─→ Balance (ETH)
    └─→ Unique contract interactions
    ↓
Call /api/generate-vibe
    ↓
API Route Handler
    ├─→ Validate request body
    ├─→ Create Claude AI prompt
    ├─→ Call Anthropic API (30s timeout)
    ├─→ Parse JSON response
    ├─→ Extract vibe attributes
    └─→ Return formatted data
    ↓
    ├─→ Success?
    │   ├─→ Yes: Display cosmic reading
    │   │   ├─→ Update UI with attributes
    │   │   ├─→ Animate 3D visual
    │   │   ├─→ Show AI prediction
    │   │   ├─→ Suggest memes (Tenor API)
    │   │   ├─→ Track metrics (localStorage)
    │   │   └─→ Check achievements
    │   └─→ No: Fallback to deterministic generation
    │       ├─→ Use hash-based algorithm
    │       ├─→ Generate pseudo-random attributes
    │       └─→ Display reading (no AI badge)
    ↓
Store in Component State
    ↓
Update Metrics Dashboard
    ↓
Unlock Achievements (if eligible)
```

### 3. NFT Minting Flow

```
User Clicks "Mint NFT"
    ↓
Prepare NFT Metadata
    ├─→ Name: "Cosmic Vibe #{number}"
    ├─→ Description: AI prediction
    ├─→ Attributes: Energy, Luck, Chaos, etc.
    └─→ Image: Placeholder or generated
    ↓
Call /api/ipfs-upload
    ↓
API Route Handler
    ├─→ Validate metadata
    ├─→ Format as JSON
    ├─→ Call Pinata API
    ├─→ Upload to IPFS
    └─→ Return IPFS hash + URLs
    ↓
Construct Mint Transaction
    ├─→ Contract address (Base NFT)
    ├─→ Token URI (IPFS URL)
    ├─→ Gas estimation
    └─→ Transaction data
    ↓
Send Transaction (wagmi)
    ├─→ User approves in wallet
    ├─→ Transaction submitted
    ├─→ Wait for confirmation
    └─→ Transaction hash received
    ↓
Display Success
    ├─→ Show BaseScan link
    ├─→ Show IPFS metadata link
    ├─→ Update metrics (NFTs minted)
    └─→ Check achievements
```

### 4. Token Price Fetching Flow

```
Component Mounts (RealTokenPrices)
    ↓
Check isFetching Flag
    ├─→ Already fetching? Exit
    └─→ Not fetching? Continue
    ↓
Set isFetching = true
    ↓
Primary: Try OnchainKit API
    ├─→ Fetch prices for: ETH, USDC, DEGEN, BRETT, TOSHI
    ├─→ Wait up to 20s
    ├─→ Success?
    │   ├─→ Yes: Parse and display prices
    │   │   └─→ Set source = "OnchainKit"
    │   └─→ No / Timeout: Fallback to CoinGecko
    │       ├─→ Fetch from CoinGecko API
    │       ├─→ Wait up to 15s
    │       ├─→ Success?
    │       │   ├─→ Yes: Parse and display prices
    │       │   │   └─→ Set source = "CoinGecko (fallback)"
    │       │   └─→ No: Show error message
    │       │       └─→ "Network slow. Click refresh."
    │       └─→ Set isFetching = false
    └─→ Display Prices
        ├─→ Price value
        ├─→ Change percentage
        ├─→ Source indicator
        └─→ Refresh button
```

---

## State Management

### Client-Side State

#### 1. **Component State (useState)**
- **Purpose**: Local UI state
- **Examples**:
  - Loading states
  - Form inputs
  - Modal visibility
  - Expanded/collapsed sections
- **Scope**: Single component

#### 2. **wagmi State (useAccount, useBalance, etc.)**
- **Purpose**: Web3 wallet state
- **Examples**:
  - Connected address
  - Network chain ID
  - Account balance
  - Connection status
- **Scope**: All components (via React Context)

#### 3. **localStorage State**
- **Purpose**: Persistent client-side data
- **Examples**:
  - Vibe generation count
  - NFTs minted count
  - Tokens launched count
  - Achievements unlocked
  - Tour completion status
  - Last visit date
  - Total time spent
- **Scope**: Cross-session persistence
- **Implementation**:
  ```typescript
  // Read
  const data = localStorage.getItem('cosmic-metrics')
  const metrics = data ? JSON.parse(data) : defaultMetrics
  
  // Write
  localStorage.setItem('cosmic-metrics', JSON.stringify(metrics))
  ```

#### 4. **React Query State (TanStack Query)**
- **Purpose**: Server state caching
- **Examples**:
  - Token prices (cached for 5s)
  - Transaction history (cached for 30s)
  - NFT metadata (cached indefinitely)
- **Scope**: All components (via QueryClientProvider)
- **Configuration**:
  ```typescript
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: {
        refetchOnWindowFocus: false,
        retry: 1,
        staleTime: 5_000, // 5 seconds
      },
    },
  })
  ```

### Server-Side State

#### API Routes (Stateless)
- Each API route is a serverless function
- No shared state between requests
- Short-lived execution (max 10s on Vercel)
- Horizontal scaling (auto-scaled by Vercel)

---

## API Architecture

### API Routes Structure

```
src/app/api/
├── generate-vibe/
│   └── route.ts          # Claude AI integration
├── ipfs-upload/
│   └── route.ts          # Pinata IPFS upload
└── proxy/
    └── route.ts          # External API proxy (CORS)
```

### API Design Principles

1. **Stateless**: No server-side session storage
2. **Secure**: API keys stored in environment variables
3. **Timeout Handling**: All external calls have timeouts
4. **Error Handling**: Try-catch with user-friendly messages
5. **Type Safety**: TypeScript interfaces for all requests/responses
6. **CORS**: Handled by Next.js API routes automatically

### API Route: `/api/generate-vibe`

**Purpose**: Generate AI-powered cosmic reading using Claude

**Flow**:
```
POST Request
    ↓
Validate Request Body (Zod schema)
    ├─→ address: string
    ├─→ transactionCount: number
    ├─→ lastActivity: string (ISO)
    ├─→ walletAge: number (days)
    ├─→ balance: string
    └─→ uniqueContracts: number
    ↓
Construct Claude Prompt
    ├─→ Include wallet stats
    ├─→ Request JSON format
    └─→ Specify attributes (energy, luck, etc.)
    ↓
Call Anthropic API
    ├─→ Model: claude-3-5-haiku-20241022
    ├─→ Max tokens: 1024
    ├─→ Temperature: 0.8
    ├─→ Timeout: 30 seconds
    └─→ API Key from env
    ↓
Parse Response
    ├─→ Extract JSON from content
    ├─→ Validate structure
    └─→ Format attributes
    ↓
Return JSON Response
    ├─→ 200 OK: { success: true, data: {...} }
    └─→ 503 Error: { success: false, fallback: true }
```

**Error Handling**:
- **401**: Invalid API key → return fallback flag
- **429**: Rate limit → return fallback flag
- **Timeout**: AbortController after 30s → return fallback flag
- **Parse error**: JSON extraction failed → return fallback flag

### API Route: `/api/ipfs-upload`

**Purpose**: Upload NFT metadata to IPFS via Pinata

**Flow**:
```
POST Request
    ↓
Validate Request Body
    ├─→ metadata.name: string
    ├─→ metadata.description: string
    ├─→ metadata.attributes: Array<{trait_type, value}>
    └─→ metadata.image: string (optional)
    ↓
Prepare Pinata Upload
    ├─→ Format as JSON
    ├─→ Add Pinata metadata (name, keyvalues)
    └─→ Set content type
    ↓
Call Pinata API
    ├─→ Endpoint: /pinning/pinJSONToIPFS
    ├─→ Headers: Authorization (JWT)
    ├─→ Timeout: 30 seconds
    └─→ API Key from env
    ↓
Parse Response
    ├─→ Extract IPFS hash
    ├─→ Construct URLs (ipfs://, gateway)
    └─→ Validate upload
    ↓
Return JSON Response
    ├─→ 200 OK: { success: true, ipfsHash, ipfsUrl, gatewayUrl }
    └─→ 500 Error: { success: false, error: "..." }
```

**Error Handling**:
- **400**: Invalid metadata → return error
- **401**: Invalid Pinata key → return error
- **Timeout**: AbortController after 30s → return error
- **Network error**: IPFS unavailable → return error

### API Route: `/api/proxy`

**Purpose**: Proxy external API calls to handle CORS

**Flow**:
```
POST Request
    ↓
Validate Request Body
    ├─→ protocol: "https"
    ├─→ origin: string (domain)
    ├─→ path: string (endpoint path)
    ├─→ method: "GET" | "POST" | "PUT" | "DELETE"
    ├─→ headers: Record<string, string>
    └─→ body?: any (optional)
    ↓
Construct Target URL
    ├─→ protocol + "://" + origin + path
    └─→ Validate URL format
    ↓
Make External Request
    ├─→ Method from request
    ├─→ Headers from request + CORS headers
    ├─→ Body from request (if applicable)
    └─→ Timeout: 10 seconds
    ↓
Parse Response
    ├─→ Extract status code
    ├─→ Parse body (JSON or text)
    └─→ Forward headers
    ↓
Return Proxied Response
    ├─→ Status: Same as external API
    ├─→ Headers: Include CORS headers
    └─→ Body: Forwarded from external API
```

**Security**:
- No authentication bypass (API keys still required by client)
- Rate limiting inherited from external services
- No caching (stateless)

---

## 3D Rendering Architecture

### Three.js Scene Structure

```
Canvas (react-three/fiber)
    ├─→ Scene (automatic)
    ├─→ Camera (PerspectiveCamera)
    │   ├─→ Position: [0, 2, 6]
    │   ├─→ FOV: 55°
    │   └─→ Controls: OrbitControls
    ├─→ Lights
    │   ├─→ AmbientLight (intensity: 0.5)
    │   ├─→ DirectionalLight (main sun)
    │   └─→ PointLights (accents)
    ├─→ ParticleSystem
    │   ├─→ 5000 particles
    │   ├─→ BufferGeometry
    │   ├─→ PointsMaterial
    │   │   ├─→ Size: 0.05
    │   │   ├─→ Color: Dynamic (based on vibe)
    │   │   ├─→ Opacity: 0.8
    │   │   └─→ Blending: AdditiveBlending
    │   └─→ Animation (useFrame)
    │       ├─→ Rotation: Slow spin
    │       ├─→ Position: Wave motion
    │       └─→ Color: Pulsing effect
    ├─→ CosmicSphere (center)
    │   ├─→ SphereGeometry (radius: 2)
    │   ├─→ MeshStandardMaterial
    │   │   ├─→ Color: Dynamic (vibe-based)
    │   │   ├─→ Metalness: 0.5
    │   │   ├─→ Roughness: 0.2
    │   │   └─→ Emissive: Glow effect
    │   └─→ Animation
    │       ├─→ Rotation: Slow Y-axis
    │       └─→ Scale: Breathing effect
    └─→ Effects (post-processing)
        ├─→ Bloom (EffectComposer)
        └─→ Vignette (subtle)
```

### Performance Optimizations

1. **Lazy Loading**: 
   - Three.js components loaded with `next/dynamic`
   - Prevents SSR issues
   - Reduces initial bundle size

2. **Instancing**:
   - Particles use InstancedMesh
   - Single draw call for 5000 particles
   - GPU-accelerated

3. **Frame Rate**:
   - Target: 60fps
   - Adaptive quality (lower dpr on slow devices)
   - `dpr={[1, 2]}` limits max DPR

4. **Suspense Boundaries**:
   - Wrap async components
   - Show loading UI during load
   - Prevent render blocking

---

## Security Architecture

### API Key Management

```
Environment Variables (.env.local)
    ↓
Server-Side Only (API Routes)
    ↓
Never Exposed to Client
    ↓
    ├─→ ANTHROPIC_API_KEY (Claude AI)
    ├─→ PINATA_API_KEY (IPFS)
    ├─→ RPC_URL (Base Network)
    └─→ ONCHAINKIT_API_KEY (OnchainKit)
```

### Public Environment Variables

Only these are exposed to the client:
- `NEXT_PUBLIC_SDK_CHAIN_ID`
- `NEXT_PUBLIC_ONCHAINKIT_API_KEY`
- `NEXT_PUBLIC_ONCHAINKIT_PROJECT_ID`
- `NEXT_PUBLIC_WALLETCONNECT_PROJECT_ID`

### CORS Handling

- **Next.js API Routes**: Automatically handle CORS
- **External APIs**: Proxied through `/api/proxy`
- **Headers**:
  ```typescript
  'Access-Control-Allow-Origin': '*',
  'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE',
  'Access-Control-Allow-Headers': 'Content-Type, Authorization'
  ```

### Wallet Security

- **SIWE**: Sign-In with Ethereum for authentication
- **No Private Keys**: Never stored or transmitted
- **Client-Side Signing**: All transactions signed in wallet
- **Nonce Verification**: Prevents replay attacks

---

## Performance Architecture

### Bundle Optimization

```
Main Bundle (page.tsx)
    ├─→ Critical Path: 120 KB (gzipped)
    │   ├─→ React + Next.js
    │   ├─→ Wagmi + Viem
    │   └─→ UI Components
    ├─→ Lazy Loaded: 370 KB (gzipped)
    │   ├─→ Three.js + R3F
    │   ├─→ Recharts
    │   └─→ html2canvas
    └─→ Total First Load: 490 KB
```

### Loading Strategy

1. **Critical First Paint**:
   - Render shell (layout, header)
   - Show wallet connect button
   - Display loading skeletons

2. **Lazy Load Heavy Components**:
   - 3D visualization (Three.js)
   - Charts (Recharts)
   - Export tools (html2canvas)

3. **Prefetch Data**:
   - Token prices (on mount)
   - Transaction history (on connect)
   - Memes (on scroll)

### Caching Strategy

#### Browser Cache
- **Static Assets**: 1 year
- **API Responses**: No cache (dynamic)
- **Images**: 1 week

#### React Query Cache
- **Token Prices**: 5 seconds
- **Transaction History**: 30 seconds
- **NFT Metadata**: Indefinite

#### localStorage Cache
- **User Metrics**: Permanent
- **Achievements**: Permanent
- **Tour Status**: Permanent

---

## Deployment Architecture

### Vercel Deployment

```
GitHub Repository
    ↓
Push to main branch
    ↓
Vercel Build Process
    ├─→ Install dependencies (pnpm)
    ├─→ Run Next.js build
    ├─→ Generate static pages
    ├─→ Bundle API routes (serverless functions)
    └─→ Optimize assets
    ↓
Deploy to Edge Network
    ├─→ Static files → CDN (global)
    ├─→ API routes → Serverless (US East)
    └─→ Preview URL generated
    ↓
Production Domain
    ├─→ SSL certificate (automatic)
    ├─→ Custom domain (optional)
    └─→ Analytics enabled
```

### Serverless Functions

Each API route becomes a serverless function:
- **Runtime**: Node.js 18
- **Region**: US East (default)
- **Max Duration**: 10s (Vercel free tier)
- **Max Memory**: 1024 MB
- **Concurrency**: Auto-scaled

---

## Monitoring & Analytics

### Built-in Metrics

1. **Client-Side**:
   - **localStorage tracking**:
     - Vibe generations
     - NFT mints
     - Token launches
     - Social shares
     - Session duration
   - **Achievement progress**
   - **Feature usage stats**

2. **Server-Side**:
   - **Vercel Analytics** (optional):
     - Request count
     - Response times
     - Error rates
     - Geographic distribution
   - **API logs**:
     - Claude API calls
     - IPFS uploads
     - Proxy requests

### Error Tracking

```typescript
// API Route Error Handling
try {
  const result = await externalAPI()
  return NextResponse.json({ success: true, data: result })
} catch (error) {
  console.error('API Error:', error)
  return NextResponse.json(
    { success: false, error: error.message },
    { status: 500 }
  )
}
```

---

## Scalability Considerations

### Horizontal Scaling
- **Serverless**: Auto-scales with traffic
- **CDN**: Global edge network
- **No State**: Stateless API routes

### Vertical Limits
- **API Timeout**: 10s (Vercel free tier)
- **Payload Size**: 4.5 MB (Vercel limit)
- **Memory**: 1024 MB per function

### Bottlenecks & Solutions

| Bottleneck | Solution |
|-----------|----------|
| Claude AI rate limits | Fallback to deterministic generation |
| IPFS upload slow | Show progress, allow background upload |
| RPC rate limits | Use multiple RPC endpoints, implement caching |
| Large 3D assets | Lazy load, compress textures, use instancing |
| High token price fetch frequency | React Query cache (5s staleTime) |

---

## Future Architecture Improvements

### Planned Enhancements

1. **Database Layer**:
   - PostgreSQL (Supabase or Vercel Postgres)
   - Store user vibes, achievements, rankings
   - Enable social leaderboards

2. **Caching Layer**:
   - Redis (Upstash or Vercel KV)
   - Cache token prices, transaction history
   - Reduce RPC calls

3. **Backend for Frontend (BFF)**:
   - GraphQL API (Apollo or Relay)
   - Unified data fetching
   - Better type safety

4. **Websockets**:
   - Real-time notifications
   - Live leaderboard updates
   - Collaborative features

5. **Mobile App**:
   - React Native
   - Shared codebase with web
   - Native wallet integration

---

## Conclusion

The Cosmic Vibe Generator architecture is designed for:
- **Performance**: Fast loading, smooth animations
- **Scalability**: Serverless, auto-scaling
- **Security**: API keys protected, SIWE authentication
- **Maintainability**: Clean code, TypeScript, modular components
- **User Experience**: Responsive, accessible, delightful

The architecture can easily scale to support millions of users with minimal changes.
