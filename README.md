# Stock-Revenue# ---
# Project: Extracting and Visualizing Stock Data
# Date: 03/26/2026
# Author: Corey Brown Jr
# ---

# [span_3](start_span)Install necessary libraries if running locally[span_3](end_span)
# !pip install yfinance bs4 nbformat matplotlib requests

import yfinance as yf
import pandas as pd
import requests
from bs4 import BeautifulSoup
import matplotlib.pyplot as plt
import warnings

# [span_4](start_span)Ignore all warnings[span_4](end_span)
warnings.filterwarnings("ignore", category=FutureWarning)

# -[span_5](start_span)[span_6](start_span)-- Define Graphing Function[span_5](end_span)[span_6](end_span) ---
def make_graph(stock_data, revenue_data, stock):
    stock_data_specific = stock_data[stock_data.Date < '2021-06-14']
    revenue_data_specific = revenue_data[revenue_data.Date < '2021-04-30']
    
    fig, axes = plt.subplots(2, 1, figsize=(12, 8), sharex=True)

    # [span_7](start_span)Stock price plot[span_7](end_span)
    axes[0].plot(pd.to_datetime(stock_data_specific.Date), stock_data_specific.Close.astype("float"), label="Share Price")
    axes[0].set_ylabel("Price ($US)")
    axes[0].set_title(f"{stock} Historical Share Price")

    # [span_8](start_span)Revenue plot[span_8](end_span)
    axes[1].plot(pd.to_datetime(revenue_data_specific.Date), revenue_data_specific.Revenue.astype("float"), label="Revenue")
    axes[1].set_ylabel("Revenue ($US Millions)")
    axes[1].set_xlabel("Date")
    axes[1].set_title(f"{stock} Historical Revenue")

    plt.tight_layout()
    plt.show()

# -[span_9](start_span)-- Question 1: Use yfinance to Extract Tesla Stock Data[span_9](end_span) ---
tesla = yf.Ticker("TSLA")
tesla_data = tesla.history(period="max")
[span_10](start_span)tesla_data.reset_index(inplace=True) #[span_10](end_span)
print("Tesla Stock Data (First 5 Rows):")
print(tesla_data.head())

# -[span_11](start_span)[span_12](start_span)-- Question 2: Use Webscraping to Extract Tesla Revenue Data[span_11](end_span)[span_12](end_span) ---
url_tesla = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-Skills Network/labs/project/revenue.htm"
html_data = requests.get(url_tesla).text
[span_13](start_span)soup = BeautifulSoup(html_data, 'html.parser') #[span_13](end_span)

# [span_14](start_span)Extracting the table using the specific index mentioned[span_14](end_span)
tesla_revenue = pd.read_html(str(soup))[1]
tesla_revenue.columns = ['Date', 'Revenue']

# [span_15](start_span)Data Cleaning[span_15](end_span)
tesla_revenue["Revenue"] = tesla_revenue['Revenue'].str.replace(',|\$', "", regex=True)
tesla_revenue.dropna(inplace=True)
tesla_revenue = tesla_revenue[tesla_revenue['Revenue'] != ""]

print("\nTesla Revenue Data (Last 5 Rows):")
print(tesla_revenue.tail())

# -[span_16](start_span)-- Question 3: Use yfinance to Extract GameStop Stock Data[span_16](end_span) ---
gme = yf.Ticker("GME")
gme_data = gme.history(period="max")
[span_17](start_span)gme_data.reset_index(inplace=True) #[span_17](end_span)
print("\nGameStop Stock Data (First 5 Rows):")
print(gme_data.head())

# -[span_18](start_span)[span_19](start_span)-- Question 4: Use Webscraping to Extract GME Revenue Data[span_18](end_span)[span_19](end_span) ---
url_gme = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-Skills Network/labs/project/stock.html"
html_data_2 = requests.get(url_gme).text
[span_20](start_span)soup_2 = BeautifulSoup(html_data_2, 'html.parser') #[span_20](end_span)

[span_21](start_span)gme_revenue = pd.read_html(str(soup_2))[1] #[span_21](end_span)
gme_revenue.columns = ['Date', 'Revenue']

# [span_22](start_span)[span_23](start_span)Data Cleaning[span_22](end_span)[span_23](end_span)
gme_revenue["Revenue"] = gme_revenue['Revenue'].str.replace(',|\$', "", regex=True)
gme_revenue.dropna(inplace=True)
gme_revenue = gme_revenue[gme_revenue['Revenue'] != ""]

print("\nGameStop Revenue Data (Last 5 Rows):")
print(gme_revenue.tail())

# -[span_24](start_span)[span_25](start_span)-- Question 5: Plot Tesla Stock Graph[span_24](end_span)[span_25](end_span) ---
make_graph(tesla_data, tesla_revenue, 'Tesla')

# -[span_26](start_span)[span_27](start_span)-- Question 6: Plot GameStop Stock Graph[span_26](end_span)[span_27](end_span) ---
make_graph(gme_data, gme_revenue, 'GameStop')
