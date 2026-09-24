# GenLayer AI Market Sentinel: Decentralized Push Oracle

A decentralized AI market classification engine built on GenLayer that evaluates 4-hour OHLCV candles to generate trustless trading signals via multi-LLM consensus.

## Live Deployment & Verification
* **Contract Address:** `0x96BBCe58F16fDC03B3f69A078dCFFa6e6e6d1697`
* **Network:** GenLayer StudioNet
* **GenLayer Explorer:** [View Contract 0x96BBCe58...](https://explorer-studio.genlayer.com/address/0x96BBCe58F16fDC03B3f69A078dCFFa6e6e6d1697)
* **GenLayer Studio:** [Import into Studio IDE](https://studio.genlayer.com/?import-contract=0x96BBCe58F16fDC03B3f69A078dCFFa6e6e6d1697)
* **Live dApp Frontend:** [Market Sentinel Web App](https://genvm-market-sentinel-nktt665js-ameer-hamza-s-projects2.vercel.app/)

---

## Core System Architecture

### 1. Push Oracle Design
Because GenLayer's GenVM execution environment enforces determinism by prohibiting arbitrary outbound HTTP requests, this protocol uses a **Cryptographic Push Oracle**:
* Completed 4-hour OHLCV candle payloads are assembled off-chain.
* The payload is canonicalized into sorted JSON and hashed with SHA-256.
* The contract verifies `calculated_hash == expected_sha256` before opening execution gates.

### 2. Multi-Stage Validation Gates
Before calling non-deterministic AI validators, the contract enforces deterministic on-chain sanity checks:
* **Supported Assets:** Restricted to `BTC/USDT`, `ETH/USDT`, `SOL/USDT`, `NEAR/USDT`, and `VIRTUAL/USDT`.
* **OHLC Integrity:** Rejects invalid ranges (`high < low`, `open/close` outside `[low, high]`, or negative volume).
* **Resistance Thresholds:** Evaluates candle close prices against pair-specific resistance lines (e.g., BTC at $100,000, ETH at $5,000, SOL at $250). AI execution triggers only when resistance conditions are satisfied.
* **Double-Processing Prevention:** Uses `TreeMap` lookups to prevent duplicate candle timestamps and replayed snapshot hashes.

### 3. Multi-LLM Consensus Engine
When validation gates pass, the contract triggers non-deterministic consensus across GenLayer validator nodes:
* Validators analyze price action relative to previous close and classify the market structure into:
  * `BULLISH_BREAKOUT`
  * `FAKE_OUT`
  * `CONSOLIDATION`
* Leader-validator agreement requires identical classification and validated reasoning format.
* Upon confirming a `BULLISH_BREAKOUT`, the contract emits a `MARKET_EVALUATED` event with `SIGNAL_EMITTED` and tracks virtual allocation against budget caps.

---

## Repository Files
* `ai_market_sentinel.py`: The complete GenLayer Intelligent Contract containing the logic gates, SHA-256 verification, and multi-LLM consensus execution.
