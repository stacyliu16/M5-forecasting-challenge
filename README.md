# M5-forecasting-challenge
Estimate the point forecasts of the unit sales of various products sold in the USA by Walmart

## Introduction

**The goal**: We have been challenged to **predict sales data** provided by the retail giant Walmart 28 days into the future. This competition will run in 2 tracks: In addition to forecasting the values themselves in the Forecasting competition, we are simultaneously tasked to **estimate the uncertainty** of our predictions in the Uncertainty Distribution competition. Both competitions will have the same 28 day forecast horizon.

**The goal of EDA Notebook**: This notebook will be used to visualize the sales data and gain some insights from it.

**The data**: We are working with **42,840 hierarchical time series**. The data were obtained in the 3 US states of California (CA), Texas (TX), and Wisconsin (WI). “Hierarchical” here means that data can be aggregated on different levels: item level, department level, product category level, and state level. The sales information reaches back from Jan 2011 to June 2016. In addition to the sales numbers, we are also given corresponding data on prices, promotions, and holidays. Note, that we have been warned that **most of the time series contain zero values.**

The data comprises **3049** individual products from *3 categories* and *7 departments*, sold in 10 stores in 3 states. The hierachical aggregation captures the combinations of these factors. For instance, we can create 1 time series for all sales, 3 time series for all sales per state, and so on. The largest category is sales of all individual 3049 products per 10 stores for 30490 time series.

The training data comes in the shape of 3 separate files:

- sales_train.csv: this is our main training data.  Contains the historical daily unit sales data per product and store from d_1 - d_1913.

- sell_prices.csv: the store and item IDs together with the sales price of the item as a weekly average.

- calendar.csv: dates together with related features like day-of-the week, month, year, and an 3 binary flags for whether the stores in each state allowed purchases with SNAP food stamps at this date (1) or not (0).

- sales_train_evaluation.csv - Available one month before the competition deadline. It will include sales from d_1 - d_1941.

**The metrics:**

The point forecast submission are being evaluated using the **Root Mean Squared Scaled Error (RMSSE)**, which is derived from the Mean Absolute Scaled Error (MASE) that was designed to be scale invariant and symmetric. 

## Introduction

**The goal**: We have been challenged to **predict sales data** provided by the retail giant Walmart 28 days into the future. This competition will run in 2 tracks: In addition to forecasting the values themselves in the Forecasting competition, we are simultaneously tasked to **estimate the uncertainty** of our predictions in the Uncertainty Distribution competition. Both competitions will have the same 28 day forecast horizon.

**The goal of EDA Notebook**: This notebook will be used to visualize the sales data and gain some insights from it.

**The data**: We are working with **42,840 hierarchical time series**. The data were obtained in the 3 US states of California (CA), Texas (TX), and Wisconsin (WI). “Hierarchical” here means that data can be aggregated on different levels: item level, department level, product category level, and state level. The sales information reaches back from Jan 2011 to June 2016. In addition to the sales numbers, we are also given corresponding data on prices, promotions, and holidays. Note, that we have been warned that **most of the time series contain zero values.**

The data comprises **3049** individual products from *3 categories* and *7 departments*, sold in 10 stores in 3 states. The hierachical aggregation captures the combinations of these factors. For instance, we can create 1 time series for all sales, 3 time series for all sales per state, and so on. The largest category is sales of all individual 3049 products per 10 stores for 30490 time series.

The training data comes in the shape of 3 separate files:

- sales_train.csv: this is our main training data.  Contains the historical daily unit sales data per product and store from d_1 - d_1913.

- sell_prices.csv: the store and item IDs together with the sales price of the item as a weekly average.

- calendar.csv: dates together with related features like day-of-the week, month, year, and an 3 binary flags for whether the stores in each state allowed purchases with SNAP food stamps at this date (1) or not (0).

- sales_train_evaluation.csv - Available one month before the competition deadline. It will include sales from d_1 - d_1941.

**The metrics:**

The point forecast submission are being evaluated using the **Root Mean Squared Scaled Error (RMSSE)**, which is derived from the Mean Absolute Scaled Error (MASE) that was designed to be scale invariant and symmetric. 
