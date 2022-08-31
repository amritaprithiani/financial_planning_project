# Financial Planning with APIs and Simulations

Purpose of this repository is to create two financial analysis tools to help credit union members evaluate their financial health,by using a single Jupyter notebook. The two tool are finacial planner for emergencies and financial planner for retirement.

## Technologies

This project leverages python 3.9 with the following packages:
*[Jupyter notebook file]
*[pandas]
*[matplotlib]

## Installation Guide

Before running the application first install the following dependencies.

```python
pip install Jupyter lab
pip install pandas
pip install matplotlib
conda install -c anaconda requests
conda install -c jmcmurray json
pip install python-dotenv
pip insall alpaca_trade_api 
MCForecastTools import MCSimulation
```

## Usage

Ability to plan for emergencies and retirement. Following steps were taken to build the tool.

## Financial planning for emergencies

The members will be able to use this tool to visualize their current savings. The members can then determine if they have enough reserves for an emergency fund.

## Evaluate the Cryptocurrency Wallet by Using the Requests Library

1. Create a variable named monthly_income, and set its value to 12000.

2. Use the Requests library to get the current price (in US dollars) of Bitcoin (BTC) and Ethereum (ETH) by using the API endpoints that the starter code supplies.

```python
# The Free Crypto API Call endpoint URLs for the held cryptocurrency assets
btc_url = "https://api.alternative.me/v2/ticker/Bitcoin/?convert=USD"
eth_url = "https://api.alternative.me/v2/ticker/Ethereum/?convert=USD"

# Using the Python requests library, make an API call to access the current price of BTC & Eth
btc_response = requests.get(btc_url).json()
print(json.dumps(btc_response, indent=4, sort_keys=True))

eth_response = requests.get(eth_url).json()
print(json.dumps(eth_response, indent=4, sort_keys=True))
```

3.Navigate the JSON response object to access the current price of each coin, and store each in a variable.
accessing the current price and calulating the value in US dollars,comes up with current total crypto wallet balance = $323661.78

## Evaluate the Stock and Bond Holdings by Using the Alpaca SDK

1. Create an environment file (.env) to store the values of your Alpaca API key and Alpaca secret key.

2. Set the variables for the Alpaca API and secret keys. Using the Alpaca SDK, create the Alpaca tradeapi.REST object. In this object, include the parameters for the Alpaca API key, the secret key, and the version number.

```python
# Set the variables for the Alpaca API and secret keys
alpaca_api_key = os.getenv("ALPACA_API_KEY")
alpaca_secret_key = os.getenv("ALPACA_SECRET_KEY")

# Check the values were imported correctly by evaluating the type of each
display(type(alpaca_api_key))
display(type(alpaca_secret_key))

# Create the Alpaca tradeapi.REST object
alpaca = tradeapi.REST(
    alpaca_api_key,
    alpaca_secret_key,
    api_version="v2")
```

3.Start_date and end_date is 2020-08-07

4.Get the current closing prices for SPY and AGG by using the Alpaca get_bars function.

```python
# Use the Alpaca get_bars function to get current closing prices the portfolio
# Be sure to set the `df` property after the function to format the response object as a DataFrame
current_closing_price_df =  alpaca.get_bars(
    tickers,
    timeframe,
    start=start_date,
    end=end_date
).df

# Reorganize the DataFrame
# Separate ticker data
SPY = current_closing_price_df[current_closing_price_df['symbol']=='SPY'].drop('symbol', axis=1)
AGG = current_closing_price_df[current_closing_price_df['symbol']=='AGG'].drop('symbol', axis=1)


# Concatenate the ticker DataFrames
current_closing_price_df= pd.concat([SPY, AGG], keys=["SPY", "AGG"])

# Review the first 5 rows of the Alpaca DataFrame
current_closing_price_df.head()
```

5. Navigating the Alpaca response DataFrame, select the SPY and AGG closing prices, and store them as variables.

6. Calculate the value, in US dollars, of the current amount of shares in each of the stock and bond portions of the portfolio.
After navigating and calculating the current total balance in stock and bond = $60688.70

## Evaluate the Emergency Fund

1. Create a Python list named savings_data that has two elements. i.e total crypto balance and total stock and bonds.

2. Use the savings_data list to create a Pandas DataFrame named savings_df.

```python
# Create a Pandas DataFrame called savings_df 
savings_df = pd.DataFrame(data = savings_data, columns = ["amount"], index = ["crypto","stock/bond"])

# Display the savings_df DataFrame
savings_df
```

3. Use the savings_df DataFrame to plot a pie chart that visualizes the composition of the member’s portfolio.

![pie chart](/financial_planning/pie-chart.png)

4. Using Python, determine if the current portfolio has enough to create an emergency fund as part of the member’s financial plan. Ideally, an emergency fund should equal to three times the member’s monthly income. To do this, implement the following steps:

    a. Create a variable named emergency_fund_value, and set it equal to three times the value of the member’s monthly_income of $12000. 

    b. Create a series of three if statements to determine if the member’s total portfolio is large enough to fund the emergency portfolio:

```python
if total_portfolio > emergency_fund_value:
    print("Congratulations you have enough money in the fund!")
elif total_portfolio == emergency_fund_value:
    print("Congratulation you on reaching important financial goal!")
else:
    print("You are ${ emergency_fund_value - total_portfolio} away from reaching the goal")
```

## Financial planning for retirement

This tool will forecast the performance of member's retirement portfolio in 30 years and 10 years. To do this, the tool will make an Alpaca API call via the Alpaca SDK to get historical price data for use in Monte Carlo simulations.

## Create the Monte Carlo Simulation

1. Make an API call via the Alpaca SDK to get 3 years of historical closing prices for a traditional 60/40 portfolio split: 60% stocks (SPY) and 40% bonds (AGG) and 80/20 portfolio split: 80% stock (SPY) and 40% bonds (AGG).

```python
# Use the Alpaca get_bars function to make the API call to get the 3 years worth of pricing data
mc_prices_df =  alpaca.get_bars(
    tickers,
    timeframe,
    start=mc_start_date,
    end=mc_end_date
).df
# Reorganize the DataFrame
# Separate ticker data
SPY = mc_prices_df[mc_prices_df['symbol']=='SPY'].drop('symbol', axis=1)
AGG = mc_prices_df[mc_prices_df['symbol']=='AGG'].drop('symbol', axis=1)


# Concatenate the ticker DataFrames
mc_prices_df = pd.concat([SPY, AGG], axis=1, keys=["SPY", "AGG"])
```


2. Run a Monte Carlo simulation of 500 samples and 30 years for the 60/40 portfolio, and then plot the results.
Run a Monte Carlo simulation of 500 samples and 10 years for the 80/20 portfolio, and then plot the results.

```python
# The weights should be split 40% to AGG and 60% to SPY.
# Run 500 samples.
MC_spy60_weight =  MCSimulation(
    portfolio_data = mc_prices_df,
    weights = [.60, .40],
    num_simulation = 500,
    num_trading_days = 252 * 30
)
# The weights should be split 20% to AGG and 80% to SPY.
# Run 500 samples.

MC_spy80_weight =  MCSimulation(
    portfolio_data = mc_prices_df,
    weights = [.80, .20],
    num_simulation = 500,
    num_trading_days = 252 * 10
)
```

3. Plot the probability distribution of the Monte Carlo simulation. Plot the probability distribution of the Monte Carlo simulation. The following image shows the histogram plot for 30 years projection resulting from a simulation.

![thirty year plot](/financial_planning/MC_30_year_line_plot.png)
![thirty year dist](/financial_planning/MC_30_year_dist_plot.png)

4. Plot the probability distribution of the Monte Carlo simulation. Plot the probability distribution of the Monte Carlo simulation. The following image shows the histogram plot for 10 years projection resulting from a simulation.

![ten year plot](/financial_planning/MC_10_year_line_plot.png)
![ten year dist](/financial_planning/MC_10_year_dist_plot.png)

## Analyze the Retirement Portfolio Forecasts

Using the current value of only the stock and bond portion of the member's portfolio and the summary statistics that you generated from the Monte Carlo simulation, gives following analysis results for both 30 years and 10 years simulation:

There is a 95% chance that an initial investment of $60688.70 in the portfolio with a 60% weight in SPY over the next 30 years will end within in the range of $153017.14 and $2315120.0.

There is a 95% chance that an initial investment of $60688.70 in the portfolio with a 80% weight in SPY over the next 10 years will end within in the range of $51277.58 and $438940.67.

So,weighing the portfolio heavily in stocks for 10 years might not be a great option for members. Though 10 years simulation is less risker than 30 years, but there are chances of losing money as well.

## Contributors

Brought to you by Amrita Prithiani

## License

MIT
