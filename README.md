# -Stock-and-Revenue-Analysis-Dashboard-Tesla-vs-GameStop-
import yfinance as yf
tesla = yf.Ticker("TSLA")
tesla_data = tesla.history(period="max")
tesla_data.reset_index(inplace=True)
tesla_data.head()
import requests
from bs4 import BeautifulSoup
import pandas as pd

url = "https://www.macrotrends.net/stocks/charts/TSLA/tesla/revenue"
html_data = requests.get(url).text
soup = BeautifulSoup(html_data, "html.parser")

# Locate the correct table
tables = soup.find_all("table")
for table in tables:
    if "Tesla Quarterly Revenue" in str(table):
        df = pd.read_html(str(table))[0]
        break

tesla_revenue = df.rename(columns={"Tesla Quarterly Revenue(Millions of US $)": "Date", "Tesla Quarterly Revenue(Millions of US $).1": "Revenue"})
tesla_revenue.tail()
gme = yf.Ticker("GME")
gme_data = gme.history(period="max")
gme_data.reset_index(inplace=True)
gme_data.head()
url = "https://www.macrotrends.net/stocks/charts/GME/gamestop/revenue"
html_data = requests.get(url).text
soup = BeautifulSoup(html_data, "html.parser")

tables = soup.find_all("table")
for table in tables:
    if "GameStop Quarterly Revenue" in str(table):
        df = pd.read_html(str(table))[0]
        break

gme_revenue = df.rename(columns={"GameStop Quarterly Revenue(Millions of US $)": "Date", "GameStop Quarterly Revenue(Millions of US $).1": "Revenue"})
gme_revenue.tail()
import plotly.graph_objects as go

def make_graph(stock_data, revenue_data, stock, company):
    fig = go.Figure()
    fig.add_trace(go.Scatter(x=stock_data.Date, y=stock_data.Close, name="Stock Price"))
    fig.add_trace(go.Scatter(x=revenue_data.Date, y=revenue_data.Revenue, name="Revenue"))
    fig.update_layout(title=f"{company} Stock Price and Revenue", xaxis_title="Date", yaxis_title="USD")
    fig.show()

make_graph(tesla_data, tesla_revenue, "TSLA", "Tesla")
make_graph(gme_data, gme_revenue, "GME", "GameStop")
