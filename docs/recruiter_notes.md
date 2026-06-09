# Recruiter Notes

This document is a recruiter-facing summary for the **Machine Learning Portfolio Optimization Research** project.

The goal of this file is to make the project easier to explain in resumes, LinkedIn messages, interviews, and job applications. It is written in a clear and honest way so the project sounds strong without making unrealistic trading claims.

---

## 1. Short Recruiter Summary

**Machine Learning Portfolio Optimization Research** was a research-style project where I built a time-aware machine learning pipeline for stock ranking, signal evaluation, and PPO-based portfolio exposure control using historical daily and extended-hours market data.

The project included data cleaning, feature engineering, target comparison, supervised signal modeling, walk-forward evaluation, Top-10 basket construction, and reinforcement learning experiments using PPO.

The strongest part of the project was building an end-to-end financial ML pipeline while carefully avoiding data leakage and comparing the model against simple baselines.

---

## 2. One-Sentence Resume Version

Built a research-style machine learning portfolio optimization pipeline using historical daily and extended-hours market data, engineered market/sector/volatility/liquidity features, evaluated stock-ranking signals with time-aware walk-forward testing, and tested PPO for portfolio exposure control against SPY and Top-10 basket baselines.

---

## 3. Short Resume Bullet Version

- Built a financial ML research pipeline for stock ranking and portfolio exposure control using historical daily and extended-hours market data.
- Engineered momentum, volatility, liquidity, market-regime, sector-regime, and premarket/after-hours features.
- Trained supervised signal models and evaluated Top-10 vs baseline basket performance using time-aware walk-forward testing.
- Tested PPO as an exposure controller for a signal-based Top-10 portfolio while accounting for transaction costs and drawdown behavior.
- Compared PPO against SPY, equal-weight Top-10, and inverse-volatility Top-10 baselines, showing useful signal but also identifying limits of RL in noisy financial data.

---

## 4. Stronger Resume Bullet Version

- Developed an end-to-end financial machine learning research pipeline for stock ranking, signal generation, and PPO-based portfolio exposure control across a multi-stock universe.
- Built feature engineering workflows for price momentum, volume/liquidity, volatility, market regime, sector regime, and extended-hours trading behavior.
- Designed time-aware train/validation/test splits and walk-forward evaluation to reduce data leakage and test signal generalization across market periods.
- Evaluated supervised ranking models through Top-10 basket performance, top-minus-bottom spread, and baseline comparisons rather than relying only on classification accuracy.
- Implemented PPO as an interpretable exposure controller for a signal-ranked Top-10 basket and compared it against SPY, equal-weight, and inverse-volatility portfolio baselines.

---

## 5. LinkedIn / GitHub Project Description

This project explores machine learning and reinforcement learning for stock ranking and portfolio exposure control using historical daily and extended-hours market data.

I built a research pipeline that cleaned market data, engineered features across momentum, volatility, liquidity, market regime, sector regime, and extended-hours behavior, and tested multiple future-return targets. The supervised learning portion focused on ranking stocks into stronger and weaker baskets using time-aware walk-forward evaluation. The reinforcement learning portion used PPO as an exposure controller for a Top-10 signal basket rather than trying to directly optimize every stock weight.

The project showed useful ranking signal in some prediction windows and demonstrated how PPO can be connected to a real financial ML pipeline. However, the results are framed as research findings, not a production trading system. PPO beat SPY in the 2025 test comparison but did not clearly outperform the strongest simple Top-10 baselines, which made baseline testing and honest interpretation a key part of the project.

---

## 6. 30-Second Interview Pitch

I worked on a machine learning portfolio optimization research project using historical market data. I built a pipeline that cleaned daily and extended-hours stock data, engineered features like momentum, volatility, liquidity, market regime, sector regime, and premarket/after-hours signals, then trained models to rank stocks.

Instead of only reporting accuracy, I evaluated whether the model could separate stronger Top-10 baskets from weaker baskets using time-aware walk-forward testing. I also tested PPO as a reinforcement learning exposure controller, where the model selected how much exposure to take in a signal-ranked basket.

The main lesson was that financial ML is very sensitive to leakage, time splits, transaction costs, and baselines. PPO worked as a prototype and beat SPY in the test plot, but the simple Top-10 baselines were still very competitive, so I treated it as a research system rather than a finished trading bot.

---

## 7. 60-Second Interview Pitch

One of my bigger projects was a machine learning portfolio optimization research system. I used historical daily and extended-hours market data and built a feature pipeline around momentum, volume, liquidity, volatility, market regime, sector regime, and premarket/after-hours behavior.

The first part of the project was supervised learning. I compared multiple future-return targets, because predicting close-to-next-open movement is different from predicting open-to-close or multi-day movement. Then I trained signal models and evaluated them in a time-aware way so the model was always trained on earlier periods and tested on later periods.

The second part was reinforcement learning. I tested PPO for portfolio decisions, but I found that directly letting PPO choose every stock weight was too unstable. So I redesigned PPO as an exposure controller. The supervised model selected the Top-10 stocks, and PPO chose whether to take lower or higher exposure to that basket.

The most important result was not that PPO solved trading. It did not clearly beat the strongest simple Top-10 baselines. But the project showed an end-to-end pipeline with careful leakage prevention, realistic baseline comparison, and honest evaluation. It gave me experience with financial ML, feature engineering, walk-forward testing, and reinforcement learning in a noisy real-world environment.

---

## 8. Technical Skills This Project Shows

This project demonstrates experience with:

- Python
- pandas
- NumPy
- scikit-learn
- XGBoost-style modeling
- Time-series feature engineering
- Financial market data
- Parquet data workflows
- Data cleaning
- Feature pipelines
- Classification and ranking models
- Walk-forward validation
- Train/validation/test split design
- Data leakage prevention
- Portfolio construction
- Reinforcement learning
- PPO environment design
- Baseline comparison
- Model evaluation
- Result interpretation
- Research documentation

---

## 9. Best Way to Explain Why the Code Is Not Public

Use this explanation if a recruiter asks why the repository does not include full code:

The public GitHub repository is a case-study version of the project. I did not include the full code because the original implementation used paid historical market data, private API credentials, local file paths, and large intermediate datasets. I kept the public version focused on methodology, results, limitations, and project design so it can be reviewed safely without exposing private data or credentials.

A shorter version:

The full implementation used paid data access and private data-processing pipelines, so I published a case-study version with the methodology, results, limitations, and synthetic examples instead of exposing raw data or credentials.

---

## 10. What to Say If They Ask Whether It Is a Trading Bot

Do not call it a trading bot.

Use this answer:

No, I would not describe it as a production trading bot. I would describe it as a financial machine learning research pipeline. It tested whether engineered market features could rank stocks into stronger and weaker baskets, and then tested PPO as an exposure controller on top of those signals. The goal was research and evaluation, not live trading.

---

## 11. What to Say If They Ask Whether It Was Profitable

Use this answer:

The signal-based baskets performed better than SPY in the 2025 test comparison, and PPO also beat SPY in the test plot. However, PPO did not clearly outperform the strongest simple Top-10 baselines. So I would not claim it is a finished profitable strategy. The honest result is that the feature pipeline found useful signal, but more testing, better execution modeling, and longer out-of-sample validation would be needed before making any real trading claim.

---

## 12. What to Say If They Ask What Was Hard

The hardest part was avoiding data leakage. In financial data, it is very easy to accidentally include future information through rolling returns, shifted targets, cross-sectional ranks, or train/test splits. I had to think carefully about what information would actually be available at prediction time.

Another hard part was PPO stability. Financial rewards are noisy, and the model can learn unstable behavior if the action space is too large or if the reward function is not designed carefully. That is why I changed the PPO design from direct allocation to exposure control.

---

## 13. What to Say If They Ask Why PPO Was Used

Use this answer:

I used PPO because I wanted to test reinforcement learning for portfolio decision-making, but I learned that directly applying PPO to stock weights is too unstable for a first version. So I used PPO as an exposure controller. The supervised model ranked the stocks, and PPO learned when to take more or less exposure to the Top-10 basket. That made the RL problem smaller, more interpretable, and easier to evaluate.

---

## 14. What to Say If They Ask Why Not Just Use Supervised Learning

Use this answer:

The supervised model was better for ranking stocks, but PPO was used to test the decision-making layer. The supervised model answered, “Which stocks look strongest based on the signal?” PPO answered, “How much exposure should the portfolio take based on the current state?” This separated signal generation from portfolio control.

---

## 15. What to Say If They Ask About Data Leakage

Use this answer:

Data leakage was one of the biggest concerns. I used time-aware splits instead of random splits, kept targets separate from input features, shifted rolling features carefully, and checked for suspicious future-return columns. The goal was to make sure the model only used information that would have been available before the prediction window.

---

## 16. What to Say If They Ask About Baselines

Use this answer:

I compared the PPO strategy against SPY and simple Top-10 basket baselines, including equal-weight and inverse-volatility versions. This was important because a complex model is not automatically useful unless it beats simple rules. In my test, PPO beat SPY but did not clearly beat the strongest Top-10 baselines, which showed that the signal basket was strong but PPO needed more tuning.

---

## 17. What to Say If They Ask About the Main Result

Use this answer:

The main result was that some engineered signals had useful predictive behavior, especially for certain gap and extended-hours movement windows. The Top-10 signal basket performed well against SPY, and PPO worked as a first exposure-control prototype. However, PPO was not clearly better than the strongest simple baselines, so the result should be treated as a strong research prototype rather than a completed trading strategy.

---

## 18. What to Say If They Ask What You Would Improve

Use this answer:

I would improve the project by adding longer out-of-sample testing, more realistic slippage and transaction cost modeling, news sentiment features, earnings calendar features, stronger factor baselines, and statistical significance testing. I would also test multiple PPO reward functions and compare long-only, exposure-control, and long-short PPO designs.

---

## 19. What to Avoid Saying

Avoid saying:

- I built a trading bot.
- The model predicts the market.
- The strategy is profitable.
- PPO beats the market.
- This is production-ready.
- The model can pick winning stocks.
- The model guarantees returns.
- Hedge funds only need 60% accuracy.
- Accuracy above 60% means it will make money.

These statements can sound unrealistic or overclaimed.

---

## 20. Better Words to Use

Use these phrases instead:

- Research-style portfolio optimization project
- Financial machine learning pipeline
- Stock-ranking signal evaluation
- Time-aware walk-forward testing
- PPO exposure-control prototype
- Baseline comparison
- Signal-based Top-10 basket
- Experimental reinforcement learning environment
- Not a production trading system
- Useful signal but more validation needed
- Strong research prototype

---

## 21. STAR Interview Answer

### Situation

I wanted to explore whether machine learning and reinforcement learning could be applied to portfolio decision-making using real historical market data.

### Task

My goal was to build a pipeline that could clean market data, engineer useful features, train signal models, evaluate ranking performance, and test PPO for exposure control.

### Action

I built feature pipelines for momentum, volatility, liquidity, market regime, sector regime, and extended-hours behavior. I compared multiple prediction targets, used time-aware train/test splits, trained supervised signal models, ranked stocks into Top-10 baskets, and tested PPO as an exposure controller.

### Result

The project found useful signal in some target windows and showed that signal-based Top-10 baskets could outperform SPY in the 2025 test comparison. PPO also beat SPY, but it did not clearly outperform the strongest simple Top-10 baselines. The final result was a working end-to-end research pipeline with honest limitations and clear future improvements.

---

## 22. Recruiter-Friendly Project Title Options

Good GitHub or resume titles:

- Machine Learning Portfolio Optimization Research
- Financial ML Stock Ranking and PPO Exposure Control
- Stock Ranking and Reinforcement Learning Portfolio Research
- ML-Based Signal Evaluation for Portfolio Allocation
- PPO Exposure Control for Signal-Based Stock Baskets

Best title:

**Machine Learning Portfolio Optimization Research**

This title sounds professional and does not overclaim.

---

## 23. Resume Project Section Example

### Machine Learning Portfolio Optimization Research

Built a research-style ML pipeline for stock ranking, signal evaluation, and PPO-based portfolio exposure control using historical daily and extended-hours market data. Engineered momentum, volatility, liquidity, market-regime, sector-regime, and premarket/after-hours features, then evaluated supervised ranking models through time-aware walk-forward testing. Tested PPO as an exposure controller for a signal-ranked Top-10 basket and compared performance against SPY, equal-weight, and inverse-volatility baselines. The project emphasized data leakage prevention, baseline comparison, transaction cost awareness, and honest evaluation of reinforcement learning limitations in financial markets.

---

## 24. Short LinkedIn Message Version

I recently completed a machine learning portfolio optimization research project where I built a time-aware pipeline for stock ranking, signal evaluation, and PPO-based portfolio exposure control using historical market data. The project focused on feature engineering, leakage prevention, walk-forward evaluation, baseline comparison, and reinforcement learning in a noisy financial environment.

---

## 25. Interview Follow-Up Message

Hi [Name],

Thank you for taking the time to speak with me. I also wanted to share one of my recent machine learning projects that connects supervised learning, financial time-series feature engineering, and reinforcement learning.

The project is a research-style portfolio optimization system. I built a pipeline for cleaning historical daily and extended-hours stock data, engineering market and sector features, evaluating stock-ranking signals with time-aware walk-forward testing, and testing PPO as an exposure controller for a signal-based Top-10 basket.

The public GitHub version is a case study because the original implementation used paid historical market data and private API credentials. I included the methodology, results, limitations, and project design so the work can still be reviewed clearly.

Best,  
Ash Sonar

---

## 26. Best Final Framing

The best way to frame this project is:

**This was not a finished trading bot. It was a research project where I built a full financial ML pipeline, tested stock-ranking signals, connected PPO to a portfolio exposure environment, compared against simple baselines, and learned how difficult it is to validate models on noisy time-series data.**

That framing is honest, technical, and strong for jobs.
