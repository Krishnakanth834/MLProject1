NSE Stock Price Prediction (1-Month Horizon)

1. Project Overview

    This project builds a Machine Learning pipeline to predict the percentage return of National Stock Exchange (NSE) stocks over a 1-month (21 trading days) horizon. Unlike simple "Up/Down" classification, this model solves a Regression problem, attempting to forecast the exact magnitude of price movement (e.g., +5.2% or -3.1%).

    The system uses historical data, technical indicators, and a rigorous Train-Validation-Test split to ensure robust performance on unseen market data.

2. Methodology & Feature Engineering

    Stock prices are non-stationary (values change over time, e.g., ₹200 vs ₹3000). To handle this, we do not use raw prices as inputs. Instead, we generate normalized technical indicators and ratios using the ta library.

    The Input Features (X)

    | Category      | Indicators Used                          | Why?                                                                 |
    |---------------|------------------------------------------|----------------------------------------------------------------------|
    | Momentum      | RSI (14), Stochastic Oscillator          | Identifies overbought and oversold conditions.                       |
    | Trend         | MACD, ADX, SMA_50                        | Captures the direction and strength of the current trend.            |
    | Volatility    | ATR (Average True Range)                 | Measures market “fear” or candle size regardless of direction.       |
    | Price Action  | High/Low Ratio, Gap Up/Down              | Custom ratios to capture intraday volatility and overnight sentiment.|
    | Volume        | Volume                                   | Confirms whether price moves are backed by trading activity.         |

    | 


    The Target Variable (y)
    Goal: Predict percentage return 21 days into the future.

    Formula: ((Close_Price_in_21_days - Today_Close_Price) / Today_Close_Price) * 100    

3. Model Architecture

    We implemented and competed three distinct algorithms to find the best predictor:

    Decision Tree Regressor (Baseline):

    Role: Establishes a baseline. Learns simple "if-then" rules.

    Risk: Highly prone to overfitting (memorizing specific past dates).

    Random Forest Regressor (Ensemble):

    Role: Reduces variance. Builds hundreds of parallel trees and averages their predictions.

    Strength: Handles noisy stock data better than a single tree.

    XGBoost Regressor (Gradient Boosting):

    Role: The Optimizer. Builds trees sequentially, where each new tree specifically corrects the errors of the previous one.

    Strength: Excellent at finding subtle, non-linear patterns in time-series data.    

4. Training Strategy (The "Dar" Method)

    To prevent "Look-ahead Bias" (using future data to predict the past), we strictly used a Chronological Split:

    Training Set (60%): Earliest data. Used for the model to learn patterns.

    Validation Set (20%): Middle data. Used strictly for Hyperparameter Tuning. We ran a Grid Search (testing various depths, learning rates) to pick the winning parameters here.

    Test Set (20%): Most recent data. Kept completely unseen until the final evaluation.    

5. Experimental Results

    Performance Metric: Mean Absolute Error (MAE)
    Definition: The average gap (in percentage points) between the Predicted Return and the Actual Return.

    Example: If MAE is 4.5% and we predict +10%, the real return is likely between 5.5% and 14.5%.   


Final Outcomes

    After automated tuning, XGBoost emerged as the winner.

    Winner: XGBoost Regressor

    Best Parameters:

    learning_rate: 0.01 (Very slow learning to filter noise)

    max_depth: 3 (Shallow trees to prevent overfitting)

    n_estimators: 50

    Scores:

    Train MAE: 4.57%

    Validation MAE: 4.35%

    Final Test MAE: 4.53%


Interpretation

    The model achieved a highly consistent error rate (~4.5%) across Training, Validation, and Test datasets.

    Success: The consistency (Train ≈ Test) proves the model did not overfit. It successfully generalized to new, unseen market conditions.

    Insight: The selection of a low learning rate (0.01) and shallow depth (3) was critical. It forced the model to ignore daily market noise and focus only on the strongest signals.

6. Future Roadmap

    Deep Learning: Implement LSTM (Long Short-Term Memory) networks to better capture sequential dependencies over time.

    Sentiment Analysis: Integrate NLP on financial news headlines to account for external shocks.

    Macro Features: Add Nifty50 Index correlation and Option Chain (PCR) data.    