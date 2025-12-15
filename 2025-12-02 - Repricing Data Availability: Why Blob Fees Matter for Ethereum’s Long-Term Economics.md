# Repricing Data Availability: <br/> Why Blob Fees Matter for Ethereum’s Long-Term Economics
> **2025-12-02**

> _Fusaka is Ethereum’s second major network upgrade (hard fork) of the year
> <br/> that went live on December 3, 2025, at mainnet slot 13,164,544 ⛓️_

As Ethereum’s Fusaka upgrade went live, it is widely seen as a major boost to the network’s goal of becoming the primary settlement layer for on-chain finance. One of the key improvements is the introduction of PeerDAS, which enables validators to sample small portions of rollup data rather than downloading entire blobs. This reduces bandwidth requirements, keeps node requirements manageable, and helps the network to increase blob capacity by up to 8x. This approach allows for the system to support more rollup activity while maintaining decentralization.

Fusaka also reshapes Ethereum’s fee approach for handling rollup data by creating pricing that stays more stable during quiet periods. This avoids the sharp drop in blob fees that previously occurred when demand fell, and it gives the network a more reliable economic foundation. Combined with a higher gas limit and several performance and security upgrades, Fusaka creates a smoother and more scalable environment for users and developers. Many analysts would describe this release as a turning point that strengthens Ethereum’s ability to support DeFi growth, tokenized assets, and high-volume rollup ecosystems.

<img width="1024" height="886" alt="image" src="https://github.com/user-attachments/assets/91722679-b31f-44f1-92c1-9e19351e6f0e" />

## Roadmap Context: From the Merge to Fusaka

Fusaka sits in a sequence of major upgrades:

- The Merge (2022) PoW-to-PoS transition improved energy efficiency.
- Shanghai / Shapella (2023) enabled validator withdrawals.
- Dencun (March 2024) introduced blobs and cut L2 fees by ~90–95%.
- Pectra (May 2025) validator UX, including some smaller improvements.
- Fusaka (Dec 2025) will radically scale data availability (PeerDAS/BPO), increase L1 capacity, and prepare Ethereum for very high TPS across L2s.

The naming structure:

- Fusaka is Fulu (consensus-layer upgrade, a star) & Osaka (execution-layer upgrade city).
- It was split off from Pectra, which otherwise carried a very large set of EIPs.
- EOF & Verkle are explicitly not part of Pectra or Fusaka.

Testnets:

- Holešky: upgraded on 1 October (deprecated at Fusaka release).
- Sepolia: upgraded on 14 October.
- Hoodi: upgraded on 28 October.

The Auditing:

- Ran for four weeks.
- Co-sponsored by Gnosis and Lido Finance.
- Hosted on Sherlock.
- No issues severe enough to postpone Fusaka.

**Now, for a deeper understanding of how this all fits together, let’s go over all the EIPs in this upgrade and what they mean for Ethereum:**

## Core: Data Availability & Blobs
The heart of Fusaka is a new DA architecture for L2s.

### EIP-7594: PeerDAS (Peer Data Availability Sampling)
Problem (post-Dencun):

- Each node had to download and store all blob data for a period.
- Increasing the number of blobs would have pushed out home validators due to bandwidth and storage constraints.

PeerDAS solution: Let nodes verify that blob data exists without downloading the entire blob. It works in three stages:

1. Erasure coding
2, Distributed custody
3. Sampling

The results:

- Validators only need to download around 12.5% of the total blob data, cutting bandwidth by about 85%.
- Individual storage requirements decrease, but supernodes with ≥4,096 ETH still store the complete blob data.
- Ethereum can safely raise blob capacity without requiring giant nodes.

For ZKsync:

- ZKsync already saves data by posting state diffs instead of full calldata. PeerDAS makes those big state-diff blobs far easier for validators to handle.
- At high volumes (e.g., ZKsync’s 15,000 TPS Atlas target), data that would otherwise overwhelm L1 becomes manageable.
- PeerDAS also lays the groundwork for Full Danksharding, where blocks can carry 128+ blobs, aligning perfectly with ZKsync’s Elastic Chain vision.

### EIP-7892: Blob-Parameter-Only (BPO) Forks
Traditional capacity bumps need full hard forks. Blob demand from L2s is more dynamic. BPO forks are lightweight “mini-forks” that only adjust blob parameters (e.g., target & max blobs), not core client logic.

Planned blob schedule after Fusaka:

- Fusaka activation (3 Dec 2025)
- BPO1 (9 Dec 2025)
- BPO2 (7 Jan 2026)

Long-term, blob capacity is designed to scale from 6 to 128 blobs per block.

For ZKsync and L2s:

- BPOs enable Ethereum to grow DA supply as L2 demand rises proactively, rather than react slowly.
- They help avoid fee shocks and provide institutional chains (e.g., Prividium-style ZKsync deployments for banks) with predictable L1 DA capacity over time.

### EIP-7918: Tuning Blob Base Fees & Reserve Mechanics
Previously, in the EIP-4844 blob market:

- Blob price often fell to 1 wei when demand was low.
- When demand spiked, prices rose sharply and unpredictably.

## EIP-7918 introduces:

- A reserve price: the base fee cannot drop below a fraction of the execution gas price.
- A reserve mechanism: excess fees can accumulate and be released to smooth out spikes.

The effects:

- Validators are fairly compensated for bandwidth and storage.
- Blob fees become low but stable rather than “near-zero then spiky”.
- ZKsync sequencers can better predict L1 costs, making ZKsync gas more stable and less prone to wild swings.

<img width="960" height="540" alt="image" src="https://github.com/user-attachments/assets/4909e4ca-29ca-4cd5-bc3e-46eed63944b2" />


## Core: Layer-1 Execution Scaling & DoS Protection

### EIP-7935: Raising the Gas Limit to 60M
Fusaka significantly raises Ethereum’s block gas limit to 60 million gas units. This is a major jump from previous limits in the mid-30M / 40M range and means:

- More transactions per block.
- Room for more complex smart contracts.
- Improved throughput even before L2 effects.

For ZKsync:

- More room per block to verify ZKsync’s validity proofs, which cost hundreds of thousands of gas each.
- L1–L2 bridge and force-exit operations can stay fast and censorship-resistant even during congestion.
- It helps ensure that summaries and proofs for ZKsync’s 15,000 TPS target can actually fit on L1.

### EIP-7825: Per-Transaction Gas Cap (~16.7M)
Previously, a single transaction could consume nearly an entire block of gas. Fusaka adds a per-tx gas cap of ~16.7M gas:

- This is a simple yet effective DoS safeguard.
- It prevents a single monstrous transaction from blocking the chain.

For ZKsync:

- Proof verification txs are normally well below this cap.
- The cap protects the network so that critical operations, such as proof submissions and exits, aren’t delayed by malicious or buggy contracts.

### EIP-7934: RLP Block Size Limit (10 MB)
As gas limits rise, physical block size must remain bounded.

- EIP-7934 caps the RLP-encoded block size at 10 MB.

This ensures:

- ZKsync’s data (state diffs, proof inputs/pointers) stays within safe network limits.
- ZKsync’s already efficient state-diff strategy fits comfortably under the new constraints.

### EIP-7823 & EIP-7883: ModExp Bounds & Gas Repricing
ModExp (modular exponentiation) is a heavy cryptographic primitive.

- EIP-7823: caps ModExp input fields at 8192 bits.
- EIP-7883: increases the ModExp gas cost to reflect the real computational complexity.

For ZKsync:

- ZKsync primarily uses modern elliptic-curve ZK systems (Plonk/Boojum), so ModExp isn’t central to them.
- These changes remove low-cost but high-risk attack vectors and keep the environment safer for high-value proof-of-concept operations.
- The input bounds protect against extreme edge cases that might otherwise cause verifiers to break.

## Core: Cryptography, UX, & Proposer Behavior

### EIP-7951: secp256r1 (P-256) Precompile
Modern security standards (WebAuthn, FIDO2, Apple Secure Enclave, Android Keystore, many HSMs) use secp256r1 (P-256).

Before Fusaka:

- Verifying a secp256r1 signature in EVM contracts costs around 1–3M gas.

With Fusaka:

- EIP-7951 adds a secp256r1 precompile.
- Signature verification costs fall to a few thousand gas.

For ZKsync & wallets:

- ZKsync’s Account Abstraction lets users authenticate with biometrics (Face ID, fingerprint) instead of seed phrases.
- With EIP-7951, those passkey wallets can cheaply control assets on both L2 and L1.
- Enterprise Prividium chains can plug directly into existing bank-grade HSMs, many of which already support secp256r1.
- This is a big step toward “Face ID-style” UX, like Apple Pay but for Ethereum/ZKsync.

### EIP-7917: Deterministic Proposer Lookahead
Fusaka enables predicting which validator will propose upcoming blocks.

This enables:

- Secure pre-confirmations on L1: users can get confirmations “as good as final” at around 100 milliseconds, based on commitments from the next proposer, before the transaction is actually in a block.
- Based on rollup designs, L2s like ZKSync can consider delegating ordering to L1 proposers, using pre-arranged inclusion.

For ZKsync:

- This directly supports Atlas's goal of finality within 1 second.
- It reduces preconfirmation complexity while strengthening guarantees.

### EIP-7939: CLZ (Count Leading Zeros) Opcode
EIP-7939 adds a new opcode, CLZ, which counts the number of leading zero bits in a 256-bit word.

- Previously, finding the MSB (log₂(n)) required loops or binary search logic, which cost 30–200 gas and bloated the bytecode.
- And with CLZ, the operation can be done in a single, cheap opcode (around a few gas units).

ZKsync impact (micro-optimizations and macro effects):

- Verifier contracts: bit-level checks in proof verification become cheaper.
- ZK circuits: range checks can be expressed with fewer constraints, improving proof generation time and proof size.
- DeFi protocols on ZKsync: math-heavy logic (e.g., Uniswap-style ticks, options pricing) can be optimized using CLZ, improving performance and making zkEVM compatibility more attractive.

## Core: Node Health, History, & Configuration

### EIP-7642: History Expiry (eth/69)
EIP-7642 makes Ethereum history management explicit:

- Nodes announce which block range they serve.
- They send updates when their history window changes.
- They drop bloom filters from receipts, reducing sync size by more than 500 GB.

The effects:

- Much faster and lighter syncing.
- Lower disk usage means more people can run full nodes, thereby strengthening decentralization.
- This moves Ethereum closer to a stateless future, aligning with ZKsync’s long-term architecture.

### EIP-7910: eth_config JSON-RPC Method
Adds a new RPC method, eth_config, to allow nodes to expose which hard fork or configuration they are running. The benefits:

- Reduces the risk of consensus failures caused by misconfigured nodes.
- Let's infra providers, indexers, bridges, and ZKsync’s supporting services automate checks and stay aligned with Ethereum upgrades.

## ZKsync Ecosystem Perspective
From ZKsync’s Atlas and Elastic Chain viewpoint, Fusaka is the missing infrastructure layer.

<img width="1024" height="562" alt="image" src="https://github.com/user-attachments/assets/28cde6e3-683a-4d05-a934-fbfcd3014832" />


### Atlas Targets & Layer-1 Constraints
ZKsync’s Atlas upgrade aims for:

- 15,000 TPS
- Less than one second finality.
- Unified liquidity across multiple Hyperchains.

These require:

- Massive DA bandwidth for state diffs with proofs.
- Highly scalable L1 compute for verifying ZK proofs.
- A secure, predictable fee environment.

Before Fusaka, L1 blob capacity and gas costs made this economically difficult, and after Fusaka:

- PeerDAS with BPO: data capacity scales dynamically while keeping node costs within acceptable bounds.
- Gas 60M with CLZ and ModExp fixes: more proofs per block, cheaper verifying math.
- secp256r1 and proposer lookahead: better UX and faster finality.

## Impacts from Stakeholder's Perspective

### Regular ETH Holders
- Cheaper L2 transactions: estimated 40–60% lower costs for many L2s, including rollups like Arbitrum, Optimism, Base, and ZKsync, thanks to more blob space and better pricing.
- Smoother UX: higher throughput and better DA make apps feel more responsive.

### DeFi Users
- Faster, cheaper complex interactions: multi-step DeFi strategies become economical again at a few cents per action, rather than tens of cents.
- Room for more sophisticated protocols: larger blocks with EVM micro-optimizations support more advanced designs.

### Developers
- More headroom: bigger blocks and more blobs remove many previous scaling ceilings.
- New tools: CLZ opcode and secp256r1 precompile open new cryptographic and UX patterns.
- ZK-friendliness: small changes, such as CLZ, produce tangible improvements in ZK proof workflows.

### Layer-2 Networks (ZKsync, Arbitrum, Optimism, Base, etc.)
- Around 3.5× more blob space and 40–60% lower L2 costs overall after Fusaka as DA supply grows.
- Aggregate L2 capacity can reach 100,000+ TPS across the ecosystem, compared to ~12,000 TPS estimates pre-Fusaka.
- Networks that best exploit the new blob and fee structure (like ZKsync with state diffs) gain a strong competitive edge.

### Validators & Node Operators
- With an estimated 85% reduction in blob bandwidth, for example, from ~750 MB/day to ~112 MB/day in one scenario.
- Lower hardware requirements mean more home validators, which in turn strengthen decentralization.
- Simpler operations and a deterministic proposer make behavior more predictable, while explicit history ranges provide greater control.

## Strategic Outlook & What’s Next
- Fusaka deepens the symbiotic relationship between Ethereum and ZKsync.
- It strengthens Ethereum’s institutional appeal, especially for Prividium-style ZKsync deployments that rely on standard cryptography (secp256r1) and hardened precompiles.
- The next upgrade in the roadmap is Glamsterdam, whose headliners include protocol-enforced Proposer–Builder Separation (ePBS) with Block-Level Access Lists (BALs).

And now that Fusaka is live, attention turns to the next major scaling milestone on December 9th. This marks the rollout of PeerDAS, which will raise blob capacity from a target of 6 to 10, with a maximum of 15.

On January 7th, the target will increase again to 14, with a maximum of 21. In addition, more increases in blob capacity are scheduled throughout 2026, with Ethereum expected to reach a maximum of 72 blobs by June 2026.

<img width="1487" height="1000" alt="image" src="https://github.com/user-attachments/assets/c85941d2-2217-461d-931f-a558bc3688b2" />

At the same time, Ethereum’s blob fees are now millions of times higher than they were before Fusaka. While this may sound alarming, Fusaka actually fixed a broken blob pricing mechanism that effectively set the blob fee to 1 wei, making it free. As a result, L2s now pay a very small fee to use L1 for data storage, rather than paying nothing at all.

## The Complete Summary
> Overall, the Fusaka upgrade went well, and L2s are posting blobs without missing a beat, and credit to all the teams involved. All L1s and L2s are running as one well-oiled machine, moving forward together.

Fusaka marks the moment when Ethereum proves it can scale without sacrificing decentralization by using tools such as PeerDAS, BPO forks, and fine-tuning the execution layer. Ethereum can support large L2 ecosystems such as ZKsync while keeping its base layer strong and open to many participants.

- The most decisive step of the “Surge” phase of Ethereum’s roadmap.
- The moment Ethereum shifts from a monolithic chain to a modular data-availability (DA) layer.
- Ethereum provides secure, scalable data and verification.
- The infrastructure base that makes ZKsync’s Atlas goals (15,000 TPS, sub-1s finality, unified liquidity across Hyperchains) economically viable.

<img width="1024" height="576" alt="image" src="https://github.com/user-attachments/assets/152d9758-2ec5-4e71-85de-e6b11dfa9b95" />


Fusaka bundles 13 official meta-EIPs, which fall into four main buckets:

1. **Data availability & blobs** (EIP-7594, 7892, 7918)
2. **Execution scaling & DoS hardening** (EIP-7935, 7825, 7934, 7823, 7883)
3. **Crypto & UX primitives** (EIP-7951, 7917, 7939)
4. **Node health & config tooling** (EIP-7642, 7910)

## Sources & References

- https://blog.ethereum.org/2025/10/21/fusaka-gascap-update
- https://www.coingecko.com/learn/what-is-ethereum-fusaka-upgrade
- https://www.coindesk.com/tech/2025/12/03/fusaka-cementing-ethereum-s-role-as-on-chain-finance-settlement-layer-bitwise
- https://thedefiant.io/news/blockchains/ethereum-launches-fusaka-upgrade-to-make-network-more-scalable
- https://blockworks.co/news/fusaka-update-today
- https://cointelegraph.com/news/ethereum-fusaka-upgrade-scaling-rollups-without-breaking-the-cor
- https://cointelegraph.com/news/ethereum-fusaka-upgrade-goes-live-instant-feel-user-experiences
- https://crypto.com/en/research/ethereum-fusaka-upgrade-nov-2025
- https://www.dlnews.com/articles/defi/ethereum-fusaka-brings-eightfold-increase-in-blob-capacity
- https://www.datawallet.com/crypto/ethereum-fusaka-upgrade-explained
- https://x.com/ethereum/status/1996258202669695080
- https://x.com/solidity_lang/status/1979169214108733522
- https://x.com/jarrodwatts/status/1996721154648924446
- https://x.com/jarrodwatts/status/1996011939974316525
- https://x.com/den0thic/status/1996602745097261245
- https://x.com/l3olanza/status/1996371148771057902
- https://x.com/sassal0x/status/1996346490709762482
- https://x.com/snapcrackle/status/1996203515367309517 (image)
- https://x.com/0xNairolf/status/1995869155770466318
- https://x.com/iamjosephyoung/status/1996474679071236370
- https://x.com/safe/status/1996228629618209137
- https://x.com/TheMoneyApe/status/1996096320650752051
- ....
- https://blobscan.com/block/23911043
- https://www.bitget.com/news/detail/12560605098993
- https://coinmetrics.substack.com/p/state-of-the-network-issue-262
- https://en.theblockbeats.news/news/60453
- https://www.blocknative.com/blog/eip-4844-blobs-and-blob-gas-what-you-need-to-know
- https://www.cube.exchange/what-is/proto-danksharding

> #DataAvailability #EIP7918 #ethereum #Layer2 #blockchain #BlockchainEngineering
> #ethereum #ETH #Fusaka #FusakaUpgrade
> #blobs #ZKsync #zkEVM #PeerDAS #DataSampling #Danksharding #supernodes #Testnets #L1 #L2 #Layer2 #L2Scaling #ZKRollups #Rollups
> #BPO #BPOForks #wei #Glamsterdam #eth_config #ePBS #ModExp #Prividium #WebAuthn #calldata 

## Offical Postings

> - [**LinkedIn**](https://www.linkedin.com/pulse/fusaka-goes-live-pushes-ethereum-next-era-triangulum-technologies-f64ne/)
> - [**Medium**](https://medium.com/inside-the-blocks/fusaka-goes-live-pushes-ethereum-into-the-next-era-60820e354ba5)
> - Paragraph
