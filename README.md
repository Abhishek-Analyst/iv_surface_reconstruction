# NIFTY 50 Implied Volatility Surface Reconstruction

## 📌 Project Overview
This project tackles the reconstruction of an Implied Volatility (IV) surface using partially observed IV data for the NIFTY 50 options chain. In options markets, the IV surface is frequently incomplete, noisy, and irregular due to illiquidity and sparse trading. 

The objective of this pipeline is to predict the missing IV entries and reconstruct a realistic, consistent, and mathematically sound IV surface **without introducing look-ahead bias.**

## 🧠 Methodology: The "Volatility Smile" Engine
Time-series interpolation algorithms (like `pandas.interpolate(method='time')`) are notoriously dangerous in financial forecasting competitions because they peer into the future to fill past missing values (Look-Ahead Bias).

To achieve a robust out-of-sample Mean Squared Error (MSE), this solution strictly uses **Cross-Sectional Imputation**. 

### 1. Log-Moneyness Transformation
Instead of interpolating directly across raw strike prices (which ignores the relationship between the strike and the underlying price), the engine transforms strikes into **Log-Moneyness ($k$)**:
`k = ln(Strike / Underlying Price)`
This normalizes the data, centering the "At-The-Money" (ATM) options around 0, creating a highly stable foundation for polynomial regression.

### 2. Parabolic Smile Fitting (SVI-Inspired)
Options markets naturally exhibit a "Volatility Smile" (deep ITM and OTM options have higher implied volatility than ATM options). Linear interpolation fails at the tails, projecting straight lines that shoot to zero or infinity. 

This model fits a **2nd-degree polynomial (Parabola)** to the Log-Moneyness data per timestamp. This natively respects the U-shaped curvature of the volatility smile, generating highly realistic predictions for unobserved deep-tail strikes.

### 3. Safety Boundaries
To prevent the polynomial functions from blowing up in completely unobserved tails, all extrapolated predictions are strictly clipped between `0.0001` and `2.0` (200% IV).

## 🗂️ Repository Structure
* `dataset.csv` : The raw input dataset containing `datetime`, `underlying_price`, and partially missing Call (CE) and Put (PE) implied volatilities.
* `advanced_kaggle_pipeline.ipynb` : The main Jupyter Notebook containing the data loading, parabolic interpolation engine, and exact Kaggle-formatting logic.
* `advanced_submission.csv` : The final generated output file ready for Kaggle evaluation.

## 🚀 How to Run

1. **Install Dependencies:**
   Ensure you have the required Python libraries installed:
   ```bash
   pip install pandas numpy scipy
