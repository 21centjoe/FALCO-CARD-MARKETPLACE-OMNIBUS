# FALCO On-Chain — starter kit

Plain code, meant to run on a page **you** host (Vercel, Netlify, your own server, or just `python3 -m http.server` while you test). It will **not** run inside a Claude-hosted artifact page — that sandbox blocks the network calls this needs (a wallet bridge aside, arbitrary RPC/API calls are not allowed there).

This is unaudited scaffolding, not production security. Test everything on a testnet (Sepolia) with fake ETH before it ever touches real funds, and get a real audit before you let strangers put money through it.

## Layout
```text
contracts/
  FalcoCard.sol     — ERC-721 card (power, deckType, mint, train)
  FalcoMarket.sol    — escrow-style list/buy/cancel marketplace
scripts/
  deploy.js          — Hardhat deploy script for both contracts
hardhat.config.js
web/
  config.js          — paste your deployed addresses here
  wallet.js          — connect MetaMask / injected wallet
  mint.js            — mint a card (owner-only)
  read.js            — read a card's on-chain stats/owner
  market.js          — list / buy / cancel
  price.js           — live ETH/USD price (CoinGecko, no key needed)
```

## Setup
```bash
npm install --save-dev hardhat @nomicfoundation/hardhat-toolbox
npm install @openzeppelin/contracts
npx hardhat init            # choose "create a JavaScript project" if prompted
# copy contracts/, scripts/, hardhat.config.js into the project hardhat creates
```

Get a free Sepolia RPC URL (Alchemy or Infura) and a small amount of test ETH from a Sepolia faucet, then:

```bash
export SEPOLIA_RPC_URL="https://..."
export PRIVATE_KEY="0x..."          # a throwaway test wallet's key — never a real one
npx hardhat run scripts/deploy.js --network sepolia
```

Paste the two printed addresses into `web/config.js`.

## Using it from a page
```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/ethers/6.13.1/ethers.umd.min.js"></script>
<script src="config.js"></script>
<script src="wallet.js"></script>
<script src="mint.js"></script>
<script src="read.js"></script>
<script src="market.js"></script>
<script src="price.js"></script>

<button onclick="connectWallet()">Connect Wallet</button>
<span id="walletAddr"></span>
```

Then call `mintCard(signer, ...)`, `readCard(provider, tokenId)`, `listCard(signer, tokenId, priceEth)`, `buyCard(signer, tokenId)` as needed — each function is a few lines, documented inline in its file.

## What this deliberately leaves out
- Real trick-taking rules for Euchre/Tarot — the Vault's Power stat is a stand-in.
- Any server-side validation of `train()` cooldowns, rate limits, or pricing.
- Metadata hosting — you'll want IPFS (e.g. via nft.storage or Pinata) for `tokenURI` in production rather than a URL you control.
- Legal/regulatory review — tokenizing collectibles for value has real jurisdiction-dependent implications; this is code, not legal advice.