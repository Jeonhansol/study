# !pip install yfinance
# !pip install selenium
# !pip install webdriver-manager

import yfinance as yf
import pandas as pd

# Extract Tesla stock data
tesla_data = yf.Ticker('TSLA').history(period="max")
tesla_data.reset_index(inplace=True)  # Reset index
tesla_data.head()  # Display first 5 rows

from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from webdriver_manager.chrome import ChromeDriverManager
import pandas as pd
import time

# 1. Set up Chrome WebDriver
driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))

# 2. Navigate to Tesla revenue page
url = "https://www.macrotrends.net/stocks/charts/TSLA/tesla/revenue"
driver.get(url)

# 3. Use WebDriverWait to wait for the table to be loaded (increased to 20 seconds)
try:
    # Try finding the table using a broader search (class name instead of full XPath)
    table = WebDriverWait(driver, 20).until(
        EC.presence_of_element_located((By.CLASS_NAME, 'historical_data_table'))
    )
    
    # 4. Extract the table HTML
    html_content = table.get_attribute('outerHTML')

    # 5. Read the table into a DataFrame
    tesla_revenue = pd.read_html(html_content)[0]
    tesla_revenue.columns = ['Date', 'Revenue']
    tesla_revenue['Revenue'] = tesla_revenue['Revenue'].str.replace(',', '').str.replace('$', '').astype(float)
    tesla_revenue.dropna(inplace=True)

    # 6. Display the last 5 rows of the DataFrame
    print(tesla_revenue.tail())

finally:
    # Close the browser
    driver.quit()


# Extract GameStop stock data
gme_data = yf.Ticker('GME').history(period="max")
gme_data.reset_index(inplace=True)  # Reset index
gme_data.head()  # Display first 5 rows

from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from webdriver_manager.chrome import ChromeDriverManager
import pandas as pd
import time

# 1. Chrome WebDriver 설정
driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))

# 2. GameStop Revenue 페이지로 이동
url = "https://www.macrotrends.net/stocks/charts/GME/gamestop/revenue"
driver.get(url)

# 3. 테이블이 로드될 때까지 WebDriverWait를 사용해 대기 (최대 20초 대기)
try:
    # 테이블을 class 이름으로 찾기
    table = WebDriverWait(driver, 20).until(
        EC.presence_of_element_located((By.CLASS_NAME, 'historical_data_table'))
    )
    
    # 4. 테이블 HTML 추출
    html_content = table.get_attribute('outerHTML')

    # 5. Pandas를 사용해 HTML 테이블을 읽기
    gamestop_revenue = pd.read_html(html_content)[0]
    gamestop_revenue.columns = ['Date', 'Revenue']
    gamestop_revenue['Revenue'] = gamestop_revenue['Revenue'].str.replace(',', '').str.replace('$', '').astype(float)
    
    # 결측치 제거
    gamestop_revenue.dropna(inplace=True)

    # 6. GameStop Revenue의 마지막 5개 행 출력
    print(gamestop_revenue.tail())

finally:
    # 브라우저 닫기
    driver.quit()

import plotly.graph_objects as go

def make_graph(stock_data, revenue_data, stock_name):
    fig = go.Figure()

    # Stock data plot
    fig.add_trace(go.Scatter(x=stock_data['Date'], y=stock_data['Close'], mode='lines', name=f'{stock_name} Stock Price'))

    # Revenue data plot
    fig.add_trace(go.Scatter(x=revenue_data['Date'], y=revenue_data['Revenue'], mode='lines', name=f'{stock_name} Revenue', yaxis='y2'))

    # Update layout
    fig.update_layout(
        title=f'{stock_name} Stock Price vs Revenue',
        xaxis_title='Date',
        yaxis_title='Stock Price (USD)',
        yaxis2=dict(title='Revenue (USD Millions)', overlaying='y', side='right'),
        legend=dict(x=0, y=1.1),
    )

    fig.show()

make_graph(tesla_data, tesla_revenue, 'Tesla')

make_graph(gme_data, gamestop_revenue, 'GameStop')
