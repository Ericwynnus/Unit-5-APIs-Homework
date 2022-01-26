# Unit 5 - Financial Planning


```python
# Initial imports
import os
import requests
import pandas as pd
from dotenv import load_dotenv
import alpaca_trade_api as tradeapi
from MCForecastTools import MCSimulation

%matplotlib inline
```


```python
# Load .env enviroment variables
load_dotenv()
```




    True



## Part 1 - Personal Finance Planner

### Collect Crypto Prices Using the `requests` Library


```python
# Set current amount of crypto assets
# my_btc and my_eth. Set them equal to 1.2 and 5.3, respectively.
# I am using assignment #5.2 as my example.

my_btc = 1.2
my_eth = 5.3

```


```python
# Crypto API URLs
btc_url = "https://api.alternative.me/v2/ticker/Bitcoin/?convert=CAD"
eth_url = "https://api.alternative.me/v2/ticker/Ethereum/?convert=CAD"
```


```python
# Fetch current BTC price
btc_response = requests.get(btc_url).json()
btc_price = btc_response["data"]["1"]["quotes"]["CAD"]["price"]

# Compute current value of my crpto
my_btc_value = my_btc*btc_price

# Print current crypto wallet balance
print(f"The current value of your {my_btc} BTC is ${my_btc_value:0.2f}")

```

    The current value of your 1.2 BTC is $57480.27
    


```python
# Fetch current ETH price
eth_response_data = requests.get(eth_url).json()
eth_price = eth_response_data["data"]["1027"]["quotes"]["USD"]["price"]

eth_price
                                                       
# Compute current value of my crpto
my_eth_value = my_eth*eth_price
                                                       
print(f"The current value of your {my_eth} ETH is ${my_eth_value:0.2f}")                                                       
                                                                                                              
                                                       
#my_eth = float(df_portfolio["ETH"]["close"]) 
#print(f"The current value of your {my_eth} ETH is ${my_eth_value:0.2f}")
```

    The current value of your 5.3 ETH is $13761.29
    

### Collect Investments Data Using Alpaca: `SPY` (stocks) and `AGG` (bonds)


```python
# Set current amount of shares
my_agg = 200
my_spy = 50
```


```python
# Set Alpaca API key and secret
alpaca_api_key = os.getenv("ALPACA_API_KEY")
alpaca_secret_key = os.getenv("ALPACA_SECRET_KEY")

print(f"Alpaca Key type: {type(alpaca_api_key)}")
print(f"Alpaca Secret Key type: {type(alpaca_secret_key)}")

# Create the Alpaca API object
alpaca = tradeapi.REST(
    alpaca_api_key,
    alpaca_secret_key,
    api_version="v2")
```

    Alpaca Key type: <class 'str'>
    Alpaca Secret Key type: <class 'str'>
    


```python
# Format current date as ISO format
today = pd.Timestamp("2022-01-19", tz="America/New_York").isoformat()

# Set the tickers
tickers = ["AGG", "SPY"]

# Set timeframe to '1D' for Alpaca API
timeframe = "1D"

# Get current closing prices for SPY and AGG
# (use a limit=1000 parameter to call the most recent 1000 days of data)
df_portfolio = alpaca.get_barset(
    tickers,
    timeframe,
    start = today,
    end = today
).df

# Preview DataFrame
df_portfolio
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="5" halign="left">AGG</th>
      <th colspan="5" halign="left">SPY</th>
    </tr>
    <tr>
      <th></th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
    </tr>
    <tr>
      <th>time</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2022-01-19 00:00:00-05:00</th>
      <td>111.7</td>
      <td>111.89</td>
      <td>111.65</td>
      <td>111.71</td>
      <td>7641972</td>
      <td>458.17</td>
      <td>459.6124</td>
      <td>451.46</td>
      <td>451.79</td>
      <td>95311817</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Pick AGG and SPY close prices
# Format current date as ISO format
today = pd.Timestamp("2022-01-20", tz="America/New_York").isoformat()

tickers = ["AGG", "SPY"]

# Set timeframe to one day ('1D') for the Alpaca API
timeframe = "1D"



# Get current price data for AGG and SPY
df_portfolio = alpaca.get_barset(
    tickers,
    timeframe,
    start = today,
    end = today
).df

# Display sample data
df_portfolio

agg_close_price = float(df_portfolio["AGG"]["close"])
spy_close_price = float(df_portfolio["SPY"]["close"])

# Print AGG and SPY close prices
print(f"Current AGG closing price: ${agg_close_price}")
print(f"Current SPY closing price: ${spy_close_price}")
```

    Current AGG closing price: $111.79
    Current SPY closing price: $446.79
    


```python
#Current Value of Stock df_portfolio
agg_value = agg_close_price * my_agg
spy_value = spy_close_price * my_spy

print(agg_value)
print(spy_value)

```

    22358.0
    22339.5
    


```python
# Print current value of shares
print(f"The current value of your {my_spy} SPY shares is ${spy_value:0.2f}")
print(f"The current value of your {my_agg} AGG shares is ${agg_value:0.2f}")

```

    The current value of your 50 SPY shares is $22339.50
    The current value of your 200 AGG shares is $22358.00
    


```python
#Total Values:
total_stock_value =agg_value + spy_value  
total_stock_value

#total_crypto_value =my_btc + my_eth

#total_crypto_value

#total_value = total_stock_value + total_crypto_value

#print(value_data)

print("Total Value of Stock Shares:{df_value:0.2f}")
```

    Total Value of Stock Shares:{df_value:0.2f}
    

### Savings Health Analysis


```python
# Set monthly household income
monthly_income = 12000
print(monthly_income)

```

    12000
    


```python
# Consolidate financial assets data
df_savings_data = [total_stock_value, total_crypto_value]

# Create savings DataFrame
df_savings = pd.DataFrame(df_savings_data, columns=["Amount"], index = ["Crypto", "Shares"])

# Display savings DataFrame
display(df_savings)
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    ~\AppData\Local\Temp/ipykernel_15700/4262652680.py in <module>
          1 # Consolidate financial assets data
    ----> 2 df_savings_data = [total_stock_value, total_crypto_value]
          3 
          4 # Create savings DataFrame
          5 df_savings = pd.DataFrame(df_savings_data, columns=["Amount"], index = ["Crypto", "Shares"])
    

    NameError: name 'total_crypto_value' is not defined



```python
# Plot savings pie chart
df_savings.plot.pie(y="Amount", title="Personal Savings")
```


```python
#df_value.plot.bar(title="Current Value in Dollars of Stock Portfolio")
```


```python
# Set ideal emergency fund
emergency_fund = monthly_income * 3

# Calculate total amount of savings
total_savings = float(df_savings.sum())

# Validate saving health
if total_savings > emergency_fund:
    print(f"You have enough saved!")
elif total_savings == emergency_fund -total_savings:
    print(f'You have reached your goal!')
else:
    print(f"You are {emergency_fund - total_savings} short of you goal and get cracking!")


```

## Part 2 - Retirement Planning

### Monte Carlo Simulation


```python
# Set Alpaca API key and secret
alpaca_api_key = os.getenv("ALPACA_API_KEY")
alpaca_secret_key = os.getenv("ALPACA_SECRET_KEY")

print(f"Alpaca Key type: {type(alpaca_api_key)}")
print(f"Alpaca Secret Key type: {type(alpaca_secret_key)}")

# Create the Alpaca API object
alpaca = tradeapi.REST(
    alpaca_api_key,
    alpaca_secret_key,
    api_version="v2")
```


```python
# Set start and end dates of five years back from today.
# Sample results may vary from the solution based on the time frame chosen
start = pd.Timestamp('2017-01-19', tz='America/New_York').isoformat()
end_date = pd.Timestamp('2022-01-19', tz='America/New_York').isoformat()

```


```python
# Get 5 years' worth of historical data for SPY and AGG
# (use a limit=1000 parameter to call the most recent 1000 days of data)
# Set the ticker AGG
ticker = "AGG"
```


```python
# Set timeframe to '1D'
timeframe = "1D"

# Set start and end datetimes of 5 years from Today
start_date = pd.Timestamp("2017-01-19", tz="America/New_York").isoformat()
end_date = pd.Timestamp("2022-01-19", tz="America/New_York").isoformat()

# Get 1 year's worth of historical data for AGG
ticker_data = alpaca.get_barset(
    ticker,
    timeframe,
    start = start_date,
    end = end_date,
    limit = 1000,
).df

ticker_data.head()

# Display sample data



## Set the ticker SPY
ticker = "SPY"

# Set timeframe to '1D'
timeframe = "1D"

# Set start and end datetimes of 4 years from Today
start_date = pd.Timestamp("2017-01-19", tz="America/New_York").isoformat()
end_date = pd.Timestamp("2022-01-19", tz="America/New_York").isoformat()

# Get 1 year's worth of historical data for SPY
ticker_data = alpaca.get_barset(
    ticker,
    timeframe,
    start=start_date,
    end=end_date,
    limit=1000,
).df

ticker_data.head()
```


```python
# Configuring a Monte Carlo simulation to forecast 30 years cumulative returns
# Set number of simulations all
num_sims = 500

# Configure a Monte Carlo simulation to forecast 30 YEARS OF returns
MC_AAG = MCSimulation(
    portfolio_data = ticker_data,
    num_simulation = num_sims,
    num_trading_days = 252*30
)


# Set number of simulations SPY
num_sims = 500

# Configure a Monte Carlo simulation to forecast 30 YEARS OF returns
MC_SPY = MCSimulation(
    portfolio_data = ticker_data,
    num_simulation = num_sims,
    num_trading_days = 252*30
)

#See Below
#MC_even_dist = MCSimulation(
#    portfolio_data = monte_carlo_df,
#    weights = [.40, .60],
#    num_simulation = 1000,
#    num_trading_days = 252*30)
#MC_even_dist.portfolio_data.head()
```


```python
# Printing the simulation input data
MC_AAG.calc_cumulative_return()
MC_SPY.calc_cumulative_return()
```


```python
# Running a Monte Carlo simulation to forecast 30 years cumulative returns
MC_AAG.calc_cumulative_return()
MC_SPY.calc_cumulative_return()
```


```python
# Plot simulation outcomes
line_plot = MC_SPY.plot_simulation()
line_plot = MC_AAG.plot_simulation()
#dist_plot = MC_even_dist.plot_distrubution()
```


```python
# Plot probability distribution and confidence intervals
# YOUR CODE HERE!
```


```python
MC_even_dist = MCSimulation(
    portfolio_data = monte_carlo_df,
    weights = [.40, .60],
    num_simulation = 1000,
    num_trading_days = 252*30)
MC_even_dist.portfolio_data.head()
```

### Retirement Analysis


```python
# Fetch summary statistics from the Monte Carlo simulation results
even_tbl = MC_even_dist.summarize_cumulative_return()

# Print summary statistics
print(even_tbl)
```

### Calculate the expected portfolio return at the `95%` lower and upper confidence intervals based on a `$20,000` initial investment.


```python
# Set initial investment
initial_investment = 20000

# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes of our $20,000
#confidence_interval = ci_lower
ci_lower = round(even_tbl[8]*initial_investment,2)
ci_upper = round(even_tbl[9]*initial_investment,2)

# Print results
print(f"There is a 95% chance that an initial investment of ${initial_investment} in the portfolio"
      f" over the next 30 years will end within in the range of"
      f" ${ci_lower} and ${ci_upper}")
```

### Calculate the expected portfolio return at the `95%` lower and upper confidence intervals based on a `50%` increase in the initial investment.


```python
# Set initial investment
initial_investment = 20000 * 1.5

# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes of our $30,000
ci_lower = round(even_tbl[8]*initial_investment,2)
ci_upper = round(even_tbl[9]*initial_investment,2)

# Print results
print(f"There is a 95% chance that an initial investment of ${initial_investment} in the portfolio"
      f" over the next 30 years will end within in the range of"
      f" ${ci_lower} and ${ci_upper}")
```

## Optional Challenge - Early Retirement


### Five Years Retirement Option


```python
# Configuring a Monte Carlo simulation to forecast 5 years cumulative returns
# YOUR CODE HERE!
```


```python
# Running a Monte Carlo simulation to forecast 5 years cumulative returns
# YOUR CODE HERE!
```


```python
# Plot simulation outcomes
# YOUR CODE HERE!
```


```python
# Plot probability distribution and confidence intervals
# YOUR CODE HERE!
```


```python
# Fetch summary statistics from the Monte Carlo simulation results
# YOUR CODE HERE!

# Print summary statistics
# YOUR CODE HERE!
```


```python
# Set initial investment
# YOUR CODE HERE!

# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes of our $60,000
# YOUR CODE HERE!

# Print results
print(f"There is a 95% chance that an initial investment of ${initial_investment} in the portfolio"
      f" over the next 5 years will end within in the range of"
      f" ${ci_lower_five} and ${ci_upper_five}")
```

### Ten Years Retirement Option


```python
# Configuring a Monte Carlo simulation to forecast 10 years cumulative returns
# YOUR CODE HERE!
```


```python
# Running a Monte Carlo simulation to forecast 10 years cumulative returns
# YOUR CODE HERE!
```


```python
# Plot simulation outcomes
# YOUR CODE HERE!
```


```python
# Plot probability distribution and confidence intervals
# YOUR CODE HERE!
```


```python
# Fetch summary statistics from the Monte Carlo simulation results
# YOUR CODE HERE!

# Print summary statistics
# YOUR CODE HERE!
```


```python
# Set initial investment
# YOUR CODE HERE!

# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes of our $60,000
# YOUR CODE HERE!

# Print results
print(f"There is a 95% chance that an initial investment of ${initial_investment} in the portfolio"
      f" over the next 10 years will end within in the range of"
      f" ${ci_lower_ten} and ${ci_upper_ten}")
```


```python

```
