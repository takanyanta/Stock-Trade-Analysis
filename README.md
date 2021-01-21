# Stock-Trade-Analysis

## Purpose
* To estimate the expected profit by following the technical analysis strategies about stock trade.
* To compare the estimated profits of "*Buy and Hold Strategy(BHS)*" with of "*Buy at Golden cross and Sell at Dead cross Strategy(BGSDS)*".

## Procedure
* Though there are many trading technique by using the stock chart, focusing on the popular technique using Golden and Dead Cross.

### 1. Load Data

* For extracting the stock price data, [*pandas_datareader*](https://pandas-datareader.readthedocs.io/en/latest/) is very useful.
* For being able to change the window size of moving average(MA), the arguments for both "short-term window size" and "long-term window-size" should be assigned.

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

* Define <img src="https://latex.codecogs.com/gif.latex?p_S(t)"> as short-term-MA-price at *t*(time) and <img src="https://latex.codecogs.com/gif.latex?p_L(t)"> as long-term-MA-price  at *t*.
* Define the point as Golden Cross when <img src="https://latex.codecogs.com/gif.latex?p_S(t-1)&space;<&space;p_L(t-1)"> and <img src="https://latex.codecogs.com/gif.latex?p_S(t)&space;>&space;p_L(t)"> hold.
* Define the point as Dead Cross when <img src="https://latex.codecogs.com/gif.latex?p_S(t-1)&space;>&space;p_L(t-1)"> and <img src="https://latex.codecogs.com/gif.latex?p_S(t)&space;<&space;p_L(t)"> hold.

```python
class Golden_Dead_Cross:
    def __init__(self, df):
        self.df = df
    def return_df(self, short, long):
        self.short = short
        self.long = long
        
        self.df["change%"] = self.df["Adj Close"].pct_change()
        self.df["Cross"] = 0
        self.df["Hold"] = 1
        self.df["Assets"] = 1.0
        self.k = 0
        for self.i in range(1, len(self.df)):
            #long>short:GOLDEN, long<short:Dead
            if self.df["MA_{}".format(self.short)].iat[self.i-1] < self.df["MA_{}".format(self.long)].iat[self.i-1] and \
               self.df["MA_{}".format(self.short)].iat[self.i] > self.df["MA_{}".format(self.long)].iat[self.i]:
                self.df["Cross"].iat[self.i] = 1#Buy at GoldenCross
                
            elif self.df["MA_{}".format(self.short)].iat[self.i-1] > self.df["MA_{}".format(self.long)].iat[self.i-1] and \
                 self.df["MA_{}".format(self.short)].iat[self.i] < self.df["MA_{}".format(self.long)].iat[self.i]:
                self.df["Cross"].iat[self.i] = -1#Sell at GoldenCross
                self.k += 1

            if self.k > 0:# num of buy > 0
                if self.df["Cross"].iat[self.i] == 1:
                    self.df["Hold"].iat[self.i] = 1
                elif self.df["Cross"].iat[self.i] == -1:
                    self.df["Hold"].iat[self.i] = 0
                else:
                    self.df["Hold"].iat[self.i] = self.df["Hold"].iat[self.i-1]
    
            if self.df["Hold"].iat[self.i] == 0:
                self.df["Assets"].iat[self.i] = self.df["Assets"].iat[self.i-1]
            else:
                self.df["Assets"].iat[self.i] = self.df["Assets"].iat[self.i-1]*(1+self.df["change%"].iat[self.i])
        return self.df
```
<img src="https://github.com/takanyanta/Technical-Stock-Trade-Analysis/edit/main/komatsu.png">

### 3. Compute the estimated profits of both *BHS* and *BGSDS*

### 4. Summurize the omparison result

## Conclusion
* 
