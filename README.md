### Groundwater levels forecasting ###

This is a time series forecasting model I developed to predict the daily groundwater levels in different water stations in the UK.

## Datasets used:
- https://environment.data.gov.uk/hydrology/station/94f21263-0963-4cdc-9999-3a729884ec8e

## Data Preperation:

I started off by plotting the dataset to get a better understanding. The plot was mostly clean with small consistent gaps throughout it so I used linear interpolation to fill the gaps in.
Also since the dataset had multiple values for each day (one for each hour) I just took the average of each day to simplify the modelling process.

## Exploratory Data Analysis

To begin engineering the lag features, I used ACF and PACF to identify optimal lag values. I then derived the appropriate rolling statistics based on those lags.

Lags: 1, 7, 30, 365
Rolling features: 7-day, 30-day, and 365-day means, plus 7-day and 30-day standard deviation

## Initial Model

I built the intial Random Forest model with default hyperparameters: 
```
model = RandomForestRegressor(n_estimators=300, random_state=42)
```

Which resulted in a very accurate prediction, in which I used a validation test to check for any overfitting:

Train MSE: 0.00011284692365384644
Validation MSE: 0.003765416013566969
Test MSE: 0.0041003197529689

Because the Validation and Test MSE were much greater than the Train MSE, this was telling me that the model was memorising the training data.

## Solution

After many attempts at trying to find the major cause for this problem, I found that restraining the model's hyperparameters had the largest effect on reducing overfitting:

- I reduced the number of trees from 300 to 100
- I limited the trees depth to 8 to prevent it from memorising noise
- Increased 'min_samples_split' and 'min_samples_leaf' to 20 and 10 respectively
- Changed 'max_features' to sqrt

I also removed some redundant features which were lag_365 and rolling_std_30 to reduce pattern memorisation.

## Final results

Train MSE: 0.0012388394518625713
Validation MSE: 0.004727089895665242
Test MSE: 0.00525973313817204

Overall there was still some overfitting but it was a great improvement from the initial results and with this specific dataset, since it had distinct patterns, it would be hard to fully remove any form of overfitting. 