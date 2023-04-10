# Python project: Getting ETF data


### Challange:

* The idea is to build a code that get data from etf.com and show it in a decent way. However, at first let's focus on the 'getting' part

# Step 1: Installing the necessary libraries and tools

#### In this case, I'll be using google


```python
!pip install selenium
```

    Collecting selenium
      Downloading selenium-4.8.3-py3-none-any.whl (6.5 MB)
         ---------------------------------------- 6.5/6.5 MB 2.9 MB/s eta 0:00:00
    Collecting trio-websocket~=0.9
      Downloading trio_websocket-0.10.2-py3-none-any.whl (17 kB)
    Requirement already satisfied: certifi>=2021.10.8 in c:\users\darta\anaconda3\lib\site-packages (from selenium) (2022.12.7)
    Collecting trio~=0.17
      Downloading trio-0.22.0-py3-none-any.whl (384 kB)
         -------------------------------------- 384.9/384.9 kB 3.0 MB/s eta 0:00:00
    Requirement already satisfied: urllib3[socks]~=1.26 in c:\users\darta\anaconda3\lib\site-packages (from selenium) (1.26.14)
    Collecting async-generator>=1.9
      Using cached async_generator-1.10-py3-none-any.whl (18 kB)
    Requirement already satisfied: cffi>=1.14 in c:\users\darta\anaconda3\lib\site-packages (from trio~=0.17->selenium) (1.15.1)
    Requirement already satisfied: idna in c:\users\darta\anaconda3\lib\site-packages (from trio~=0.17->selenium) (3.4)
    Requirement already satisfied: sortedcontainers in c:\users\darta\anaconda3\lib\site-packages (from trio~=0.17->selenium) (2.4.0)
    Requirement already satisfied: sniffio in c:\users\darta\anaconda3\lib\site-packages (from trio~=0.17->selenium) (1.2.0)
    Collecting outcome
      Using cached outcome-1.2.0-py2.py3-none-any.whl (9.7 kB)
    Requirement already satisfied: attrs>=19.2.0 in c:\users\darta\anaconda3\lib\site-packages (from trio~=0.17->selenium) (22.1.0)
    Collecting exceptiongroup>=1.0.0rc9
      Downloading exceptiongroup-1.1.1-py3-none-any.whl (14 kB)
    Collecting wsproto>=0.14
      Downloading wsproto-1.2.0-py3-none-any.whl (24 kB)
    Requirement already satisfied: PySocks!=1.5.7,<2.0,>=1.5.6 in c:\users\darta\anaconda3\lib\site-packages (from urllib3[socks]~=1.26->selenium) (1.7.1)
    Requirement already satisfied: pycparser in c:\users\darta\anaconda3\lib\site-packages (from cffi>=1.14->trio~=0.17->selenium) (2.21)
    Collecting h11<1,>=0.9.0
      Downloading h11-0.14.0-py3-none-any.whl (58 kB)
         ---------------------------------------- 58.3/58.3 kB ? eta 0:00:00
    Installing collected packages: outcome, h11, exceptiongroup, async-generator, wsproto, trio, trio-websocket, selenium
    Successfully installed async-generator-1.10 exceptiongroup-1.1.1 h11-0.14.0 outcome-1.2.0 selenium-4.8.3 trio-0.22.0 trio-websocket-0.10.2 wsproto-1.2.0
    


```python
!pip install webdriver-manager
```

    Collecting webdriver-manager
      Downloading webdriver_manager-3.8.5-py2.py3-none-any.whl (27 kB)
    Requirement already satisfied: tqdm in c:\users\darta\anaconda3\lib\site-packages (from webdriver-manager) (4.64.1)
    Requirement already satisfied: requests in c:\users\darta\anaconda3\lib\site-packages (from webdriver-manager) (2.28.1)
    Requirement already satisfied: packaging in c:\users\darta\anaconda3\lib\site-packages (from webdriver-manager) (22.0)
    Requirement already satisfied: python-dotenv in c:\users\darta\anaconda3\lib\site-packages (from webdriver-manager) (1.0.0)
    Requirement already satisfied: idna<4,>=2.5 in c:\users\darta\anaconda3\lib\site-packages (from requests->webdriver-manager) (3.4)
    Requirement already satisfied: urllib3<1.27,>=1.21.1 in c:\users\darta\anaconda3\lib\site-packages (from requests->webdriver-manager) (1.26.14)
    Requirement already satisfied: certifi>=2017.4.17 in c:\users\darta\anaconda3\lib\site-packages (from requests->webdriver-manager) (2022.12.7)
    Requirement already satisfied: charset-normalizer<3,>=2 in c:\users\darta\anaconda3\lib\site-packages (from requests->webdriver-manager) (2.0.4)
    Requirement already satisfied: colorama in c:\users\darta\anaconda3\lib\site-packages (from tqdm->webdriver-manager) (0.4.6)
    Installing collected packages: webdriver-manager
    Successfully installed webdriver-manager-3.8.5
    

# Step 2: Importing libraries.


```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager
import time
import pandas as pd
```

# Step 3: Open connection and start collecting data.


```python
driver = webdriver.Chrome(service = Service(ChromeDriverManager().install()))

driver.get('https://www.etf.com/etfanalytics/etf-finder')
```

# Step 3.1: Finding elements on page
#### Expanding list to 100 per page


```python
#waiting the page to load
time.sleep(5)

btn_100 = driver.find_element("xpath",
                               '/html/body/div[5]/section/div/div[3]/section/div/div/div/div/div[2]/section[2]/div[2]/section[2]/div[1]/div/div[4]/button')

btn_100.click()

```

# Step 3.2: Finding elements on page
#### Getting the total number of pages


```python
pg_number = driver.find_element("xpath", '//*[@id="totalPages"]')

pg_number = pg_number.text.replace("of ", "")

pg_number = int(pg_number)

pg_number
```




    32



# Step 3.3: Getting the data table


```python
elem = driver.find_element("xpath", '//*[@id="finderTable"]')

html_t = elem.get_attribute('outerHTML')

table = pd.read_html(str(html_t))[0]

table
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Ticker</th>
      <th>Name</th>
      <th>Segment</th>
      <th>Issuer</th>
      <th>Expense Ratio</th>
      <th>AUM</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>SPY</td>
      <td>SPDR S&amp;P 500 ETF Trust</td>
      <td>Equity: U.S. - Large Cap</td>
      <td>State Street Global Advisors</td>
      <td>0.09%</td>
      <td>$370.35B</td>
    </tr>
    <tr>
      <th>1</th>
      <td>IVV</td>
      <td>iShares Core S&amp;P 500 ETF</td>
      <td>Equity: U.S. - Large Cap</td>
      <td>Blackrock</td>
      <td>0.03%</td>
      <td>$304.93B</td>
    </tr>
    <tr>
      <th>2</th>
      <td>VOO</td>
      <td>Vanguard S&amp;P 500 ETF</td>
      <td>Equity: U.S. - Large Cap</td>
      <td>Vanguard</td>
      <td>0.03%</td>
      <td>$285.54B</td>
    </tr>
    <tr>
      <th>3</th>
      <td>VTI</td>
      <td>Vanguard Total Stock Market ETF</td>
      <td>Equity: U.S. - Total Market</td>
      <td>Vanguard</td>
      <td>0.03%</td>
      <td>$280.86B</td>
    </tr>
    <tr>
      <th>4</th>
      <td>QQQ</td>
      <td>Invesco QQQ Trust</td>
      <td>Equity: U.S. - Large Cap</td>
      <td>Invesco</td>
      <td>0.20%</td>
      <td>$172.61B</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>BIV</td>
      <td>Vanguard Intermediate-Term Bond ETF</td>
      <td>Fixed Income: U.S. - Broad Market, Broad-based...</td>
      <td>Vanguard</td>
      <td>0.04%</td>
      <td>$14.14B</td>
    </tr>
    <tr>
      <th>96</th>
      <td>VXF</td>
      <td>Vanguard Extended Market ETF</td>
      <td>Equity: U.S. - Extended Market</td>
      <td>Vanguard</td>
      <td>0.06%</td>
      <td>$13.83B</td>
    </tr>
    <tr>
      <th>97</th>
      <td>HYG</td>
      <td>iShares iBoxx USD High Yield Corporate Bond ETF</td>
      <td>Fixed Income: U.S. - Corporate, Broad-based Hi...</td>
      <td>Blackrock</td>
      <td>0.48%</td>
      <td>$13.76B</td>
    </tr>
    <tr>
      <th>98</th>
      <td>XLI</td>
      <td>Industrial Select Sector SPDR Fund</td>
      <td>Equity: U.S. Industrials</td>
      <td>State Street Global Advisors</td>
      <td>0.10%</td>
      <td>$13.64B</td>
    </tr>
    <tr>
      <th>99</th>
      <td>VGIT</td>
      <td>Vanguard Intermediate-Term Treasury Index ETF</td>
      <td>Fixed Income: U.S. - Government, Treasury Inve...</td>
      <td>Vanguard</td>
      <td>0.04%</td>
      <td>$13.53B</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 6 columns</p>
</div>




```python
page_table = []

elem = driver.find_element("xpath", '//*[@id="finderTable"]')

for pg in range(1, pg_number + 1):
    
    html_t = elem.get_attribute('outerHTML')
    
    table = pd.read_html(str(html_t))[0]
    
    page_table.append(table)
    
    btn_next_page = driver.find_element("xpath", '//*[@id="nextPage"]')
    
    btn_next_page.click()
    
    
etfs_table = pd.concat(page_table)

etfs_table
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Ticker</th>
      <th>Name</th>
      <th>Segment</th>
      <th>Issuer</th>
      <th>Expense Ratio</th>
      <th>AUM</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>SPY</td>
      <td>SPDR S&amp;P 500 ETF Trust</td>
      <td>Equity: U.S. - Large Cap</td>
      <td>State Street Global Advisors</td>
      <td>0.09%</td>
      <td>$370.35B</td>
    </tr>
    <tr>
      <th>1</th>
      <td>IVV</td>
      <td>iShares Core S&amp;P 500 ETF</td>
      <td>Equity: U.S. - Large Cap</td>
      <td>Blackrock</td>
      <td>0.03%</td>
      <td>$304.93B</td>
    </tr>
    <tr>
      <th>2</th>
      <td>VOO</td>
      <td>Vanguard S&amp;P 500 ETF</td>
      <td>Equity: U.S. - Large Cap</td>
      <td>Vanguard</td>
      <td>0.03%</td>
      <td>$285.54B</td>
    </tr>
    <tr>
      <th>3</th>
      <td>VTI</td>
      <td>Vanguard Total Stock Market ETF</td>
      <td>Equity: U.S. - Total Market</td>
      <td>Vanguard</td>
      <td>0.03%</td>
      <td>$280.86B</td>
    </tr>
    <tr>
      <th>4</th>
      <td>QQQ</td>
      <td>Invesco QQQ Trust</td>
      <td>Equity: U.S. - Large Cap</td>
      <td>Invesco</td>
      <td>0.20%</td>
      <td>$172.61B</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>22</th>
      <td>OAIB</td>
      <td>Teucrium AiLA Long-Short Base Metals Strategy ETF</td>
      <td>Commodities: Industrial Metals</td>
      <td>Teucrium</td>
      <td>--</td>
      <td>--</td>
    </tr>
    <tr>
      <th>23</th>
      <td>IBOT</td>
      <td>VanEck Robotics ETF</td>
      <td>Equity: U.S. Robotics &amp; AI</td>
      <td>VanEck</td>
      <td>--</td>
      <td>--</td>
    </tr>
    <tr>
      <th>24</th>
      <td>CVSE</td>
      <td>Calvert US Select Equity ETF</td>
      <td>Equity: U.S. - Large Cap</td>
      <td>Morgan Stanley</td>
      <td>0.29%</td>
      <td>--</td>
    </tr>
    <tr>
      <th>25</th>
      <td>SROI</td>
      <td>Calamos Antetokounmpo Global Sustainable Equit...</td>
      <td>Equity: Global - Total Market</td>
      <td>Calamos Family Partners</td>
      <td>0.95%</td>
      <td>--</td>
    </tr>
    <tr>
      <th>26</th>
      <td>CVSB</td>
      <td>Calvert Ultra-Short Investment Grade ETF</td>
      <td>Fixed Income: Global - Broad Market, Broad-bas...</td>
      <td>Morgan Stanley</td>
      <td>0.24%</td>
      <td>--</td>
    </tr>
  </tbody>
</table>
<p>3127 rows × 6 columns</p>
</div>



# Step 3.4: Finding elements on page:
#### Going back to page 1


```python
go_back_page = driver.find_element("xpath", '//*[@id="goToPage"]')

go_back_page.clear()
go_back_page.send_keys("1")
go_back_page.send_keys(u'\ue007')
```

# Step 3.5: Finding elements on page:
#### Getting performance data


```python
btn_performance = driver.find_element("xpath", '/html/body/div[5]/section/div/div[3]/section/div/div/div/div/div[2]/section[2]/div[2]/ul/li[2]')

btn_performance.click()

# Looping for getting the data

table_pages = []

elemen = driver.find_element("xpath", '//*[@id="finderTable"]')

for page in range(1, pg_number + 1):
    
    html_t = elemen.get_attribute('outerHTML')
    
    table = pd.read_html(str(html_t))[0]
    
    table_pages.append(table)
    
    btn_next_page = driver.find_element("xpath", '//*[@id="nextPage"]')
    
    driver.execute_script("arguments[0].click();", btn_next_page)
    
    
performance_table = pd.concat(table_pages)

performance_table
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Ticker</th>
      <th>Name</th>
      <th>1 Month</th>
      <th>3 Month</th>
      <th>YTD</th>
      <th>1 Year</th>
      <th>3 Years</th>
      <th>5 Years</th>
      <th>10 Years</th>
      <th>As Of Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>SPY</td>
      <td>SPDR S&amp;P 500 ETF Trust</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>04/05/23</td>
    </tr>
    <tr>
      <th>1</th>
      <td>IVV</td>
      <td>iShares Core S&amp;P 500 ETF</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>04/05/23</td>
    </tr>
    <tr>
      <th>2</th>
      <td>VOO</td>
      <td>Vanguard S&amp;P 500 ETF</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>04/05/23</td>
    </tr>
    <tr>
      <th>3</th>
      <td>VTI</td>
      <td>Vanguard Total Stock Market ETF</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>04/05/23</td>
    </tr>
    <tr>
      <th>4</th>
      <td>QQQ</td>
      <td>Invesco QQQ Trust</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>04/05/23</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>22</th>
      <td>OAIB</td>
      <td>Teucrium AiLA Long-Short Base Metals Strategy ETF</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>04/05/23</td>
    </tr>
    <tr>
      <th>23</th>
      <td>IBOT</td>
      <td>VanEck Robotics ETF</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>04/05/23</td>
    </tr>
    <tr>
      <th>24</th>
      <td>CVSE</td>
      <td>Calvert US Select Equity ETF</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>04/05/23</td>
    </tr>
    <tr>
      <th>25</th>
      <td>SROI</td>
      <td>Calamos Antetokounmpo Global Sustainable Equit...</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>04/05/23</td>
    </tr>
    <tr>
      <th>26</th>
      <td>CVSB</td>
      <td>Calvert Ultra-Short Investment Grade ETF</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>04/05/23</td>
    </tr>
  </tbody>
</table>
<p>3127 rows × 10 columns</p>
</div>




```python
#Closing driver
driver.quit()
```

# Setp 4: Building the final table.


```python
performance_table = performance_table.set_index("Ticker")
performance_table = performance_table[['1 Year', '3 Years', '5 Years']]
etfs_table = etfs_table.set_index("Ticker")
```


```python
final_db = etfs_table.join(tabela_rentabilidade_etfs, how = 'inner')

final_db
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Name</th>
      <th>Segment</th>
      <th>Issuer</th>
      <th>Expense Ratio</th>
      <th>AUM</th>
      <th>1 Year</th>
      <th>3 Years</th>
      <th>5 Years</th>
    </tr>
    <tr>
      <th>Ticker</th>
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
      <th>SPY</th>
      <td>SPDR S&amp;P 500 ETF Trust</td>
      <td>Equity: U.S. - Large Cap</td>
      <td>State Street Global Advisors</td>
      <td>0.09%</td>
      <td>$370.35B</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
    </tr>
    <tr>
      <th>IVV</th>
      <td>iShares Core S&amp;P 500 ETF</td>
      <td>Equity: U.S. - Large Cap</td>
      <td>Blackrock</td>
      <td>0.03%</td>
      <td>$304.93B</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
    </tr>
    <tr>
      <th>VOO</th>
      <td>Vanguard S&amp;P 500 ETF</td>
      <td>Equity: U.S. - Large Cap</td>
      <td>Vanguard</td>
      <td>0.03%</td>
      <td>$285.54B</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
    </tr>
    <tr>
      <th>VTI</th>
      <td>Vanguard Total Stock Market ETF</td>
      <td>Equity: U.S. - Total Market</td>
      <td>Vanguard</td>
      <td>0.03%</td>
      <td>$280.86B</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
    </tr>
    <tr>
      <th>QQQ</th>
      <td>Invesco QQQ Trust</td>
      <td>Equity: U.S. - Large Cap</td>
      <td>Invesco</td>
      <td>0.20%</td>
      <td>$172.61B</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>OAIB</th>
      <td>Teucrium AiLA Long-Short Base Metals Strategy ETF</td>
      <td>Commodities: Industrial Metals</td>
      <td>Teucrium</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
    </tr>
    <tr>
      <th>IBOT</th>
      <td>VanEck Robotics ETF</td>
      <td>Equity: U.S. Robotics &amp; AI</td>
      <td>VanEck</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
    </tr>
    <tr>
      <th>CVSE</th>
      <td>Calvert US Select Equity ETF</td>
      <td>Equity: U.S. - Large Cap</td>
      <td>Morgan Stanley</td>
      <td>0.29%</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
    </tr>
    <tr>
      <th>SROI</th>
      <td>Calamos Antetokounmpo Global Sustainable Equit...</td>
      <td>Equity: Global - Total Market</td>
      <td>Calamos Family Partners</td>
      <td>0.95%</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
    </tr>
    <tr>
      <th>CVSB</th>
      <td>Calvert Ultra-Short Investment Grade ETF</td>
      <td>Fixed Income: Global - Broad Market, Broad-bas...</td>
      <td>Morgan Stanley</td>
      <td>0.24%</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
      <td>--</td>
    </tr>
  </tbody>
</table>
<p>3127 rows × 8 columns</p>
</div>


