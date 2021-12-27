# Stock_Trading_Sim ABOUT: 
Final Project for Data 3500 (Fall 2021), we were allowed to do whatever we wanted using skills learned from this semester
- Data was obtained from a web JSON API
- Stock market trading simulation, on real time market prices 
- Ran multiple technical trading strategies including: bollinger bands, mean reversion, moving average crossover in Python on AWS.
  - Strategies were run for 10 stock tickers
  - Inside the logic is a ticker stating if it is best to buy or sell a certain stock based on the day (as long as new data is appended) 
- Strategies yielded as high as 43% annually. 
  - Managed all data scripts and server side logic. 
- Data is created and appended to CSV files, where the functions can effectively pull the data from and then perform analysis



import requests
import json
import time


# Build function to obtain and store data from a web JSON API for each of the 10 tickers, in CSV file format
def create_data():

    tickers = ["AAPL", "ADBE", "AMC", "ETSY", "GOOG", "MARA", "RIOT", "STX", "TSLA", "TWLO"]
    for ticker in tickers:
        
         # ticker = 'AAPL'
        url = 'http://www.alphavantage.co/query?function=TIME_SERIES_DAILY&symbol='+ticker+'&outputsize=full&apikey=NG9C9EPVYBMQT0C8'
        print(url)
        request = requests.get(url)
        rqst_dict = json.loads(request.text)
        time.sleep(12)
        
        # Key names on AV converted to variables
        key_series = "Time Series (Daily)"
        key_open = '1. open'
        key_close = '4. close'
        key_hi = '2. high'
        key_lo = '3. low'
        
        prices = []
        
        for date in rqst_dict[key_series]:
            row = ""
            row += date + ","
            row += rqst_dict[key_series][date][key_open] + ","
            row += rqst_dict[key_series][date][key_hi] + ","
            row += rqst_dict[key_series][date][key_lo] + ","
            row += rqst_dict[key_series][date][key_close] + "\n"
            
            prices.append(row)
            
            
        prices.reverse()
        
        csv_file = open("/home/ubuntu/environment/FinalProject/" + ticker + '.csv', 'w')
        csv_file.write('date,open,high,low,close\n')
        
        for row in prices:
            csv_file.write(row)
            
            
        csv_file.close()



# Build a function to store append new data to each CSV file, each day the bot is run
def append_data():

    tickers = ["AAPL", "ADBE", "AMC", "ETSY", "GOOG", "MARA", "RIOT", "STX", "TSLA", "TWLO"]
    for ticker in tickers:
    
        
        url = 'http://www.alphavantage.co/query?function=TIME_SERIES_DAILY&symbol='+ticker+'&outputsize=full&apikey=NG9C9EPVYBMQT0C8'
        
        print(url)
        request = requests.get(url)
        rqst_dict = json.loads(request.text)
        time.sleep(12)
        print(request.text)
        
        # Key names on AV converted to variables
        key_series = "Time Series (Daily)"
        key_open = '1. open'
        key_close = '4. close'
        key_high = '2. high'
        key_low = '3. low'
        
        file = open("/home/ubuntu/environment/FinalProject/" + ticker + '.csv' )
        lines = file.readlines()
        last_line = lines[-1]
        items = last_line.split(',')
        last_date = items[0]
        
        prices = []
        
        for date in rqst_dict[key_series]:
            row = ''
            row += date + ","
            row += rqst_dict[key_series][date][key_open] + ','
            row += rqst_dict[key_series][date][key_high] + ','
            row += rqst_dict[key_series][date][key_low] + ','
            row += rqst_dict[key_series][date][key_close] + '\n'
            if date > last_date:
                prices.append(row)
            
        prices.reverse()
        
        csv_file = open("/home/ubuntu/environment/FinalProject/" + ticker + '.csv', 'a')
        # csv_file.write('date,open,high,low,close\n')
        
        for row in prices:
            csv_file.write(row)
            
        
            
        csv_file.close()
  
create_data()
append_data()

# All 3 strategy functions built for analyses created under this line 

# Function for strategy number, Simple Moving Average with Shorting
def Simple_Moving_Average(prices):
    short = 0
    i = 0
    buy = 0
    total_profit = 0
    first_buy = 0
    for p in prices:
        if i >= 5:
            moving_average = (prices[i-1] + prices[i-2] + prices[i-3] + prices[i-4]+ prices[i-5]) / 5
            # Logic for simple moving avereage
            if p < moving_average * 0.95 and buy == 0: # logic to buy a shorted stock
                print("Buying At: ", p)
                buy = p
                if first_buy == 0:
                    first_buy = p
                if short != 0 and buy != 0:
                     # sets total profit to be calculated on a buy
                    print("Profit from this Trade: ", short - buy)
                    print("Total Profit: ", total_profit)
                short = 0
                # Tells you if you should buy a certain stock today
                if i == len(prices)-1:
                    print("\t\t\t -------------- You should buy", ticker, "today!!")
                    
            elif p > moving_average * 1.05 and short == 0: # logic to sell a shorted stock 
                short = p
                print("Shorting this stock at: ", p)
                if short != 0 or buy != 0:
                    total_profit += (short - buy)
                    print("Profit from this Trade: ", short - buy)
                    print("Total profit", total_profit)
                buy = 0
                # Tells you if you should buy a certain stock today
                if i == len(prices)-1:
                    print("\t\t\t -------------- You should sell ", ticker, "today!!")
                
        i += 1
        
    print("\n----------------------------------------\n\n")
    final_percentage = (total_profit / prices[0]) * 100
    print("First Buy: ", first_buy)
    print("Total Profit: ", total_profit)
    print("final_percetnage: ", final_percentage, "%")
    
    return total_profit, final_percentage

# Function for strategy number 2, Mean Reversion 
def MeanReversion(prices):
    buy = 0
    first_buy = 0
    total_profit = 0
    
    for i in range(len(prices)):
        current_price = prices[i]
        
        if i >= 6:
            average_price = (prices[i-1] + prices[i-2] + prices[i-3] + prices[i-4] + prices[i-5]) / 5
            
            if current_price < average_price * 0.95 and buy == 0:
                print("Buying this stock at: ", current_price)
                buy = current_price
                if first_buy == 0:
                    first_buy = prices[i]
                    
            elif current_price > average_price * 1.05 and buy != 0:
                print("Selling this stock at: ", current_price)
                trade_profit = (current_price - buy)
                
                print("Profit from this Trade: ", round(trade_profit, 2))
                total_profit += trade_profit
                buy = 0
                
            i += 1
    
    print("\n\n---------------------------\n\n")
    
    percentage_return = (total_profit / first_buy) * 100
    print(ticker, "Total Profit: ", round(total_profit, 2))
    print(ticker, "First Buy: ", first_buy)
    print(ticker, "Percentage Return: ", round(percentage_return, 2), "%") 
    
    return total_profit, percentage_return
    
    
# Function for strategy number 3, Bollinger Bands 
def BollingerBands(prices):
    i = 0
    buy = 0
    total_profit = 0
    first_buy = 0
    for p in prices:
        if i >= 5:
            moving_average = (prices[i-1] + prices[i-2] + prices[i-3] + prices[i-4] + prices[i-5]) / 5
            # Logic for simple moving avereage
            if p > moving_average * 1.05 and buy == 0:
                print("Buying this Stock at: ", p)
                buy = p
                if first_buy == 0:
                    first_buy = p
            elif p < moving_average * 0.95 and buy != 0:
                print("Selling this stock at: ", p)
                print("Trade Profit: ", p - buy)
                total_profit += (p - buy)
                buy = 0
        i += 1
        
    print("\n----------------------------------------\n\n")
    final_percentage = (total_profit / first_buy) * 100
    print("First Buy: ", first_buy)
    print("Total Profit: ", total_profit)
    print("Final_Percetnage: ", final_percentage, "%")
    
    return total_profit, final_percentage
    

# Run analyses on the 10 tickers, from the stored CSV files in FinalProject folder, and append the results from the analysis into a JSON file 

tickers = ["AAPL", "ADBE", "AMC", "ETSY", "GOOG", "MARA", "RIOT", "STX", "TSLA", "TWLO"]

for ticker in tickers:
    fil = open("/home/ubuntu/environment/FinalProject/" + ticker + ".csv")
    lines = fil.readlines()[1:]
    prices = [float(line.split(',')[4]) for line in lines]
    
    print("\n\n\t\t\t", ticker, "Simple Moving Average Shorting Strategy: \n\n")
    sma_profit, sma_returns = Simple_Moving_Average(prices)
    
    print("\n\n\t\t\t", ticker, "Mean Reversion Shorting Strategy: \n\n" )
    mr_profit, mr_returns  = MeanReversion(prices)
    
    print("\n\n\t\t\t", ticker, "Bollinger Bands Strategy: \n\n")
    bb_profit, bb_returns = BollingerBands(prices)

    saveResults = {}
    for ticker in tickers: 
        
        saveResults[ticker + " Simple Moving Average Shorting Profit:"] = sma_profit
        saveResults[ticker + " Simple Moving Average Shorting Percent Return"] = sma_returns
        saveResults[ticker + " Mean Reversion Shorting Profit: "] = mr_profit
        saveResults[ticker + " Mean Reversion Shorting Percent Return"] = mr_returns    
        saveResults[ticker + " Bollinger Bands Profit: "] = bb_profit
        saveResults[ticker + " Bollinger Bands Percent Return "] = bb_returns        
    
        json.dump(saveResults, open("/home/ubuntu/environment/FinalProject/results.json", "w"))
            
            
print("\n\n\t\t\t---------------------------------------\n\n", "\t\t\tJSON Dictionary (results.json) in Python Terminal: ", "\n\n")
new_dictionary = json.load(open("/home/ubuntu/environment/FinalProject/results.json"))
print(new_dictionary)
    
print("\n\n\t\t\t-------------------------------------\n\n", "\t\t\tTICKER AND STRATEGY WITH HIGHEST RETURN: ", "\n\n")
high_key = ""
high_returns = 0

# Logic for indication on whether it is best to buy or sell a stock based on the date
for key in saveResults:
    if "Return" in key:
        if saveResults[key] > high_returns:
            high_key = key
            high_returns = saveResults[key]
print("Highest Return was", high_key, high_returns)
