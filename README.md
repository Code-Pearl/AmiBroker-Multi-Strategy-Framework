
```markdown

 ▄▄▄▄▄▄▄   ▄▄▄▄▄   ▄▄▄▄▄▄    ▄▄▄▄▄▄▄                ▄▄▄▄▄▄▄    ▄▄▄▄▄▄▄   ▄▄▄▄   ▄▄▄▄▄▄▄   ▄▄▄    
███▀▀▀▀▀ ▄███████▄ ███▀▀██▄ ███▀▀▀▀▀                ███▀▀███▄ ███▀▀▀▀▀ ▄██▀▀██▄ ███▀▀███▄ ███    
███      ███   ███ ███  ███ ███▄▄                   ███▄▄███▀ ███▄▄    ███  ███ ███▄▄███▀ ███    
███      ███▄▄▄███ ███  ███ ███                     ███▀▀▀▀   ███      ███▀▀███ ███▀▀██▄  ███    
▀███████  ▀█████▀  ██████▀  ▀███████                ███       ▀███████ ███  ███ ███  ▀███ ████████
                                    ▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄                                             

```

# AmiBroker Multi-Strategy Framework & Combined Equity Curve Engine

A plug-and-play AmiBroker Formula Language (AFL) framework designed to export, compare, and combine equity curves across multiple quantifiable trading strategies.

---

## Key Features

* **Multi-Strategy Backtesting:** Toggle between multiple quantitative strategies (Connors Double 7, Connors RSI(2), 5-Days Down/Up, Buy & Hold) within a single AFL script.
* **Automated Data Export:** Uses AmiBroker's Custom Backtester Interface (`CBT`) to export portfolio equity arrays into hidden composite tickers (`~~TickerName`).
* **Normalized % Comparison:** Scales raw dollar curves into baseline percentages ($100\% = \text{Day 1}$) so strategies with vast profit gaps can be accurately plotted side-by-side.
* **Combined Portfolio Curve:** Averages and overlays multiple equity curves into a single master portfolio line, allowing visual analysis of diversification benefits.
* **Null Protection:** Uses `Nz()` array handling to prevent missing or unexecuted strategy backtests from breaking the portfolio calculation.

---

## Included Trading Strategies

1. **Larry Connors Double 7:** Buys when price is above the 200 SMA and closes at a 7-day low. Exits on a 7-day high.
2. **Larry Connors RSI(2):** Mean reversion setup buying dips when $\text{RSI}(2) < 10$ above the 200 SMA. Exits above the 5 SMA.
3. **5-Days Down / 5-Days Up:** Buys after 5 consecutive down closes and sells after 5 up closes.
4. **Buy & Hold Benchmark:** Full portfolio equity allocation over the backtest duration for baseline comparison.

---

## How It Works

```
[ Mode 1: Backtest ] ---> Export Equity to Static Composites (~~Ticker)
                                  │
                                  ├──> [ Mode 2: Individual Comparison ] (Normalized %)
                                  └──> [ Mode 3: Combined Portfolio ]    (Averaged $)

```

---

## Quick Start Guide

### Step 1: Chart Setup

1. Open AmiBroker and create a new blank chart pane (**File → New → Blank Chart**).
2. Save `Multi_Strategy_Framework.afl` into your AmiBroker **Formulas** folder.
3. Drag and drop the AFL file onto your new blank chart.

### Step 2: Generate Strategy Data (Mode 1)

1. Open the **Analysis Window** (`Ctrl + R`) and load `Multi_Strategy_Framework.afl`.
2. Right-click the chart pane → select **Parameters**.
3. Set **Mode** to `1. Backtest Strategy`.
4. Select a strategy from the dropdown (e.g., `Larry Connors Double 7`).
5. Click **Portfolio Backtest** in the Analysis Window.
6. Repeat steps 4 & 5 for each strategy, including the `Buy and Hold Benchmark`.

### Step 3: View Results (Modes 2 & 3)

* **Individual Comparison:** Set **Mode** to `2. Individual Comparison` in Chart Parameters to view all strategy growth lines normalized starting at 100%.
* **Combined Portfolio:** Set **Mode** to `3. Combined Portfolio` to plot the combined multi-strategy curve against Buy & Hold.

---

## AFL Code Overview

```afl
// Backtest Execution snippet using CBT
if( Mode == "1. Backtest Strategy" )
{
    SetCustomBacktestProc(""); 
    if( Status("action") == actionPortfolio )
    {
        bo = GetBacktesterObject();
        bo.Backtest(); 
        
        eq = Foreign("~~~EQUITY", "C"); // Catch equity array
        AddToComposite( eq, TickerName, "X", atcFlagDeleteValues | atcFlagEnableInPortfolio );
    }
}

```

---

## Notes & Best Practices

* **Identical Date Ranges:** Always run **Mode 1** across the exact same date range in the Analysis window for all strategies to ensure accurate overlapping arrays.
* **Overwriting Data:** Running a backtest in **Mode 1** using the `atcFlagDeleteValues` flag automatically refreshes and overwrites that specific composite ticker with your latest results.
* **Position Sizing:** The framework defaults strategy allocation to 20% equity per position and benchmark allocation to 100%. Adjust `SetPositionSize()` in Section 2 as needed.

