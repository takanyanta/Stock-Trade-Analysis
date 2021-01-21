# Stock-Trade-Analysis

## Purpose
* To estimate the expected profit by following the technical analysis strategies about stock trade.
* To compare the estimated profits of "*Buy and Hold Strategy(BHS)*" with of "*Buy at Golden cross and Sell at Dead cross Strategy(BGSDS)*".

## Procedure
* Though there are many trading technique by using the stock chart, focusing on the popular technique using Golden and Dead Cross.

### 1. Load Data

* For extracting the stock price data, [*pandas_datareader*](https://pandas-datareader.readthedocs.io/en/latest/) is very useful.
* For being able to change the window size of moving average, the arguments for both "short-term window size" and "long-term window-size" should be assigned.

```python
class Load_data:
    def __init__(self, ticker):
        self.ticker = ticker
    def return_df(self):
        self.df = DataReader(self.ticker, "yahoo", start=datetime(2000, 1, 1))
        self.df = self.df.sort_index()
        self.df = self.df[self.df["Volume"] > 0]
        return self.df
    def add_MA(self, df, window_short=25, window_long=100, visible = False):
        self.df = df
        self.window_short = window_short
        self.window_long = window_long
        self.visible = visible
        
        self.df["MA_{}".format(int(self.window_short))] = self.df["Adj Close"].rolling(window=self.window_short).mean()
        self.df["MA_{}".format(int(self.window_long))] = self.df["Adj Close"].rolling(window=self.window_long).mean()
        
        if visible == True:
            plt.figure(figsize=(5, 5))
            plt.plot(self.df["Adj Close"], label="Original")
            plt.plot(self.df["MA_{}".format(int(self.window_short))], label="Short_MA(window={})".format(self.window_short))
            plt.plot(self.df["MA_{}".format(int(self.window_long))], label="Long_MA(window{})".format(self.window_long))
            plt.legend()
            plt.show()
        
        return self.df
    def add_Std(self, df, window = 25):
        self.df = df
        self.window=window
        self.df["Std"] = self.df["Adj Close"].rolling(window = self.window).std()
        return self.df
```

### 2. Define the point of both  Golden and Dead Cross.

* 

### 3. Compute the estimated profits of both *BHS* and *BGSDS*

### 4. Summurize the omparison result

## Conclusion
* 
