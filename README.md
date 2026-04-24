# Nado Signal Bot

> Real-time trading signal bot for Nado CLOB DEX on Ink L2 (Kraken). Monitors funding rates, CLOB order book imbalances, open interest shifts, and large wallet entries across spot and perp markets and delivers structured alerts to Telegram before the move develops.

*Revision: April 2026*

## What is Nado Signal Bot?

Nado Signal Bot is a market intelligence tool for Nado traders on Ink L2. It continuously scans Nado's CLOB order book across spot and perpetual markets for high-conviction setups - funding extremes, sudden OI acceleration, order book bid/ask imbalances, large single orders - and sends structured signal cards to Telegram when conditions align.

![preview_nado-signal-bot](https://github.com/user-attachments/assets/abd40d78-d8d7-4207-a64b-91452ce72d51)

No auto-execution. The bot delivers the signal; you decide whether to act. Each alert includes market, direction, entry zone, stop-loss, take-profit, and the specific trigger combination that fired. You trade on Nado using the signal as your entry context.

Nado's CLOB structure provides real order book data - unlike AMM pools, CLOB signals reflect genuine order flow from market participants.

---

## Download

| Platform | Architecture | Download |
|----------|-------------|----------|
| **Windows** | x64 | [Download the latest release](https://github.com/cobra5000vibe/nado-signal-bot/releases) |

---

## Signal Triggers

| Signal Type | Trigger Condition | Description |
|-------------|------------------|-------------|
| Funding Extreme | Funding > 0.03% or < -0.02% | Extreme funding precedes reversal pressure |
| OI Acceleration | OI increases > 7% in 10 min | New capital entering the market rapidly |
| CLOB Imbalance | Bid/ask depth ratio > 2.5:1 | Directional order pressure building |
| Large Order | Single order > $3,000 | Significant participant positioning |
| Spot Momentum | Spot price breaks 2h high/low | Breakout confirmation on spot market |

---

## Engine Features

* **Spot and perp monitoring** - signals fire on both spot and perp Nado markets
* **Real-time CLOB depth** - reads live bid/ask depth ratios from Nado order book
* **Funding rate feed** - monitors funding across all Nado perp markets
* **OI acceleration detector** - tracks open interest changes per 10-minute window
* **Large order filter** - flags single CLOB orders above your size threshold
* **Multi-signal combinator** - optionally require 2+ triggers before alert fires
* **Telegram delivery** - sends formatted signal card with market, direction, entry, SL, TP
* **Signal log** - stores all alerts with trigger data and price outcome for accuracy review

---

## Two Ways to Run It

| | Windows App | Python Bot |
|---|---|---|
| **Setup** | Double-click | `pip install` + config |
| **Signal rules** | Config-based | Full code access |
| **Spot+perp** | Both covered | Separate toggles |
| **Config** | `config.toml` | Direct code access |
| **Logs** | Dashboard | JSON per signal |

## Quick Start

```
# 1. Download from Releases
# 2. Edit config.toml - set Telegram token and signal thresholds
# 3. Run Signal Bot - alerts start arriving for qualifying setups
```

### Python

```bash
cd nado-signal-bot/python
pip install -r requirements.txt
python nado-signal-bot-raw-v.1.4.14.py
```

---

## How It Works

1. **Poll** - fetches funding rate, OI, CLOB depth, and recent large orders from Nado API
2. **Evaluate** - checks each market against all active signal triggers
3. **Combine** - fires alert when minimum trigger count is met
4. **Deliver** - sends Telegram signal card with full trade context

### Config Reference

```toml
[nado]
api_base = "https://api.nado.exchange"
poll_interval_seconds = 8
include_spot = true
include_perp = true

[signals]
min_triggers_to_fire = 2
funding_extreme_pct = 0.03
oi_acceleration_pct = 7.0
clob_imbalance_ratio = 2.5
large_order_usd = 3000
spot_momentum_window_hours = 2

[alerts]
telegram_bot_token = ""
telegram_chat_id = ""
signal_cooldown_min = 20
```

---

## Signal Card Format (Telegram)

```
NADO SIGNAL
Market: SOL-PERP (Ink L2)
Direction: LONG
Entry zone: 141.20 - 142.80
Stop-loss: 138.50
Take-profit: 148.00
Triggers: funding_extreme (-0.031%) + oi_acceleration (+9.1%)
Confidence: HIGH (2/2 triggers)
Time: 2026-03-19 08:22 UTC
```

---

## Verified Live

**Configuration used:**
* Spot + perp, 2-trigger minimum, funding 0.03%, OI acceleration 7%

| | Details |
|---|---|
| Market | SOL-PERP |
| Signal | LONG |
| Triggers | Funding -0.031% + OI +9.1% |
| Entry zone | 141.20 - 142.80 |
| Delivered | 2026-03-19 08:22 UTC |

| | Result |
|---|---|
| High after signal | 149.40 (TP zone exceeded) |
| Signal lead time | ~3h before market repriced |

![nado signal alert result](https://github.com/user-attachments/assets/2fd88f5c-6d81-4ef7-8092-766b8028956b)

---

## Frequently Asked Questions

**Does the bot execute trades?**
No. Nado Signal Bot delivers signals to Telegram only. You execute trades manually on Nado.

**Does it cover spot markets as well as perps?**
Yes. Set `include_spot = true` to receive signals on Nado's spot market order book alongside perp signals. The Spot Momentum trigger is specific to spot price action.

**What is the CLOB imbalance signal?**
When the bid side of Nado's order book has 2.5x+ more depth than the ask side, there is significant buy pressure building. This often precedes upward price movement as the order book rebalances.

**Can I require only one trigger?**
Yes. Set `min_triggers_to_fire = 1` to receive alerts on any single trigger match. This increases alert volume but reduces the latency between trigger and signal delivery.

**What is the large order signal?**
Any single CLOB order above your configured USD threshold triggers this signal. Large orders represent informed participants taking significant directional positions.

**Does this work for INK airdrop strategy?**
Yes. Use signals to guide which markets to trade during farming sessions for more efficient volume allocation and potentially better P&L.

---

## Use Cases

- **Nado trading signals** - receive Telegram alerts for high-conviction Nado CLOB setups
- **Ink L2 perp signal bot** - monitor funding and OI across Nado perp markets in real time
- **CLOB order book signals** - detect bid/ask imbalances on Nado before price moves
- **Nado spot momentum bot** - alert on 2h breakouts in Nado spot markets with OI confirmation
- **Kraken L2 signal alerts** - systematic market intelligence for Nado traders on Ink L2

---

## Repository Structure

```
nado-signal-bot/
+-- nado-signal-bot-raw-v.1.4.14.exe
+-- config.toml
+-- data/
|   +-- signals/
|   +-- logs/
|   +-- dll/
+-- python/
|   +-- src/
|   |   +-- monitor.py
|   |   +-- evaluator.py
|   |   +-- combinator.py
|   |   +-- telegram_sender.py
|   +-- scripts/
|   |   +-- backtest.py
|   +-- requirements.txt
+-- README.md
```

---

## Requirements

```
python-dotenv, httpx, web3, typer[all], python-telegram-bot, devtools
```

* Nado API access (public market data)
* Telegram bot token for signal delivery
* Python 3.10+

---

*The market moves fast. Your bot moves faster.*
