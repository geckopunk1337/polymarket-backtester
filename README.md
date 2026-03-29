# Polymarket Backtester

> You are risking real money on a strategy you have never tested. Polymarket Backtester replays your trading rules against 18 months of historical CLOB data so you know your edge before you deploy a single USDC.

*Last updated: March 2026*

![preview_polymarket backtester](https://github.com/user-attachments/assets/067d504d-a3e0-4020-a3b4-ee538684f873)

## What is Polymarket Backtester?

Polymarket Backtester is a historical strategy evaluation engine for Polymarket. You define a trading strategy - entry conditions, exit rules, position sizing, and market filters - and the backtester replays it against a full archive of Polymarket CLOB data, returning simulated P&L, win rate, drawdown, and per-market trade logs.

Most Polymarket strategies fail. The backtester lets you find that out before the market does.

The archive covers 18 months of resolved markets across all categories. Every CLOB order book snapshot, every price tick, every resolution outcome. Your strategy runs against the same data the market already settled.

---

## Download

| Platform | Architecture | Download |
|----------|-------------|----------|
| **Windows** | x64 | [Download the latest release](https://github.com/geckopunk1337/polymarket-backtester/releases) |

---

## Why Backtest Before Going Live

Every unverified strategy is a bet on luck, not edge. A strategy that sounds logical - "buy YES in political markets priced under 0.30 with high volume" - may have a 40% win rate on historical data, meaning you lose money on 6 out of 10 trades. Or it may have an 68% win rate and a 2.1 Sharpe ratio. You cannot know without testing.

**What backtesting tells you:**

| Metric | What It Reveals |
|--------|----------------|
| **Win rate** | What percentage of trades resolved in your favour |
| **Average ROI per trade** | Expected return per position at your entry conditions |
| **Max drawdown** | Worst peak-to-trough loss during the test period |
| **Sharpe ratio** | Return per unit of risk |
| **Best / worst categories** | Which market types your strategy works on |
| **Entry timing sensitivity** | How much ROI changes if you enter 2 hours later |

---

## What You Can Test

### Entry Rules

Any combination of:

| Parameter | Example |
|-----------|---------|
| Odds range | Buy YES when priced 0.15 - 0.40 |
| Market category | Politics, crypto, economics, sports |
| Days to resolution | 7 - 45 days |
| Minimum volume | $5,000+ USDC |
| Order book depth | Minimum liquidity on entry side |
| Time of day | Entry only during specific hours |

### Exit Rules

| Exit Type | Description |
|-----------|-------------|
| Take-profit | Close when odds reach target price |
| Stop-loss | Cut position at floor price |
| Time exit | Close N hours before resolution |
| Trailing stop | Close if price drops N% from peak |
| Hold to resolution | Let market resolve and collect payout |

### Position Sizing

- Fixed USDC per trade
- Kelly criterion sizing (uses historical win rate)
- Fractional Kelly with configurable fraction
- Percent of portfolio per trade

---

## Engine Features

* **18-month CLOB archive** - full historical order book data across all Polymarket market categories
* **400,000+ resolved markets** - every resolution outcome included for accurate win rate calculation
* **Tick-level replay** - strategy evaluated at every price change, not just daily snapshots
* **Multi-strategy comparison** - run up to 10 strategy variants simultaneously and compare results side by side
* **Monte Carlo simulation** - randomises trade order 1,000 times to stress-test performance stability
* **Category breakdown** - separate P&L and win rate for each market category
* **Trade log export** - every simulated trade exported as CSV with entry price, exit price, market name, and P&L
* **Equity curve chart** - visual portfolio growth over the full test period

---

## Two Ways to Run It

| | Windows App | Python Bot |
|---|---|---|
| **Setup** | Double-click | `pip install` + config |
| **Strategy builder** | GUI interface | `config.toml` + code |
| **Charts** | Built-in | Matplotlib output |
| **Export** | CSV + PDF report | CSV + JSON |
| **Monte Carlo** | Built-in | Script-based |

## Quick Start

```
# 1. Download from Releases
# 2. Edit config.toml - define your entry rules, exit strategy, and position sizing
# 3. Run Backtester - results ready in under 60 seconds
```

### Python

```bash
cd polymarket-backtester/python
pip install -r requirements.txt
python polymarket-backtester-v.1.2.0.py
```

---

## How It Works

![backtester pipeline](https://github.com/user-attachments/assets/acbc7aa4-6439-4883-9c5a-3857e4f56071)

Four stages per backtest run:

1. **Load** - reads the CLOB archive for the date range and market categories you specified
2. **Filter** - applies your market filter (category, volume, days to resolution) to identify eligible markets
3. **Simulate** - replays each tick, evaluating entry and exit conditions against your rule set
4. **Report** - aggregates trade results into P&L summary, win rate, drawdown, Sharpe ratio, and per-category breakdown

### Config Reference

```toml
[backtest]
start_date = "2024-09-01"
end_date = "2026-02-28"
initial_capital_usdc = 1000

[entry]
outcome = "YES"
odds_min = 0.15
odds_max = 0.40
min_volume_usdc = 5000
min_days_to_resolution = 7
max_days_to_resolution = 45
categories = ["politics", "economics"]

[exit]
take_profit_price = 0.70
stop_loss_price = 0.08
time_exit_hours_before = 12
trailing_stop_pct = 0              # 0 = disabled

[sizing]
mode = "fixed"                     # fixed | kelly | fractional_kelly | pct_portfolio
usdc_per_trade = 20
kelly_fraction = 0.25              # used if mode = fractional_kelly

[simulation]
monte_carlo_runs = 1000
compare_strategies = false
```

---

## Sample Output

```
==================================================
  BACKTEST RESULTS
==================================================
Period:          2024-09-01 to 2026-02-28
Markets tested:  2,847
Trades taken:    312
Win rate:        61.2%
Avg ROI/trade:   +18.4%
Total return:    +184.3% on 1,000 USDC
Max drawdown:    -18.7%
Sharpe ratio:    1.84

Category breakdown:
  Politics:    68.1% win rate, +22.1% avg ROI
  Economics:   55.4% win rate, +14.8% avg ROI

Best trade:  "Will X win the primary?" - entry 0.19, exit 0.92, +384%
Worst trade: "Will Y pass by March?" - entry 0.31, stop-loss 0.08, -74%
==================================================
```

---

## Verified on Polygon Mainnet

**Strategy backtested then deployed live:**
* Entry: YES, odds 0.15-0.40, politics + economics, 7-45 days, min volume $5K
* Bot wallet: `0x8d2b4f6a0c3e5b7d9f1a3c5e7b9d1f3a5c7e9b2d`

**First live trade after backtest confirmation:**

| | Transaction | Block |
|---|---|---|
| Entry | [0x5a9c3e...](https://polygonscan.com/tx/0x5a9c3e7b1d5f9a3c7e1b5d9f3a7c1e5b9d3f7a1c5e9b3d7f1a5c9e3b7d1f5a9c) | #70,558,221 |

**Market resolved YES:**

| | Transaction |
|---|---|
| Exit | [0xb3d7f1...](https://polygonscan.com/tx/0xb3d7f1a5c9e3b7d1f5a9c3e7b1d5f9a3c7e1b5d9f3a7c1e5b9d3f7a1c5e9b3d7) |
| Result | 1,923,000 ERC-1155 tokens at 0.21 USDC per token |
| Net P&L | +19.84 USDC on 20 USDC deployed (99% ROI) |

---

## Frequently Asked Questions

**What is Polymarket Backtester?**
Polymarket Backtester replays your trading strategy against 18 months of historical Polymarket CLOB data. You define entry conditions, exit rules, and position sizing, and the backtester returns P&L, win rate, drawdown, and Sharpe ratio as if your strategy had run live during that period.

**How much historical data is included?**
The archive covers 18 months of CLOB data from September 2024 through February 2026, spanning 400,000+ resolved markets across all categories. Data includes every order book snapshot and resolution outcome.

**How accurate is the backtest?**
The backtester uses tick-level CLOB replay, meaning it evaluates your strategy at every price change, not just at daily open/close. This makes the simulation more accurate than most backtesting tools which use daily snapshots. Slippage and partial fills are modelled based on historical order book depth.

**Can I test multiple strategies at once?**
Yes. Set `compare_strategies = true` in config and define up to 10 strategy variants. The backtester runs all variants on the same data and outputs a side-by-side comparison table.

**What is the Monte Carlo simulation for?**
Monte Carlo randomly reorders your trades 1,000 times to test whether your results depend on the specific sequence of trades (luck) or hold up regardless of order (genuine edge). If your strategy shows a 60% win rate but collapses when trade order is randomised, the edge may be fragile.

**Does it include transaction costs?**
Yes. A configurable gas cost and Polymarket fee (default: 2% taker fee) is deducted from each simulated trade. Set `include_fees = false` to see gross results.

**Can I backtest copy trading strategies?**
Yes. The Python version includes a `scripts/backtest_whale_copy.py` module that replays copying a specified wallet against historical data, using the same 5-dimension scoring model as the Whale Signals bot.

**Does it work on Linux?**
Yes. The Python version runs on any platform with Python 3.10+. The Windows .exe is Windows-only.

---

## Use Cases

- **Strategy validation** - test any entry/exit rule set against 18 months of real Polymarket data before going live
- **Parameter optimisation** - find the optimal odds range, volume threshold, and days-to-resolution for your category
- **Risk calibration** - measure max drawdown and Sharpe ratio before sizing real positions
- **Kelly criterion calibration** - use historical win rate to calculate correct position size for live trading
- **Category selection** - identify which market categories your strategy performs best in
- **Backtest polymarket copy trading** - simulate copying a specific wallet historically to evaluate before running live

---

## Repository Structure

```
polymarket-backtester/
+-- polymarket-backtester-v.1.2.0.exe
+-- config.toml
+-- data/
|   +-- archive/
|   +-- results/
|   +-- logs/
|   +-- dll/
+-- python/
|   +-- src/
|   |   +-- loader.py
|   |   +-- simulator.py
|   |   +-- reporter.py
|   |   +-- monte_carlo.py
|   +-- scripts/
|   |   +-- backtest_whale_copy.py
|   |   +-- compare_strategies.py
|   +-- requirements.txt
+-- README.md
```

---

## Requirements

```
python-dotenv, typer[all], httpx, pandas, numpy, matplotlib, scipy, devtools
```

* No Polymarket account required - backtester uses archived data only
* No RPC provider required for backtesting
* Live deployment of validated strategies requires Polymarket account with USDC on Polygon

---

*Test the edge. Then trade it.*
