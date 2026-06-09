# Methodology

This document explains the methodology used in the **Machine Learning Portfolio Optimization Research** project.

The project was designed as a research-style machine learning pipeline for stock ranking, signal evaluation, and PPO-based portfolio exposure control. The main goal was not to claim that the stock market can be perfectly predicted. The goal was to test whether engineered historical market features could produce useful ranking signals and whether reinforcement learning could improve portfolio exposure decisions on top of those signals.

---

## 1. Research Question

The project started with a practical research question:

**Can historical price, volume, volatility, market-regime, sector-regime, and extended-hours features help rank stocks into stronger and weaker baskets?**

A second research question was added later:

**Can a PPO reinforcement learning agent improve exposure decisions after a supervised model has already selected a signal-based stock basket?**

This separated the project into two connected parts:

1. **Supervised signal modeling**  
   Build prediction and ranking signals from engineered features.

2. **Reinforcement learning portfolio control**  
   Use PPO to decide how much exposure to take in a Top-10 signal basket.

This structure was important because asking PPO to directly solve the entire stock market allocation problem was too large and unstable for a first version.

---

## 2. Data Collection and Preparation

The project used historical stock market data covering approximately five years. The original dataset included daily market data and extended-hours data from premarket and after-hours trading.

The ticker universe focused on liquid stocks and ETFs. This helped reduce issues from thinly traded assets, missing data, and unreliable price movement.

The raw data included fields such as:

- Date
- Ticker
- Open price
- High price
- Low price
- Close price
- Volume
- VWAP-style information
- Transaction information
- Premarket price behavior
- After-hours price behavior
- Extended-hours volume behavior

The raw files were cleaned and converted into structured datasets. The cleaned datasets were saved in Parquet format because Parquet is efficient for larger tabular datasets and works well with pandas-based machine learning workflows.

The real raw data is not included in this repository because it came from paid historical market data access.

---

## 3. Data Cleaning

Before feature engineering, the data had to be cleaned and standardized.

The cleaning process included:

- Converting dates into a consistent datetime format
- Sorting rows by ticker and date
- Removing or filtering invalid rows
- Checking ticker coverage
- Converting numeric columns into usable numeric types
- Handling missing values
- Removing assets with poor historical coverage
- Saving cleaned intermediate files

This step was important because financial data can contain missing dates, incorrect values, gaps in ticker history, and inconsistent formatting.

A weak cleaning process can create misleading model results, especially if missing data is handled differently across training and testing periods.

---

## 4. Feature Engineering Overview

Feature engineering was the main technical part of the project.

The goal was to convert raw market data into useful signals that describe how each stock was behaving before the prediction target.

The feature groups included:

1. Price and momentum features  
2. Volume and liquidity features  
3. Volatility features  
4. Market-regime features  
5. Sector-regime features  
6. Premarket and after-hours features  
7. Cross-sectional ranking features  

Each feature was designed to use only information that would have been available before the prediction period. This was important for avoiding lookahead bias.

---

## 5. Price and Momentum Features

Price and momentum features were used to describe recent stock movement.

These features helped answer questions such as:

- Has the stock been moving upward recently?
- Is the stock showing short-term strength?
- Is the stock reversing after a large move?
- Is the stock outperforming or underperforming the broader market?

Examples of price and momentum features included:

- Daily return
- Rolling return windows
- Gap movement
- Close-to-close movement
- Short-term momentum
- Multi-day momentum
- Stock return relative to SPY or other benchmarks

These features were useful because short-term price behavior can sometimes contain momentum or reversal patterns.

However, these features also had a high risk of leakage if future returns were accidentally included. Because of that, return windows had to be shifted carefully.

---

## 6. Volume and Liquidity Features

Volume and liquidity features were used to measure trading activity.

These features helped answer questions such as:

- Is the stock trading more than usual?
- Is the stock liquid enough to include in the universe?
- Is unusual volume appearing before a price move?
- Is the stock easier or harder to trade realistically?

Examples included:

- Daily volume
- Dollar volume
- Rolling average volume
- Relative volume
- Volume rank
- Liquidity filters
- Volume change from recent averages

Liquidity mattered because a strategy that looks good on illiquid stocks may not be realistic after transaction costs, slippage, and execution limits.

---

## 7. Volatility Features

Volatility features were used to measure risk and instability.

These features helped answer questions such as:

- Is the stock moving more than usual?
- Is the stock entering a high-volatility period?
- Is the stock too unstable for a simple signal?
- Should the portfolio reduce exposure to the stock?

Examples included:

- Daily range
- High-low range
- Rolling volatility
- Extended-hours volatility
- Recent return standard deviation
- Relative volatility compared to the market

Volatility features were also important for portfolio construction because inverse-volatility weighting was used as one of the baseline approaches.

---

## 8. Market-Regime Features

Market-regime features were included because individual stock movement depends heavily on the broader market environment.

A stock signal can behave very differently during:

- Strong bull markets
- Market corrections
- High-volatility periods
- Low-volatility periods
- Sector rotation periods
- Broad risk-on or risk-off environments

Market features included information from broad ETFs such as SPY, QQQ, IWM, and DIA.

Examples included:

- SPY return
- QQQ return
- IWM return
- Rolling market volatility
- Market momentum
- Stock performance relative to the market

These features helped the model understand whether a stock was moving because of its own behavior or because the whole market was moving.

---

## 9. Sector-Regime Features

Sector-regime features were used to add context about industry-level movement.

A stock can look strong in isolation but weak compared to its sector. A stock can also look weak in isolation but still be outperforming a falling sector.

Examples of sector context included:

- Sector ETF return
- Sector momentum
- Sector volatility
- Stock return relative to sector movement
- Sector-adjusted ranking behavior

This helped the model compare stocks more fairly by considering the environment around each stock.

---

## 10. Extended-Hours Features

The project also included premarket and after-hours features.

This was important because important information often affects prices outside regular trading hours. Earnings, news releases, analyst updates, and macro events can cause movement before the normal market session opens.

Examples of extended-hours features included:

- Premarket return
- After-hours return
- Extended-hours volume
- Extended-hours volume share
- Premarket volatility
- After-hours volatility
- Extended-hours price range

These features were used to test whether after-hours and premarket behavior contained useful predictive information for future return windows.

---

## 11. Target Engineering

The project did not rely on only one prediction target.

Several future movement windows were created and compared.

Targets included:

- Close-to-next-open return
- Next open-to-close return
- Next close-to-close return
- Two-day close-to-close return
- Five-day close-to-close return
- After-hours-to-next-open return

This was important because stock prediction depends heavily on the time window being predicted.

For example, predicting overnight movement may require different information than predicting multi-day return. A feature that helps with after-hours movement may not help with five-day movement.

Testing multiple targets helped avoid building the whole project around one noisy label.

---

## 12. Time-Aware Splitting

The project used time-aware train, validation, and test splits.

This was one of the most important parts of the methodology.

For financial data, random splitting is usually inappropriate because it can mix future rows into the training set. That can create overly optimistic results and make the model appear stronger than it really is.

The project used chronological splitting, where earlier years were used for training and later years were used for validation or testing.

A simplified version of the split idea was:

- Train on earlier historical years
- Validate on a later year
- Test on the final unseen period

The final PPO evaluation focused on an unseen 2025 test period.

This helped make the evaluation more realistic, although one year is still a limited out-of-sample period for financial modeling.

---

## 13. Leakage Prevention

Avoiding leakage was a major concern throughout the project.

Leakage can happen when a model accidentally receives information from the future.

Examples of leakage risks included:

- Rolling return features that were not shifted correctly
- Future return targets accidentally included as features
- Cross-sectional ranks computed with future information
- Market-relative features using future market movement
- Train/test rows mixed randomly
- Features calculated after the prediction target

To reduce this risk, the project focused on:

- Sorting by ticker and date
- Using shifted feature windows
- Keeping prediction targets separate from input features
- Using chronological train/test splits
- Reviewing feature columns for accidental future-return information
- Removing suspicious return-based columns when needed

This was one of the most important technical lessons from the project.

---

## 14. Supervised Modeling

The supervised learning portion was used to create stock prediction and ranking signals.

The project tested models that could estimate whether a stock had a higher probability of positive future movement under different target definitions.

The supervised models were not treated as final trading systems by themselves. Instead, their output was used as a signal-generation layer.

The model outputs were used to produce:

- Probability scores
- Ranking scores
- Composite signal scores
- Top-ranked stock baskets
- PPO-ready input features

The purpose was to identify stronger candidate stocks each day, not to predict the exact future price of every stock.

---

## 15. Multi-Signal Modeling

After comparing different targets, multiple prediction signals were combined into a broader signal dataset.

This created a more flexible input for portfolio decision-making.

The idea was that no single target captures the full market behavior. Combining several related signals can give a stronger view of stock behavior across different time windows.

The multi-signal dataset was used to support the PPO portfolio environment.

The final signal score was used to rank stocks each day and select the Top-10 basket.

---

## 16. Basket Construction

Instead of evaluating only individual predictions, the project evaluated ranked baskets.

Each day, stocks were ranked by their composite signal score.

The highest-ranked stocks formed a Top-10 basket.

This basket-based approach was useful because portfolio decisions are usually based on selecting a group of assets, not one isolated prediction.

The basket approach also allowed the project to compare:

- Top-ranked stocks
- Bottom-ranked stocks
- Top-minus-bottom spread
- Equal-weight Top-10 baseline
- Inverse-volatility Top-10 baseline
- PPO-controlled Top-10 exposure

This made the evaluation more connected to portfolio construction.

---

## 17. Baseline Portfolio Methods

Baselines were included so the PPO model would not be compared only against SPY.

The baseline methods included:

- SPY benchmark
- Equal-weight Top-10 basket
- Inverse-volatility Top-10 basket
- Static exposure versions of the Top-10 basket

These baselines were important because a complex reinforcement learning model should be compared against simple rules.

If a simple equal-weight or inverse-volatility basket performs as well as PPO, then PPO has not clearly added value yet.

This helped keep the interpretation honest.

---

## 18. PPO Environment Design

The reinforcement learning portion used PPO as an exposure controller.

The environment was designed around a signal-based Top-10 basket.

The supervised model selected the stocks. PPO selected the exposure level.

This simplified the reinforcement learning problem.

The PPO state included information such as:

- Composite signal strength
- Market features
- Volatility features
- Portfolio state information
- Previous exposure or allocation information

The PPO action was the exposure level applied to the Top-10 basket.

Example actions included:

- 60% exposure
- 75% exposure
- 90% exposure
- 100% exposure

The reward considered portfolio performance while also accounting for risk and trading costs.

---

## 19. Why PPO Was Not Used as a Full Stock-Weight Optimizer

Earlier versions tested more complex PPO designs.

Some versions attempted to let PPO directly choose individual stock weights or more aggressive long/short behavior.

These designs were harder to train because:

- The action space was larger
- The reward was noisy
- Portfolio returns were unstable
- Transaction costs had a larger effect
- The policy was sensitive to market regime changes
- The test period was limited

Because of this, the final design used PPO as an exposure controller.

This made the system easier to understand:

**The ranking model selected the basket. PPO decided how much exposure to take.**

This approach was more stable and easier to explain in a project or interview setting.

---

## 20. Reward Design

The PPO reward was designed to encourage better portfolio behavior.

The reward needed to consider more than raw return.

A useful portfolio reward should account for:

- Daily portfolio return
- Transaction costs
- Drawdown risk
- Exposure changes
- Volatility
- Risk-adjusted behavior

The exact reward design was part of the experimentation process.

The main lesson was that reward design has a large effect on PPO behavior. If the reward is too simple, PPO may take too much risk. If the reward is too restrictive, PPO may fail to learn useful exposure changes.

---

## 21. Evaluation Metrics

The project used multiple evaluation metrics instead of relying on one number.

Prediction metrics included:

- Accuracy
- AUC
- Target-specific model performance

Portfolio and ranking metrics included:

- Top basket return
- Bottom basket return
- Top-minus-bottom spread
- Top-beats-bottom rate
- Growth of $1
- Drawdown behavior
- Baseline comparison
- Transaction cost impact

This was important because classification accuracy alone does not prove that a trading strategy works.

A model can have decent directional accuracy and still lose money if the losing trades are larger than the winning trades or if transaction costs remove the edge.

---

## 22. Results Interpretation

The prediction experiments showed that some target windows had more useful signal than others. At least one movement window reached accuracy above 59%, which was notable for a noisy short-term stock direction problem.

However, the project did not treat this as proof of a profitable trading strategy.

The PPO exposure controller was tested on the unseen 2025 period. PPO performed better than SPY in the test plot, but it did not clearly outperform the strongest simple Top-10 baselines.

This meant the PPO setup worked as a first version, but the static baselines remained very competitive.

The most honest interpretation is:

- The feature and ranking pipeline found some useful signal.
- PPO could be connected to the signal-based portfolio environment.
- PPO was not clearly better than the best simple baselines yet.
- More tuning and longer out-of-sample testing are needed.

---

## 23. Limitations of the Methodology

The methodology had several limitations.

First, the dataset covered approximately five years. This was useful for experimentation, but still limited for financial modeling.

Second, the final PPO test focused on one unseen year. A longer out-of-sample test would be needed to make stronger claims.

Third, transaction costs and slippage were simplified. A real trading system would need more realistic execution assumptions.

Fourth, PPO was sensitive to reward design, action space design, and market conditions.

Fifth, the project did not yet include news sentiment or event-based features, which could be important for explaining sudden stock movement.

---

## 24. Future Methodology Improvements

Future versions of the methodology could improve the project by adding:

- Longer out-of-sample testing
- More walk-forward periods
- Random basket baselines
- Factor-model baselines
- More realistic transaction cost modeling
- Slippage and bid-ask spread estimates
- Better PPO reward functions
- Long-only PPO allocation
- Long-short PPO allocation
- News sentiment features
- Earnings calendar features
- Macro event features
- Statistical significance testing

These additions would help determine whether the signal is stable across different market environments.

---

## 25. Summary

The methodology followed a full end-to-end research pipeline:

```text
raw market data
→ cleaning
→ feature engineering
→ target comparison
→ supervised signal modeling
→ composite signal ranking
→ Top-10 basket construction
→ PPO exposure control
→ baseline comparison
→ results interpretation
```

The project showed that stock-market machine learning requires careful evaluation, not just model training.

The most important parts of the methodology were:

- Avoiding data leakage
- Using time-aware splits
- Comparing multiple targets
- Evaluating ranked baskets
- Comparing against simple baselines
- Treating PPO as an experimental exposure controller
- Being honest about limitations

The final result was a working research pipeline for financial machine learning and reinforcement learning portfolio experimentation.
