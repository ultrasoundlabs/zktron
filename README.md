# zktron
ZK light client for Tron Network written in Noir

## How

Tron's consensus mechanism is deadly simple. Every 7200 blocks users delegate their TRX to validators (representatives), and the 27 validators with most votes become Super Representatives - ones who produce blocks. Producer selection is round-robin, and after 18 confirmations (that is, 2/3 of the SR set) the block is considered finalized.

The block production is an ECDSA signature over the SHA256 hash of the protobuf-encoded block header. That is, one block = one signature. This allows us to efficiently generate ZK proofs for light verification of the Tron blockchain. Even though Tron does not merkleize state, transaction root in the block headers is already pretty powerful.

## Why

As we can ZK prove Tron's consensus, we can as well make some other blockchain aware of transactions happening in Tron. This way, we can, for example, implement a P2P-like bridge between USDT in Tron and any other chain.

[USDT on Tron is huge in the third world](https://mirror.xyz/0x8958D0c419BCDFB8A86b8c0089552bE015fbe364/ODhOuYjK80atc9_jGprXotSo3PNobT1PRLFtorXHBrA). However, Tron is a heavily cartelized network with fees comparable with Ethereum L1 and no integrations with ecosystems outside Tron. Its tech is outdated, updates are mostly targeted at making TRX go up, and the vast majority of activity is people from poor countries with dead economies paying each other with USDT with $1.5 fee per transfer. We can use such design to implement a trustless, cheap, and fast bridge from Tron to, say, any Ethereum L2. On L2s, the fees are an order of magnitude lower, the ecosystem is huge, and the entire developed world is already onboarded. Let's untron the people!

## Implementation

> **This thing is a PoC, probably unsafe and lacks some checks!**

According to my tiny [research](https://github.com/alexhooketh/tron-light-client), SR set hasn't changed in at least the last 250 days. This is because the most existing SRs are funded with Justin Sun's TRX holdings, and other validators can only get enough delegations to become SR partners who don't participate in block production.

So, I did not implement vote counts, and just wrote down the public keys of current SRs in the circuit. I believe it'll be cheaper to just redeploy the verifier every time the set is updated (almost never). I didn't implement round-robin production checks either, but AFAICT they're feasible to ZK prove.

In `Prover.toml`, there's header data for block 62913164. You can fetch this data using [this script](https://github.com/alexhooketh/tron-light-client). In public inputs, there are only three values - previous block ID, new block ID, and a transaction root. Somewhat a state transition function :P