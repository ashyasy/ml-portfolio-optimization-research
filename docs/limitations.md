# Limitations

This document explains the main limitations of the **Machine Learning Portfolio Optimization Research** project.

The limitations are important because this project involved financial machine learning, and financial models can look stronger than they really are if the results are not interpreted carefully. The goal of this project was to build and evaluate a research pipeline, not to claim that the model is a finished trading system.

---

## 1. Overview

The project successfully built an end-to-end workflow for stock ranking, signal evaluation, and PPO-based portfolio exposure control.

The workflow included:

```text
historical market data
→ cleaning
→ feature engineering
→ target comparison
→ multi-signal modeling
→ Top-10 basket construction
→ PPO exposure control
→ baseline comparison
→ result interpretation
```

However, several limitations affected the strength of the final results.

The most important limitations were:

- Limited historical data coverage
- One-year final PPO test period
- No guarantee that the signal generalizes across future market regimes
- Simplified transaction cost assumptions
- Limited slippage and execution modeling
- PPO instability in noisy financial environments
- Risk of data leakage if features are not shifted correctly
- Lack of full news, earnings, and macro-event features
- PPO not clearly outperforming the strongest simple Top-10 baselines

These limitations do not make the project weak. They actually make the project more honest because they show that the results were interpreted carefully instead of being overclaimed.

---

## 2. Limited Historical Data

One major limitation was the amount of historical data available.

The dataset covered approximately five years of historical stock data. This was enough to build a working research pipeline, but it is still limited for a stock-market modeling problem.

Financial markets change across different time periods. A model trained on one set of market conditions may not perform the same way in a different environment.

For example, a strategy can behave differently during:

- Bull markets
- Bear markets
- High-volatility periods
- Low-volatility periods
- Interest-rate changes
- Sector rotations
- Recession fears
- Inflation-driven markets
- Earnings-heavy periods
- News-driven market shocks

Because of this, a five-year dataset is useful for experimentation, but not enough to fully prove that a strategy is stable.

A stronger version of the project would test the same pipeline across a longer historical period and across more market regimes.

---

## 3. Limited Final Test Period

Another important limitation was the final unseen PPO test period.

The PPO exposure controller was tested on the 2025 unseen test period. This helped make the evaluation more realistic because the model was tested on data that came after the training period.

However, one unseen year is still limited.

A single test year can be affected by specific market conditions. If 2025 had strong trends, unusual volatility, or a favorable environment for the signal basket, the result may not fully represent how the strategy would perform in other years.

A better evaluation would include multiple walk-forward test periods, such as:

- Train on 2021–2022, test on 2023
- Train on 2021–2023, test on 2024
- Train on 2021–2024, test on 2025
- Continue this process as more data becomes available

This would make it easier to see whether the strategy works consistently or only in one favorable year.

---

## 4. Market Regime Dependence

Financial signals can be highly regime-dependent.

A model may perform well during one market environment and poorly during another. This is a major challenge in financial machine learning because the relationship between features and future returns is not fixed.

For example:

- Momentum features may work well during trending markets.
- Reversal features may work better during choppy markets.
- Volatility features may matter more during market stress.
- Extended-hours signals may behave differently during earnings seasons.
- Sector features may matter more during sector rotation periods.

The project included market-regime and sector-regime features, but the model still may not fully understand all regime changes.

A future version should test whether performance changes across different regime types.

---

## 5. Data Leakage Risk

Data leakage was one of the most important technical risks in the project.

Data leakage happens when information from the future accidentally enters the training features. In financial modeling, even a small leakage issue can make a model look much better than it really is.

Possible leakage risks included:

- Rolling return features that were not shifted correctly
- Future returns accidentally included as input features
- Cross-sectional ranks calculated using future information
- Market-relative features using data from the prediction period
- Train/test splits that accidentally overlap in time
- Extended-hours features being assigned to the wrong prediction window
- Features calculated after the target return period had already started

The project addressed this risk by using time-aware splitting, reviewing feature columns, removing suspicious return-based features when needed, and keeping targets separate from features.

Even with these controls, leakage prevention remains a key limitation because financial pipelines are complex.

A stronger version of the project would include automated leakage checks and stricter feature timestamp validation.

---

## 6. Noisy Labels

Short-term stock prediction labels are very noisy.

A stock can move up or down for reasons that are not visible in historical price and volume data.

Examples include:

- Earnings reports
- Analyst upgrades and downgrades
- Breaking news
- Product announcements
- Lawsuits
- Federal Reserve announcements
- Inflation data
- Sector shocks
- Market-wide selloffs
- Large institutional trades

Because of this, even a well-engineered feature set may not fully explain future stock movement.

This means the model should not be expected to predict every stock correctly. The more realistic goal is to find a small statistical edge that can separate stronger baskets from weaker baskets over time.

---

## 7. Accuracy Does Not Equal Profitability

One of the project results showed that at least one movement window reached accuracy above 59%, and one walk-forward signal reached accuracy above 60% in a later test year.

This is useful, but it does not automatically prove that the strategy is profitable.

A model can have good directional accuracy and still lose money if:

- The wrong predictions lose more money than the right predictions make
- The model is correct on small moves but wrong on large moves
- Transaction costs remove the edge
- Slippage is too high
- The portfolio is too concentrated
- The model takes too much exposure during drawdowns
- The model works only in one test period

For this reason, the project evaluated portfolio behavior and baselines instead of relying only on classification accuracy.

---

## 8. Simplified Transaction Costs

The project included transaction cost assumptions, but they were still simplified.

The transaction cost assumption helped make the evaluation more realistic than a cost-free backtest. However, real trading costs are more complicated.

A production trading system would need to consider:

- Bid-ask spread
- Slippage
- Market impact
- Liquidity constraints
- Order size
- Order execution timing
- Partial fills
- Broker fees
- Shorting costs if long-short strategies are used

Simplified transaction costs can make a strategy look better than it would be in real execution.

Future versions should include more conservative execution assumptions.

---

## 9. Slippage and Market Impact Were Not Fully Modeled

Slippage happens when the actual execution price is worse than the expected price.

Market impact happens when a trade is large enough to move the price.

The project focused on liquid stocks and ETFs to reduce this problem, but slippage and market impact were not fully modeled.

This matters because a strategy that trades frequently or rebalances often can lose a large part of its edge during execution.

A future version should estimate slippage based on:

- Average daily volume
- Dollar volume
- Bid-ask spread estimates
- Position size
- Rebalance frequency
- Volatility
- Time of day

This would make the results more realistic.

---

## 10. PPO Did Not Clearly Beat the Strongest Baselines

One of the most important limitations was the PPO result.

The PPO exposure controller performed better than SPY during the 2025 test period. This suggested that the signal-based Top-10 basket contained useful information.

However, PPO did not clearly outperform the strongest simple Top-10 baselines, such as:

- Equal-weight Top-10
- Inverse-volatility Top-10
- Static exposure Top-10 portfolios

This means the PPO environment worked as a first version, but the PPO policy was not clearly superior to simpler portfolio rules.

This is an important and honest result.

The project should not claim that PPO solved the portfolio optimization problem. A better claim is that PPO was successfully connected to the signal-based portfolio environment and produced reasonable exposure behavior, but more tuning is needed.

---

## 11. PPO Training Instability

Reinforcement learning is difficult to apply to financial markets.

PPO was sensitive to:

- Reward function design
- Action space design
- State feature selection
- Hyperparameters
- Transaction cost assumptions
- Market regime changes
- Training period selection
- Random initialization

Financial rewards are noisy and delayed. A good decision can lose money by chance, and a bad decision can make money by chance. This makes it hard for PPO to learn stable behavior.

Earlier PPO versions that attempted direct stock-weight selection or more aggressive long-short behavior were harder to train consistently.

Because of this, the final version used PPO as an exposure controller instead of a full stock-weight optimizer.

This made the problem more stable, but it also limited what PPO could do.

---

## 12. Simplified Action Space

The final PPO action space was intentionally simplified.

Instead of allowing PPO to choose every individual stock weight, PPO selected an exposure level for a Top-10 signal basket.

Example exposure levels included:

- 60%
- 75%
- 90%
- 100%

This made the environment easier to train and explain, but it also reduced flexibility.

The PPO agent could not fully decide:

- Which stocks to hold
- Exact stock-level weights
- Whether to hold cash dynamically beyond the exposure choices
- Whether to short stocks
- Whether to rotate between sectors
- Whether to hedge market exposure directly

This limitation was intentional for a first version, but future versions could explore more flexible action spaces.

---

## 13. Reward Function Limitations

The PPO reward function attempted to balance return, transaction costs, and drawdown/risk behavior.

However, reward design is difficult.

If the reward focuses too much on return, the agent may take too much risk. If the reward penalizes risk too heavily, the agent may avoid exposure and fail to learn useful behavior.

A stronger reward function could include:

- Volatility penalty
- Drawdown penalty
- Turnover penalty
- Risk-adjusted return
- Benchmark-relative return
- Exposure smoothness penalty
- Downside-risk penalty

The reward function likely needs more experimentation before PPO can be fairly compared against strong static baselines.

---

## 14. State Feature Limitations

The PPO state used signal and market features, but the state may not fully represent the market environment.

A financial RL agent can only learn from what is included in the state.

Missing state information may include:

- News sentiment
- Earnings dates
- Analyst revisions
- Options-market data
- Macro announcements
- Sector rotation indicators
- Interest-rate expectations
- Market breadth
- Intraday order-flow information

Without these features, PPO may not have enough context to make better exposure decisions than a simple static rule.

---

## 15. Missing News and Event Data

The current version did not fully include news sentiment or event-based signals.

This is a major limitation because many stock moves are caused by new information, not just historical price and volume behavior.

Important missing information could include:

- Earnings reports
- Company headlines
- Analyst upgrades
- Analyst downgrades
- SEC filings
- Product announcements
- Legal news
- Merger and acquisition rumors
- Federal Reserve announcements
- Inflation reports
- Jobs reports

A future version could use an NLP model or financial news API to create a daily news-impact score for each stock.

That score could be added to the supervised model and PPO state.

---

## 16. Backtest Simplification

The project used research-style backtesting and portfolio comparison.

This was useful for evaluating the model, but it was not a full production-grade backtest.

A more complete backtest would need:

- Exact trade execution timestamps
- Realistic order types
- Bid-ask spread modeling
- Liquidity constraints
- Corporate action handling
- Survivorship bias checks
- Delisting handling
- Borrow cost modeling for shorts
- Cash drag
- Tax considerations
- Portfolio risk limits
- Position concentration limits

Because these details were simplified, the results should be interpreted as research results, not production trading results.

---

## 17. Survivorship Bias Risk

Survivorship bias can happen if the ticker universe only includes stocks that survived until the end of the dataset.

If failed, delisted, acquired, or removed stocks are missing, the backtest may look better than reality.

The project focused on a liquid ticker universe, but survivorship bias is still a possible limitation unless the dataset explicitly includes historical constituents and delisted names.

A stronger version would use a point-in-time universe.

---

## 18. One Universe of Stocks

The project used a selected universe of liquid stocks and ETFs.

This helped keep the data cleaner, but it also means the results may depend on the chosen universe.

The strategy might perform differently on:

- Small-cap stocks
- Illiquid stocks
- Only S&P 500 names
- Only technology stocks
- Only ETFs
- International stocks
- Crypto assets
- Options
- Futures

Future work could test whether the signal holds across different asset universes.

---

## 19. Model Overfitting Risk

Financial models can overfit easily.

Overfitting happens when a model learns patterns that exist in the training data but do not generalize to future data.

Possible overfitting risks included:

- Too many engineered features
- Multiple target comparisons
- Hyperparameter tuning on limited data
- Repeated testing on the same years
- Selecting the best-looking model after many experiments
- Building features that accidentally match one market regime

The project used time-aware testing to reduce this risk, but overfitting remains a major concern.

A future version should include stronger validation methods and statistical significance testing.

---

## 20. Need for Statistical Significance Testing

The project compared model performance across years and baselines, but more statistical testing would strengthen the results.

Useful future tests could include:

- Random basket comparison
- Bootstrap confidence intervals
- Permutation tests
- Sharpe ratio significance tests
- Drawdown comparison
- Top-minus-bottom spread significance
- Transaction cost sensitivity analysis

This would help determine whether the observed signal is statistically meaningful or possibly due to chance.

---

## 21. Reproducibility Limitations

The public repository does not include the full code, raw data, or API credentials.

This means the project is not fully reproducible from the public GitHub version.

This was intentional because the original work used:

- Paid historical market data
- Private data-processing paths
- Large intermediate files
- Local and Colab workflows
- Private credentials

The public repository is meant to present the project as a case study, not as a fully executable open-source trading system.

---

## 22. Public Repository Limitation

Because the public GitHub repository does not include the full implementation code, recruiters or reviewers cannot run the full pipeline directly.

To reduce this issue, the repository includes:

- A detailed README
- Methodology documentation
- Results documentation
- Limitations documentation
- Synthetic sample data
- Feature descriptions
- Result summaries
- Visual outputs

This makes the project understandable without exposing private code or paid data.

---

## 23. Ethical and Practical Limitations

This project should not be used to encourage risky trading behavior.

Machine learning models can create false confidence, especially in finance. Even if a model performs well historically, future market behavior can be different.

The project should be framed as:

- A machine learning research project
- A portfolio optimization experiment
- A reinforcement learning prototype
- A signal evaluation system

It should not be framed as:

- A guaranteed trading bot
- A finished investment strategy
- A financial advice system
- A risk-free way to make money

---

## 24. Best Honest Interpretation

The best honest interpretation of the project is:

**The project found useful ranking signal in some future-return windows and successfully connected PPO to a signal-based portfolio environment. However, PPO did not clearly beat the strongest simple Top-10 baselines, and the results require longer out-of-sample testing, better execution modeling, and more robust validation before any real trading claim could be made.**

This interpretation is strong because it shows technical ability without exaggerating the result.

---

## 25. Future Improvements

The main improvements needed are:

- Add longer historical testing
- Use more walk-forward periods
- Add news sentiment and event data
- Improve PPO reward design
- Test more PPO action spaces
- Add more realistic transaction cost modeling
- Add slippage and bid-ask spread assumptions
- Add random and factor-model baselines
- Add statistical significance testing
- Test across different market regimes
- Use point-in-time ticker universes
- Improve reproducibility with cleaner public pseudocode

These improvements would make the project stronger and more reliable.

---

## 26. Summary

The project was successful as a research prototype, but it has clear limitations.

The strongest parts of the project were:

- End-to-end pipeline design
- Feature engineering
- Target comparison
- Time-aware evaluation
- Leakage awareness
- Basket-based signal testing
- PPO environment design
- Honest baseline comparison

The main limitations were:

- Limited historical data
- One-year final PPO test
- Simplified execution assumptions
- PPO instability
- Missing news/event features
- PPO not clearly outperforming the best simple baselines
- Public repository not fully reproducible without private data and code

Overall, these limitations show that the project is a strong learning and research project, but not a finished production trading system.
