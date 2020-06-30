# M5-forecasting-challenge
Estimate the point forecasts of the unit sales of various products sold in the USA by Walmart

## Introduction

**The goal**: We have been challenged to **predict sales data** provided by the retail giant Walmart 28 days into the future. This competition will run in 2 tracks: In addition to forecasting the values themselves in the Forecasting competition, we are simultaneously tasked to **estimate the uncertainty** of our predictions in the Uncertainty Distribution competition. Both competitions will have the same 28 day forecast horizon.

**EDA Notebook**: This notebook will be used to visualize the sales data and gain some insights from it. (https://github.com/stacyliu16/M5-forecasting-challenge/blob/master/M5%20Forecasting%20-%20EDA.ipynb)

**Prophet Notebook**: This notebook will be used to model sales using Facebook Prophet model. (https://github.com/stacyliu16/M5-forecasting-challenge/blob/master/M5%20Forecasting%20Challenge%20-%20Prophet%20Model%20Deep%20Dive.ipynb)

**LightGBM Notebook**: This notebook will be used to model sales using LightGBM model. (https://github.com/stacyliu16/M5-forecasting-challenge/blob/master/M5%20Forecasting%20Challenge%20-%20LightGBM%20Deep%20Dive.ipynb)

**LSTM Notebook (To be uploaded)**: This notebook will be used to model sales using LSTM model.

**The data**: We are working with **42,840 hierarchical time series**. The data were obtained in the 3 US states of California (CA), Texas (TX), and Wisconsin (WI). “Hierarchical” here means that data can be aggregated on different levels: item level, department level, product category level, and state level. The sales information reaches back from Jan 2011 to June 2016. In addition to the sales numbers, we are also given corresponding data on prices, promotions, and holidays. Note, that we have been warned that **most of the time series contain zero values.**

The data comprises **3049** individual products from *3 categories* and *7 departments*, sold in 10 stores in 3 states. The hierachical aggregation captures the combinations of these factors. For instance, we can create 1 time series for all sales, 3 time series for all sales per state, and so on. The largest category is sales of all individual 3049 products per 10 stores for 30490 time series.

The training data comes in the shape of 3 separate files:

- sales_train.csv: this is our main training data.  Contains the historical daily unit sales data per product and store from d_1 - d_1913.

- sell_prices.csv: the store and item IDs together with the sales price of the item as a weekly average.

- calendar.csv: dates together with related features like day-of-the week, month, year, and an 3 binary flags for whether the stores in each state allowed purchases with SNAP food stamps at this date (1) or not (0).

- sales_train_evaluation.csv - Available one month before the competition deadline. It will include sales from d_1 - d_1941.

**The metrics:**

The point forecast submission are being evaluated using the **Root Mean Squared Scaled Error (RMSSE)**, which is derived from the Mean Absolute Scaled Error (MASE) that was designed to be scale invariant and symmetric. 

**Final Model & Results**:

Results from the **Prophet** model was submitted for final evaluation. The built in seasonality trends and ability to handle outliers as well as ease of parameter customization allowed me to produce the smallest error of 0.57504 on the test set.

The parameters used are: growth='linear', holidays = holidays, uncertainty_samples=False, n_changepoints = 50, changepoint_prior_scale=0.7, changepoint_range=0.8, holidays_prior_scale=20, seasonality_mode='multiplicative', seasonality_prior_scale=20

Note that "holidays" includes events, snap dates, as well as a +2 and -3 window for events 

## EDA Summary
- The rolling 90 day sales by state shows that sales for all three States have been increasing from 2011 to 2016
	- CA sold the most amount of items during the entire 5 year period
		- CA has four stores with store 3 always having highest sales
		- CA_1 and CA_4 had comparably flatter growth
		- CA_3 had a long decline, followed by steep increase at around day 1550 --> **for CA_3 might want to try removing d1510-1750**
	- WI overtook TX in terms of the number of the items sold around day 1600
		- Around day 1000, all three TX stores had a steep decline in sales, especially so for store TX_2 --> **for TX might want to try limiting training dataset to d1100 and after**
		- WI_1 and WI_2 saw steep growth in sales after slower beginnng
		- WI_3 sale started declining when WI_1 and WI_2 began growing --> **WI had lots of movement during the first 750 days. Might want to limit data to after d750
	- There's some seasonality at play here with sales reaching a local max every 300-400 days
- By Category:
	- Food has the highest unit sales during this period, followed by household items, and lastly hobbies. In particular FOODS_3 has much higher sales than any other department.
		- Food appears to be driving high avg sales on event days, in particular for Easter, Father's Day, Labor Day, Mother's Day, Orthodox Easter, and Superbowl
	- Household: Where we are seeing peaks in Food sale, we are also seeing bump in household item sales (especially for Easter, Labor Day, Orthodox Easter, and Superbowl)
	- Hobbies does not have much variation between events except for dips on Independence Day, New Year, and Thanksgiving
- By Calendar:
	- Walmart sales has been increasing from 2012 to 2016. The one day every year where we see close to zero sales is Christmas when stores are closed. --> **remove Christmas from the training dataset**
	- Easter, Father's Day, LaborDay, MemorialDay, OthodoxEaster, and St Patricks Day appears to have much higher sales than no event days.
	- For Event_Name_2, Cinco de Mayo, Father's Day, and OthodoxEaster appears to have much higher sales than no event days.
	- Average daily sale is much higher 1-2 days prior to events than the actual event, driven by Food. --> **create a customized calendar to flag 2 days prior events**
	- In all 3 States, SNAP days generated larger sales
	- Seasonality:
		- Sales were the highest on the weekend (fri-sun).
		- Sales are higher in the first half of the month --> may be result of SNAP
		- Sales peaked in the summer months (June-Oct).
- By Price:
	- Price does not seem to have an effect on sales amt
- At the item level sales are sporatic with lots of days with zero sales

## Prophet Summary
**Prophet Model**: Prophet is a procedure for forecasting time series data based on an additive model where non-linear trends are fit with yearly, weekly, and daily seasonality, plus holiday effects. It works best with time series that have strong seasonal effects and several seasons of historical data. Prophet is robust to missing data and shifts in the trend, and typically handles outliers well. (https://facebook.github.io/prophet/)

**Model & Results:**
Since at the item level data is fairly sporatic, I used a top down approach to predict 70 time series group by Department, Category, Store and State and then distribute down to the item level by weight (total sale in last 28 days). 

The following model metrics produced the smallest error of **0.57504** on the test set:
- Prophet(growth='linear', holidays = holidays, uncertainty_samples=False, n_changepoints = 50, changepoint_prior_scale=0.7, changepoint_range=0.8, holidays_prior_scale=20, seasonality_mode='multiplicative', seasonality_prior_scale=20)
    - Note that "holidays" includes events, snap dates, as well as a +2 and -3 window for events (from EDA we found that sales increased 2 days prior to event and decreases 3 days after)
    
## LightGBM Summary
**LightGBM Model**:Light GBM is a fast, distributed, high-performance gradient boosting framework based on decision tree algorithm, used for ranking, classification and many other machine learning tasks. It splits the tree leaf wise with the best fit whereas other boosting algorithms split the tree depth wise or level wise rather than leaf-wise. So when growing on the same leaf in Light GBM, the leaf-wise algorithm can reduce more loss than the level-wise algorithm and hence results in much better accuracy which can rarely be achieved by any of the existing boosting algorithms.

It is designed to be distributed and efficient with the following advantages: (1) Faster training speed and higher efficiency, (2) Lower memory usage, (3) Better accuracy, (4) Support of parallel and GPU learning, (5) Capable of handling large-scale data (https://lightgbm.readthedocs.io/en/latest/)

**Model & Results:**
- Additional features added: (1) lagging 7, 28 day demand, (2) rolling 7, 28 day mean demand, (3) additional date features
- parameters used = {
          "objective" : "poisson",
          "metric" :"rmse",
          "force_row_wise" : True,
          "learning_rate" : 0.075,
          "sub_row" : 0.75,
          "bagging_freq" : 1,
          "lambda_l2" : 0.1,
          "metric": ["rmse"],
          'verbosity': 1,
          'num_iterations' : 1200,
          'num_leaves': 128,
          "min_data_in_leaf": 100,
         }
- Result: **2.93753** on the test set

