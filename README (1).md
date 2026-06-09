# Machine Learning Portfolio Optimization Research

A research-style machine learning and reinforcement learning project for stock ranking, signal evaluation, and PPO-based portfolio exposure control using historical daily and extended-hours market data.

This repository is a public case-study version of the project. The full implementation code, private data-processing pipeline, raw market data, and API credentials are intentionally not included because the original project used paid historical market data access and private local/Colab workflows.

> **Important:** This project is for research, experimentation, and portfolio-modeling education only. It is not financial advice and is not presented as a production trading system.

---

## Project Overview

The goal of this project was to explore whether historical stock market data and engineered trading features could help support portfolio decision-making.

Instead of trying to predict one perfect stock or present a guaranteed trading bot, the project focused on a more realistic research question:

**Can engineered market features help rank stocks into stronger and weaker baskets, and can reinforcement learning improve exposure decisions on top of those signals?**

The project combined supervised machine learning for signal generation with reinforcement learning for portfolio exposure control. The supervised models were used to create ranking and probability signals. A PPO reinforcement learning agent was then tested as an exposure controller for a signal-based Top-10 stock basket.

The full workflow included:

1. Historical market data collection and cleaning
2. Daily and extended-hours feature engineering
3. Target comparison across multiple future return windows
4. Multi-signal model training
5. Composite signal score generation
6. Top-10 basket construction
7. PPO reinforcement learning exposure control
8. Baseline comparison against SPY and simple Top-10 strategies
9. Results interpretation and limitations analysis

The main lesson from the project was that stock prediction is noisy and difficult, and that evaluation design matters as much as model choice. A model can appear useful under one target or one test year, but the signal must be tested carefully across time-aware splits and realistic portfolio assumptions.

---

## Motivation

Financial machine learning is difficult because stock prices are noisy, market regimes change, and small mistakes in the data pipeline can create misleading results.

A normal random train/test split is not appropriate for this kind of project because it can mix past and future data. That makes it easy for the model to accidentally learn information that would not have been available at prediction time.

Because of this, the project focused heavily on:

- Time-aware training and testing
- Avoiding lookahead bias
- Comparing multiple future-return targets
- Evaluating stock baskets instead of only single-stock predictions
- Comparing machine learning results against simple portfolio baselines
- Being honest about where PPO helped and where it did not

The project was built as a research system, not as a simple classification notebook.

---

## Dataset

The dataset was built from historical stock market data covering approximately five years. It included daily market data and extended-hours information from premarket and after-hours trading.

The ticker universe focused on liquid stocks and ETFs so that the project would use assets with cleaner historical coverage and enough trading activity.

The raw data included information such as:

- Open price
- High price
- Low price
- Close price
- Volume
- VWAP-style approximations
- Transaction information
- Premarket activity
- After-hours activity
- Extended-hours volume behavior

The processed data was saved into cleaned Parquet files and later combined into feature datasets and PPO-ready datasets.

The real dataset is not included in this repository because it came from paid historical market data access.

---

## Feature Engineering

A major part of the project was feature engineering. The goal was to create features that described price behavior, liquidity, volatility, market context, sector context, and extended-hours movement.

### Price and Momentum Features

These features captured recent stock movement and short-term trend behavior.

Examples:

- Daily return
- Rolling return windows
- Close-to-close movement
- Gap behavior
- Short-term momentum
- Relative performance against market benchmarks

### Volume and Liquidity Features

These features measured whether a stock had unusual trading activity or enough liquidity to be included in the model universe.

Examples:

- Daily volume
- Dollar volume
- Relative volume
- Rolling volume averages
- Volume rank
- Liquidity filters

### Volatility Features

These features described how unstable or risky a stock had been over recent windows.

Examples:

- Daily range
- Rolling volatility
- High-low range
- Intraday movement
- Extended-hours volatility summaries

### Market Regime Features

Market-wide behavior was included because individual stocks often move differently depending on the broader market environment.

Examples:

- SPY movement
- QQQ movement
- IWM movement
- Market rolling volatility
- Stock performance relative to the market

### Sector Regime Features

Sector-level context was also important because a stock can look strong or weak depending on how its sector is behaving.

Examples:

- Sector ETF movement
- Stock performance relative to sector
- Sector momentum
- Sector volatility

### Extended-Hours Features

Premarket and after-hours features were included because important price movement often happens outside regular trading hours.

Examples:

- Premarket return
- After-hours return
- Extended-hours volume
- Extended-hours volume share
- Premarket volatility
- After-hours volatility

This feature engineering step was one of the most important parts of the project because the model could only learn from the information represented in the feature set.

---

## Target Comparison

The project did not rely on only one future movement target.

Instead, several future-return windows were tested to see which type of movement had more predictive structure.

Targets included:

- Close-to-next-open movement
- Next open-to-close movement
- Next close-to-close movement
- Two-day close-to-close return
- Five-day close-to-close return
- After-hours-to-next-open movement

This was important because stock prediction is not one single problem. A model may perform differently depending on whether it is trying to predict overnight movement, intraday movement, next-day movement, or multi-day movement.

Testing multiple targets helped show which return windows had stronger signals and which ones were too noisy.

---

## Modeling Approach

The project used a staged modeling approach.

| Stage | Purpose | Why It Mattered |
|---|---|---|
| Target comparison | Tested multiple future movement windows | Avoided relying on one noisy target |
| Feature engineering | Built market, sector, momentum, volatility, and extended-hours signals | Gave the model richer market context |
| Multi-signal modeling | Combined prediction signals from multiple targets | Created stronger inputs for portfolio decisions |
| Basket ranking | Ranked stocks by composite signal score | Evaluated the model as a stock-ranking system |
| PPO exposure control | Used reinforcement learning to choose exposure level | Tested whether RL could improve risk-adjusted behavior |
| Baseline comparison | Compared PPO to SPY and simple Top-10 strategies | Prevented overclaiming model performance |

The supervised learning portion focused on ranking stocks rather than only predicting whether one stock would go up or down.

The model produced signal scores, and the highest-ranked stocks were placed into a Top-10 basket. This made the evaluation more realistic because portfolio construction usually depends on ranking opportunities, not just making one isolated prediction.

---

## Reinforcement Learning Design

For the reinforcement learning portion, PPO was tested as an exposure controller.

Instead of allowing PPO to directly choose every individual stock weight, the model first selected the Top-10 stocks each day using the composite signal score.

Then a base portfolio was created using inverse-volatility weighting.

The PPO agent chose the exposure level to apply to that basket.

Example exposure choices included:

- 60%
- 75%
- 90%
- 100%

This design made the reinforcement learning problem smaller and easier to interpret.

The PPO agent was not trying to solve the entire stock market. It was learning when to take more or less exposure to a signal-based basket while considering portfolio reward, transaction costs, and risk penalties.

---

## Why PPO Was Used as an Exposure Controller

Earlier PPO designs attempted to give the agent more direct control over the portfolio.

Some experiments explored ideas such as:

- Directly selecting weights across multiple stocks
- More aggressive long/short behavior
- Larger action spaces
- More flexible allocation decisions

These approaches were difficult to train consistently.

The main issues were:

- The action space became too large
- The reward signal was noisy
- The model became sensitive to transaction costs
- The policy reacted differently across market regimes
- The test period was limited
- Training stability was difficult to maintain

Because of this, the final PPO design was simplified.

The stock selection was handled by the supervised composite signal score, while PPO focused only on exposure control.

This made the result easier to explain:

**The supervised model selected what to buy. PPO learned how much exposure to take.**

---

## Evaluation Method

The project used time-aware evaluation because financial data must be tested in chronological order.

A normal random split would be misleading because it could allow future information to leak into the training set.

The evaluation approach focused on:

- Training on earlier time periods
- Testing on later unseen periods
- Comparing top-ranked and bottom-ranked baskets
- Measuring whether the model could separate stronger and weaker stocks
- Comparing the PPO strategy against simple baselines

The key evaluation metrics included:

- Accuracy for target prediction
- AUC for classification quality
- Top basket return
- Bottom basket return
- Top-minus-bottom spread
- Top-beats-bottom rate
- Growth of $1
- Drawdown behavior
- Transaction cost impact
- Baseline comparison against SPY and Top-10 rules

---

## Results Summary

The prediction experiments showed that at least one movement window reached accuracy above 59%.

This was a useful result because short-term stock direction prediction is noisy and difficult. However, accuracy alone does not prove that a trading strategy is profitable.

A model can be directionally correct more often than not and still lose money if:

- Losing trades are larger than winning trades
- Transaction costs remove the edge
- Slippage is too high
- The model works only during one market regime
- The test period is too short
- The strategy takes too much risk

Because of this, the project did not treat accuracy as the final result. The more important question was whether the signal could create stronger stock baskets and whether PPO could improve exposure decisions.

---

## PPO Baseline Comparison

The PPO exposure controller was tested on the unseen 2025 test period.

The PPO strategy performed better than SPY in the test plot, which suggested that the signal basket contained useful information.

However, PPO did not clearly outperform the strongest simple Top-10 baselines, such as:

- Equal-weight Top-10
- Inverse-volatility Top-10
- Higher static exposure Top-10 portfolios

This was an important result.

It showed that the PPO environment was functional, but the reinforcement learning policy still needed more tuning before it could be considered stronger than simple portfolio rules.

The PPO result should be interpreted as a first working reinforcement learning version, not as a finished trading strategy.

![PPO Baseline Comparison](images/ppo_baseline_comparison_2025.png)

---

## Main Takeaways

The main takeaway was that the supervised ranking system appeared more stable and easier to evaluate than the PPO agent.

PPO was useful for testing reinforcement learning on a real financial dataset, but the simpler Top-10 baselines remained very competitive.

This is common in financial machine learning. A complex model is not automatically better than a simple rule-based baseline.

The project showed that a working end-to-end pipeline could be built, but it also showed that more testing would be needed before using the strategy in a real trading environment.

---

## Technical Challenges

This project had several major technical challenges.

### Data Leakage

The biggest challenge was avoiding data leakage.

Some features that look useful can accidentally include future information if they are not shifted correctly. In financial ML, even a small leakage issue can make a model look much better than it really is.

The project required careful handling of:

- Rolling returns
- Future return targets
- Cross-sectional ranks
- Market-relative features
- Train/validation/test date boundaries
- Feature shifting
- Extended-hours timestamps

### Noisy Labels

Stock movement labels are extremely noisy.

A stock can move up or down for reasons that are not visible in historical price data, including:

- Earnings news
- Analyst upgrades
- Macro events
- Federal Reserve announcements
- Sector rotations
- Market-wide selloffs
- Unexpected company news

This makes the prediction task much harder than a normal supervised learning problem.

### Reinforcement Learning Instability

PPO was difficult to apply because financial rewards are noisy and delayed.

The agent had to learn from an environment where:

- Good decisions do not always produce immediate rewards
- Bad decisions can look good by chance
- Transaction costs can change the reward structure
- Market regimes can shift
- The best action may be different across time

This made PPO harder to tune than the supervised ranking models.

### Limited Test Period

The final PPO test focused on 2025 as an unseen test year.

This helped evaluate the model on future data, but one year is still limited for a stock-market problem. A longer out-of-sample test across different market regimes would be needed for stronger conclusions.

---

## Limitations

This project has several limitations.

First, the dataset covered approximately five years. That was enough for experimentation, but still limited for financial modeling because market behavior changes over time.

Second, the final PPO test period was only one unseen year. This means the PPO results may depend heavily on the specific market conditions in 2025.

Third, the project used simplified transaction cost assumptions. A production trading system would need more realistic assumptions for:

- Slippage
- Bid-ask spread
- Liquidity constraints
- Market impact
- Order execution
- Position sizing limits

Fourth, PPO did not clearly outperform the strongest static Top-10 baselines. This means the reinforcement learning system worked as a research prototype, but it was not yet a clearly superior trading method.

Finally, the project did not include news sentiment in the current version. Market data alone may not fully explain major stock movements.

---

## Future Work

The next major improvement would be adding news sentiment and daily news-impact features.

The idea would be to collect headlines or articles for each stock and use an AI model to estimate whether the news is positive, negative, neutral, or important.

That score could then be added into the PPO state so the agent could consider both market behavior and news-based information before choosing exposure.

Other future improvements include:

- Testing more PPO reward functions
- Trying different PPO hyperparameters
- Comparing long-only and long-short PPO designs
- Adding stronger transaction cost and slippage assumptions
- Expanding the test period
- Testing more unseen market regimes
- Adding factor-model baselines
- Comparing against random basket baselines
- Running statistical significance tests
- Improving portfolio constraints
- Testing transformer or sequence models for market state representation

---

## Skills Demonstrated

This project demonstrates experience with:

- Python data pipelines
- Financial time-series data
- Feature engineering
- Machine learning classification
- Stock ranking models
- Walk-forward evaluation
- Data leakage prevention
- Portfolio construction
- Reinforcement learning
- PPO environment design
- Baseline comparison
- Model interpretation
- Research-style project documentation

---

## Technologies Used

The private implementation used tools and libraries such as:

- Python
- pandas
- NumPy
- scikit-learn
- XGBoost
- Parquet data storage
- Gymnasium-style environment design
- Stable-Baselines3 PPO
- Matplotlib
- Google Colab
- Historical market data APIs

The full code is not included in this public repository.

---

## Repository Contents

This repository contains the public case-study version of the project.

```text
ml-portfolio-optimization-research/
│
├── README.md
├── LICENSE_NOTE.md
├── docs/
│   ├── methodology.md
│   ├── results.md
│   ├── limitations.md
│   └── recruiter_notes.md
│
├── images/
│   ├── walk_forward_signal_results.png
│   └── ppo_baseline_comparison_2025.png
│
├── data_samples/
│   ├── feature_dictionary.csv
│   └── synthetic_sample_rows.csv
│
└── results/
    └── ppo_2025_summary.csv
```

---

## What Is Not Included

The following items are intentionally not included:

- Raw historical market data
- Paid data-provider files
- API keys
- Private credentials
- Full implementation notebooks
- Local file paths
- Large intermediate datasets
- Trading automation code

This keeps the repository clean, safe, and appropriate for public viewing.

---

## Reproducibility Note

Some parts of the original project cannot be reproduced directly from this public repository because the original implementation depended on paid historical market data and private data-processing pipelines.

This repository is meant to communicate the project design, methodology, results, limitations, and technical learning process.

The full implementation can be discussed in an interview or shared privately in a cleaned format if needed.

---

## Conclusion

This project showed that stock prediction is difficult and that one simple up/down model is not enough.

The stronger approach was to combine multiple prediction signals, evaluate them across time-aware splits, and use the resulting composite score for portfolio decision-making.

The PPO model did not fully outperform the strongest simple baselines, but it showed how reinforcement learning can be connected to real financial data, engineered market features, transaction costs, and portfolio constraints.

The most important result was not that the project created a perfect trading strategy. The important result was that it produced a working end-to-end research pipeline:

**data collection → cleaning → feature engineering → target comparison → signal modeling → PPO exposure control → baseline comparison → results interpretation**

This project is a strong foundation for future work in financial machine learning, reinforcement learning, and portfolio optimization research.
