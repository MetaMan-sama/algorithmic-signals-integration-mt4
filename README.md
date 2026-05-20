# Algorithmic Signals Integration — MQL4 Script

A MetaTrader 4 script that ingests **external ML/AI-generated signal values** from a CSV file and fires buy/sell alerts — with optional automatic trade execution — based on configurable thresholds.

---

## Overview

This script bridges external algorithmic or machine learning models with MetaTrader 4. Your model writes a numeric signal value to a CSV file; the script reads it every minute, evaluates it against buy/sell thresholds, and either alerts you or places a trade automatically. This decouples signal generation (Python, R, any ML pipeline) from execution, keeping your MT4 environment clean and model-agnostic.

---

## Features

- **External signal ingestion** — reads a numeric value from a semicolon-delimited CSV via `FileReadNumber()`
- **Threshold-based buy/sell detection** — configurable upper and lower bounds
- **Optional auto-trading** — places `OP_BUY` or `OP_SELL` orders with SL/TP when `AutoTrade = true`
- **Three notification channels:** sound alert, email, and mobile push
- **Graceful error handling** — skips cycle and logs if signal file is missing or unreadable
- **Lightweight loop** — polls once per minute (`Sleep(60000)`)

---

## How It Works

1. Every minute, `LoadSignal()` opens the CSV at `SignalFilePath` using `FileOpen()` with `FILE_CSV | FILE_READ`
2. The first numeric value is read via `FileReadNumber()` and returned as `signalValue`
3. The value is evaluated:
   - `signalValue >= BuyThreshold` → **Buy Signal** fired
   - `signalValue <= SellThreshold` → **Sell Signal** fired
4. If `AutoTrade = true`, `PlaceTrade()` calls `OrderSend()` using `MarketInfo()` for Ask/Bid, with SL/TP in points
5. Missing or unreadable file → cycle skipped and logged

---

## Signal File Format

Semicolon-delimited CSV in the MT4 `Files` sandbox directory:

```
0.85
```
or
```
0.85;timestamp;optional_metadata
```

> File path: `%APPDATA%\MetaQuotes\Terminal\<ID>\MQL4\Files\signals.csv`

---

## Input Parameters

| Parameter        | Type   | Default       | Description                                       |
|------------------|--------|---------------|---------------------------------------------------|
| `SignalFilePath` | string | `signals.csv` | Path to external signal file (MT4 sandbox)        |
| `BuyThreshold`   | double | `0.7`         | Signal value at or above which a buy is triggered |
| `SellThreshold`  | double | `-0.7`        | Signal value at or below which a sell is triggered|
| `EnableAlerts`   | bool   | `true`        | Fire an on-screen/sound alert                     |
| `EnableEmail`    | bool   | `false`       | Send an email notification                        |
| `EnablePush`     | bool   | `false`       | Send a mobile push notification                   |
| `AutoTrade`      | bool   | `false`       | Automatically place trades on signal              |
| `LotSize`        | double | `0.1`         | Lot size for auto-placed trades                   |
| `SL`             | double | `50`          | Stop loss in points                               |
| `TP`             | double | `100`         | Take profit in points                             |

---

## Installation

1. Copy `Algorithmic_Signals_Integration_001.mq4` to `MQL4/Scripts/`
2. Compile in MetaEditor (F7)
3. Place your signal CSV in the MT4 Files sandbox
4. Drag onto any chart from Navigator → Scripts
5. Configure inputs and click **OK**

> **Warning:** `AutoTrade = true` places real orders. Always test on a **demo account** first.

---

## Requirements

- MetaTrader 4 (`#property strict` compatible build)
- MQL4 compiler (MetaEditor)
- External process (Python, R, etc.) writing signal values to the CSV

---

## License

MIT License

Copyright (c) 2026

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
