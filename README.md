# Stock Trading Simulator ABOUT: 
This was a final project for Data 3500 (Fall 2021) at Utah State University, where we were able to use a variety of skills learned from this semester.

# Features:
- Data was obtained through a web JSON API.
- Ran multiple technical trading strategies including: bollinger bands, mean reversion, simple moving average crossover in Python on AWS. The simple moving average strategy additionally had a shorting strategy added to it, where the bot would short the specified stocks and sell based on provided thresholds.
  - Strategies were run for 10 stock tickers.
  - Inside each strategy is additional logic stating if it is best to buy or sell stock based on the day (as long as new data is appended) 
- Strategies yielded as high as 43% annually. 
  - Returns using data spanning from 1999 to 2021: AAPL Bollinger Bands Percent Return at 489.23%.
- Managed all data scripts and server side logic. 
- Data is created and appended to CSV files, where the functions can effectively pull the data from and then perform analysis.
  - Once each strategy is performed on each specified stock, the results are added to a .JSON file.


# Results
- Strategies yielded as high as 43% annually. 
  - Returns using data spanning from 1999 to 2021: AAPL Bollinger Bands Percent Return at 489%.
- Once each strategy is performed on each specified stock, the results are added to a .JSON file.
- The first and second screenshots show outputs for the highest return and the given stock, and the associate output that is with each stock (each stock ticker has differing results)
- The third screenshot shows the JSON file format and associated outputs. 

<img width="582" alt="Screen Shot 2021-12-26 at 5 33 16 PM" src="https://user-images.githubusercontent.com/96510784/147425602-c17153f6-ed7b-45b5-aa0e-266264df06a9.png">
<img width="313" alt="Screen Shot 2021-12-26 at 5 35 24 PM" src="https://user-images.githubusercontent.com/96510784/147425660-13a005f4-0bbf-491d-8e3c-95b9fcd9d623.png"><img width="1060" alt="Screen Shot 2021-12-26 at 5 42 09 PM" src="https://user-images.githubusercontent.com/96510784/147425958-ca9fe28a-8cf1-45b5-8c58-95e1fc6c3261.png">


# Acquired Knowledge

