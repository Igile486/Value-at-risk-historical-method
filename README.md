# Value-at-risk-historical-method
This program allows, using the historical returns, to calculate the value at risk of a portfolio. If you have any advice to make it more efficient and fast let me know


import numpy as np
import pandas as pd
import datetime as dt
import yfinance as yf
import matplotlib.pyplot as plt
from scipy.stats import norm




portfolio_value = input("What is your portfolio value: ")

try:
    portfolio_value = float(portfolio_value)
    print("Your portfolio value is:", portfolio_value)

except ValueError:
    print("Erreur, ce n'est pas un nombre")

#try:
    #Number_stock = float(Number_stock)
    #print(f'You own {Number_stock} of a value of {portfolio_value}')

#except ValueError:
    #print("Erreur, ce n'est pas un nombre")

user_input = input("Enter stock=value pairs separated by commas: ")

data = {}
for pair in user_input.split(","):
    if "=" in pair:
        key, value = pair.split("=")
        data[key.strip()] = float(value.strip())
        

print("Dictionary:", data)

if sum(data.values())!= portfolio_value:
    print('les valeurs ne correspondent pas')
    SystemExit ('Les valeurs ne correspondent pas')

Tickers=keys_list = list(data.keys())
print(Tickers)

Weight=[val/sum(data.values()) for val in data.values()]
print(Weight)

end_date=dt.datetime.now()
startDate=end_date-dt.timedelta(days=365*3)
df= yf.download(Tickers,end=end_date,start=startDate,auto_adjust=False)
#print(df.head())

ADJ= df['Adj Close']
log_ADJ=np.log(ADJ/ADJ.shift(1))
log_ADJ=log_ADJ.dropna()
print(log_ADJ.head())

weighted_log_returns = log_ADJ.mul(Weight, axis=1)
portfolio_log_return = weighted_log_returns.sum(axis=1)

print("Rendements log pondérés (premières lignes) :\n", weighted_log_returns.head())
print("\nRendement log total du portefeuille :\n", portfolio_log_return.head())

Alpha=input('quel est le niveau de confiance')

try:
    Alpha=float(Alpha)
    if Alpha>1 or Alpha<0:
        print('alpha doit etre entre 0 et 1')
    else:
        Alpha=1-Alpha
except ValueError:
    print("ce n'est pas un nombre")
    SystemExit

print("VaR =", portfolio_value * abs(portfolio_log_return.quantile(Alpha)))

