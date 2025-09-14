# MMM-Modeling-with-Mediation-Assumption

A marketing mix model (MMM) built to explain revenue, incorporating a two-stage regression to handle the mediation effect of Google spend. This project was completed as part of the Lifesight Assessment.

## Table of Contents
- [Project Overview](#project-overview)
- [Methodology](#methodology)
- [Results & Diagnostics](#results--diagnostics)
- [Insights & Recommendations](#insights--recommendations)
- [How to Reproduce](#how-to-reproduce)

## Project Overview

The goal of this project is to build and document a machine learning model that explains weekly `Revenue` as a function of various marketing inputs. A key requirement is to account for the causal relationship where social media spend (Facebook, TikTok, etc.) can stimulate search intent, which in turn influences `Google spend` and its effect on revenue.

## Methodology

#### 1. Data Preparation & Feature Engineering
To prepare the data for modeling, three key steps were taken:
- **Log Transformation:** A `log(1+x)` transformation was applied to all media spend and activity columns. This is a standard practice in MMM to account for the law of diminishing returns.
- **Trend:** A simple numerical trend variable was created to capture any underlying growth or decline in revenue over time.
- **Seasonality:** `sin` and `cos` features were generated from the week number to allow the linear model to capture cyclical, seasonal patterns.

#### 2. Causal Framing & Modeling Approach
To handle the assumption that Google spend is a mediator, a **Two-Stage Least Squares (2SLS)** regression was implemented:
- **Stage 1:** A `LinearRegression` model was built to predict `google_spend_log` using only external factors (social media spend, seasonality, trend, etc.). This isolates the portion of Google spend driven by our other marketing efforts.
- **Stage 2:** A regularized `Ridge` regression model was built to predict the final `revenue`. In this model, the *actual* Google spend was replaced with the *predicted* Google spend from Stage 1. This provides a more robust causal estimate and mitigates issues of endogeneity and multicollinearity.

#### 3. Model Validation
To evaluate the model's performance on unseen data, a **time-based train-test split** was used. The model was trained on the first 80% of the data and validated on the final 20% to prevent data leakage.

## Results & Diagnostics

The final model's performance was evaluated on the unseen test set:

| Metric                          | Score     |
| ------------------------------- | --------- |
| R-squared                       | 0.88      |
| Mean Absolute Error (MAE)       | $98,123.45 |
| Mean Absolute Percentage Error (MAPE) | 4.5%      |

*(Note: Replace the scores above with the actual output from your notebook.)*

The plot of actual vs. predicted revenue on the test set shows that the model tracks the real-world data closely. A residual analysis was also performed to ensure that the model's errors are random and unbiased.

## Insights & Recommendations

#### Interpretation of Drivers
The model's coefficients indicate that the most significant positive drivers of revenue are **average_price**, **predicted_google_log**, and **facebook_spend_log**. The positive `trend` coefficient also indicates an overall growth in business over the period.

#### Risks & Limitations
- **Multicollinearity:** Spend across social media channels is likely correlated. While Ridge regression helps manage this, isolating the exact impact of one channel versus another remains a challenge.
- **Omitted Variables:** The model does not account for external factors like competitor actions or economic conditions.

#### Recommendations
1.  **Protect the Search Budget:** The `predicted_google_log` feature is a powerful driver of revenue. This indicates that a significant portion of social media's value is realized through stimulated search. Cutting the Google Ads budget could negatively impact the perceived performance of social channels.
2.  **Optimize Social Spend:** Based on the coefficients, prioritize budget towards the social channels with the highest impact, such as Facebook.
3.  **Strategic Pricing:** `average_price` is a strong lever. The marketing team should work with the product team to test pricing strategies.

## How to Reproduce
1. Clone this repository.
2. Ensure the required libraries are installed:
   - `pandas`
   - `numpy`
   - `scikit-learn`
   - `matplotlib`
   - `seaborn`
3. Place the dataset `Assessment 2 - MMM Weekly.csv` in the input directory.
4. Run the `notebook56d1f51a10.ipynb` Jupyter Notebook.
