# Data 200: Database Systems and Data Management for Data Analytics


# Take-Home Final Exam

**Dickinson College**<br/>
**Fall 2022**<br/>
Prof. Eren Bilen<br/>
<font color='red'>**Due Date:** Friday December 16, 12pm (noon) </font>
---
Enter your name in the markdown cell below.

# Name: Tai Nguyen


```python
from selenium import webdriver
import pandas as pd
import re
import time 
import random
import matplotlib.pyplot as plt
```

# Scraping task

On this exam, you will scrape reviews from BestBuy.com. The item for which you will scrape reviews for will depend on the first letter of your last name. Check the table below (both letters inclusive) for your assigned item:

|First letter of your last name | Item/link to reviews on BestBuy.com
| :---        |    :---   |
|A-G | https://www.bestbuy.com/site/reviews/microsoft-xbox-series-x-1tb-console-black/6428324?variant=A&skuId=6428324
|H-M | https://www.bestbuy.com/site/reviews/apple-airpods-with-charging-case-2nd-generation-white/6084400?variant=A&skuId=6084400
|N-S | https://www.bestbuy.com/site/reviews/samsung-65-class-7-series-led-4k-uhd-smart-tizen-tv/6401722?variant=A&skuId=6401722
|T-Z | https://www.bestbuy.com/site/reviews/nintendo-switch-32gb-lite-turquoise/6257139?variant=A&skuId=6257139


Your goal is to scrape 2,000 reviews that were posted in the reviews page of your assigned item. The format of your output should be a dataframe with the following columns/format:
<br>
![title](ss1.png)

where
```
username: the user name of the reviewer
review: the review content
rating: the rating (out of 5) left by the reviewer
helpful: the number of votes received on the review indicating that the review is helpful
unhelpful: the number of votes received on the review indicating that the review is unhelpful
net: |helpful - unhelpful| i.e., the absolute value of the difference between "helpful" and "unhelpful" votes
nethelpful: helpful - unhelpful i.e., the difference between "helpful" and "unhelpful" votes
total: total number of votes (helpful + unhelpful) received
length: length of the review, defined as the number of characters in a review (including whitespace)
```

Include the `.ipynb` and `.md` versions of your work + the following two scatterplots as part of your output. Comment on what you observe from the scatterplots. Are you surprised?
* scatterplot with `length` on the x axis, `total` on the y axis
* scatterplot with `length` on the x axis, `nethelpful` on the y axis

Commit and push on Github once you finish.

Hints: 
* You will need to scrape multiple pages. Make sure to wait at least 2 seconds between each page, e.g., using `time.sleep(random.uniform(2, 3))`.
* Trim away `\n` from review strings as it contains no useful information, and can cause bugs in regex.
* Make sure to get the position of the "next Page" button, as its position can change dynamically depending on the page number.

Good luck!


```python
driver = webdriver.Chrome(r"C:\Users\HP\Downloads\chromedriver.exe")
```

    C:\Users\HP\AppData\Local\Temp\ipykernel_14824\2532227612.py:1: DeprecationWarning: executable_path has been deprecated, please pass in a Service object
      driver = webdriver.Chrome(r"C:\Users\HP\Downloads\chromedriver.exe")
    


```python
driver.quit()
```


```python
!pip install selenium
```


```python
driver.get('https://www.bestbuy.com/site/reviews/samsung-65-class-7-series-led-4k-uhd-smart-tizen-tv/6401722?variant=A&skuId=6401722')
```


```python
#username
user_name = driver.find_element('xpath','//*[@id="reviews-accordion"]/div[1]/ul/li[1]/div/div[2]/div[2]/div[1]/span')
name = user_name.text
```


```python
name
```




    'LaCresa'




```python
#review
user_review = driver.find_element('xpath','//*[@id="reviews-accordion"]/div[1]/ul/li[1]/div/div[2]/div[4]/p')
review = user_review.text
review
```




    'The television is great, its giving me everything that i wantes out of it. My only issue was with Best Buy. My purchase experience was horrible. It was a complete waste of time to purchase online for delivery with total tech install that was never delivered, i had to repurchase and pick up in store where they gave me the floor model with no power cord. I had to return to the store again for-the cord and was not discount on the TV for being the floor model. I am a very dissatisfied customer!'




```python
#rating
user_rating = driver.find_element('xpath','//*[@id="reviews-accordion"]/div[1]/ul/li[1]/div/div[2]/div[1]/div/div/p')
rating = user_rating.text
rating
```




    'Rated 5 out of 5 stars'




```python
#helpful
user_helpful = driver.find_element('xpath','//*[@id="reviews-accordion"]/div[1]/ul/li[1]/div/div[2]/div[6]/div/div/div[1]/button[1]')
helpful = re.findall(r'\d+', user_helpful.text)
helpful
```




    ['0']




```python
#unhelpful
user_unhelpful = driver.find_element('xpath','//*[@id="reviews-accordion"]/div[1]/ul/li[1]/div/div[2]/div[6]/div/div/div[1]/button[2]')
unhelpful = re.findall(r'\d+', user_unhelpful.text)
unhelpful
```




    ['0']




```python

```


```python
# scraping a single page,
reviews=[]

for x in range(20):

    user_name = driver.find_element('xpath', "//*[@id='reviews-accordion']/div[1]/ul/li[" + str(x+1) + "]/div/div[2]/div[2]/div[1]/span")
    name = user_name.text   
    
    user_review = driver.find_element('xpath',"//*[@id='reviews-accordion']/div[1]/ul/li[" + str(x+1) + "]/div/div[2]/div[4]/p")
    review = user_review.text

    user_rating = driver.find_element('xpath', "//*[@id='reviews-accordion']/div[1]/ul/li[" + str(x+1) + "]/div/div[2]/div[1]/div/div/p")
    rating = re.findall(r'([0-9]+) .* ',user_rating.text)
    
    user_helpful = driver.find_element('xpath', "//*[@id='reviews-accordion']/div[1]/ul/li[" + str(x+1) + "]/div/div[2]/div[6]/div/div/div[1]/button[1]")
    helpful = re.findall(r'\d+',user_helpful.text)
    
    user_unhelpful = driver.find_element('xpath', "//*[@id='reviews-accordion']/div[1]/ul/li[" + str(x+1) + "]/div/div[2]/div[6]/div/div/div[1]/button[2]")
    unhelpful = re.findall(r'\d+',user_unhelpful.text)
    

    reviews.append([name,review,rating,helpful,unhelpful]) 
```


```python
# scrapping one page
df_single = pd.DataFrame(reviews, columns=['name','review','rating','helpful','unhelpful'])
df_single
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
      <th>name</th>
      <th>review</th>
      <th>rating</th>
      <th>helpful</th>
      <th>unhelpful</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td></td>
      <td>The television is great, its giving me everyth...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>1</th>
      <td></td>
      <td>well this TV for my office is so great TV I lo...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>2</th>
      <td></td>
      <td>Very sharp TV with speedy interface and stream...</td>
      <td>[5]</td>
      <td>[5]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>3</th>
      <td></td>
      <td>Great picture, excellent price, very user frie...</td>
      <td>[5]</td>
      <td>[1]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>4</th>
      <td></td>
      <td>I purchased this TV UN65TUu7000FXZA a week ago...</td>
      <td>[5]</td>
      <td>[145]</td>
      <td>[68]</td>
    </tr>
    <tr>
      <th>5</th>
      <td></td>
      <td>This 65" Samsung TU7000 Series 4K UHD set is c...</td>
      <td>[5]</td>
      <td>[13]</td>
      <td>[8]</td>
    </tr>
    <tr>
      <th>6</th>
      <td></td>
      <td>I replaced my old LG 65inch because it color g...</td>
      <td>[5]</td>
      <td>[90]</td>
      <td>[34]</td>
    </tr>
    <tr>
      <th>7</th>
      <td></td>
      <td>Love the tv. I bought another Samsung 75 inch ...</td>
      <td>[4]</td>
      <td>[0]</td>
      <td>[1]</td>
    </tr>
    <tr>
      <th>8</th>
      <td></td>
      <td>GREAT DELIVERY SERVICE ON TIME!!!! I REALLY LI...</td>
      <td>[4]</td>
      <td>[50]</td>
      <td>[32]</td>
    </tr>
    <tr>
      <th>9</th>
      <td></td>
      <td>We are really enjoying our new Samsung 65"TV. ...</td>
      <td>[4]</td>
      <td>[1]</td>
      <td>[1]</td>
    </tr>
    <tr>
      <th>10</th>
      <td></td>
      <td>Its a great Tv , i Can use the smartview if i ...</td>
      <td>[5]</td>
      <td>[2]</td>
      <td>[1]</td>
    </tr>
    <tr>
      <th>11</th>
      <td></td>
      <td>This is the third 7 series I have own and real...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>12</th>
      <td></td>
      <td>Great color but a small issue. It varies on th...</td>
      <td>[4]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>13</th>
      <td></td>
      <td>￼ I had an LG and decided to upgrade to a Sams...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>14</th>
      <td></td>
      <td>My second one due to toddler pulling a cover a...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>15</th>
      <td></td>
      <td>Best tv I’ve bought. The picture is perfect, b...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>16</th>
      <td></td>
      <td>When it fits, it fits. Great visual and audio ...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>17</th>
      <td></td>
      <td>So quick and easy.. very helpful staff and sup...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>18</th>
      <td></td>
      <td>Perfect size for our living room! It is easy t...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>19</th>
      <td></td>
      <td>This tv is amazing does all the stuff a need p...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
  </tbody>
</table>
</div>




```python

```


```python
driver.get('https://www.bestbuy.com/site/reviews/samsung-65-class-7-series-led-4k-uhd-smart-tizen-tv/6401722?variant=A&skuId=6401722')
```


```python
ls = ['p1','p2','p3','p4','p5','p6','p7','p8','p9','p10',
     'p11','p12','p13','p14','p15','p16','p17','p18','p19','p20',
     'p21','p22','p23','p24','p25','p26','p27','p28','p29','p30',
     'p31','p32','p33','p34','p35','p36','p37','p38','p39','p40',
     'p41','p42','p43','p44','p45','p46','p47','p48','p49','p50',
     'p51','p52','p53','p54','p55','p56','p57','p58','p59','p60',
     'p61','p62','p63','p64','p65','p66','p67','p68','p69','p70',
     'p71','p72','p73','p74','p75','p76','p77','p78','p79','p80',
     'p81','p82','p83','p84','p85','p86','p87','p88','p89','p90',
     'p91','p92','p93','p94','p95','p96','p97','p98','p99','p100']
```


```python
reviews=[]
for i in ls:
    print(i)
    driver.get('https://www.bestbuy.com/site/reviews/samsung-65-class-7-series-led-4k-uhd-smart-tizen-tv/6401722?variant=A&skuId=6401722' + i)
    
    # scraping a single page
    for x in range(20):

        user_name = driver.find_element('xpath', "//*[@id='reviews-accordion']/div[1]/ul/li[" + str(x+1) + "]/div/div[2]/div[2]/div[1]/span")
        name = user_name.text   

        user_review = driver.find_element('xpath',"//*[@id='reviews-accordion']/div[1]/ul/li[" + str(x+1) + "]/div/div[2]/div[4]/p")
        review = user_review.text

        user_rating = driver.find_element('xpath', "//*[@id='reviews-accordion']/div[1]/ul/li[" + str(x+1) + "]/div/div[2]/div[1]/div/div/p")
        rating = re.findall(r'([0-9]+) .* ',user_rating.text)

        user_helpful = driver.find_element('xpath', "//*[@id='reviews-accordion']/div[1]/ul/li[" + str(x+1) + "]/div/div[2]/div[6]/div/div/div[1]/button[1]")
        helpful = re.findall(r'\d+',user_helpful.text)

        user_unhelpful = driver.find_element('xpath', "//*[@id='reviews-accordion']/div[1]/ul/li[" + str(x+1) + "]/div/div[2]/div[6]/div/div/div[1]/button[2]")
        unhelpful = re.findall(r'\d+',user_unhelpful.text)

        reviews.append([name,review,rating,helpful,unhelpful])  
        
    time.sleep(random.uniform(2,3))
    button = driver.find_element('xpath', '//*[@class="pagination ugc body-copy-lg"]/li[@class="page next"]/a')
    button.click()  
    
df_out = pd.DataFrame(reviews, columns=['name','review','rating','helpful','unhelpful'])
```

    p1
    p2
    p3
    p4
    p5
    p6
    p7
    p8
    p9
    p10
    p11
    p12
    p13
    p14
    p15
    p16
    p17
    p18
    p19
    p20
    p21
    p22
    p23
    p24
    p25
    p26
    p27
    p28
    p29
    p30
    p31
    p32
    p33
    p34
    p35
    p36
    p37
    p38
    p39
    p40
    p41
    p42
    p43
    p44
    p45
    p46
    p47
    p48
    p49
    p50
    p51
    p52
    p53
    p54
    p55
    p56
    p57
    p58
    p59
    p60
    p61
    p62
    p63
    p64
    p65
    p66
    p67
    p68
    p69
    p70
    p71
    p72
    p73
    p74
    p75
    p76
    p77
    p78
    p79
    p80
    p81
    p82
    p83
    p84
    p85
    p86
    p87
    p88
    p89
    p90
    p91
    p92
    p93
    p94
    p95
    p96
    p97
    p98
    p99
    p100
    


```python
df_out
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
      <th>name</th>
      <th>review</th>
      <th>rating</th>
      <th>helpful</th>
      <th>unhelpful</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td></td>
      <td>The television is great, its giving me everyth...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>1</th>
      <td></td>
      <td>well this TV for my office is so great TV I lo...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>2</th>
      <td></td>
      <td>Very sharp TV with speedy interface and stream...</td>
      <td>[5]</td>
      <td>[5]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>3</th>
      <td></td>
      <td>Great picture, excellent price, very user frie...</td>
      <td>[5]</td>
      <td>[1]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>4</th>
      <td></td>
      <td>I purchased this TV UN65TUu7000FXZA a week ago...</td>
      <td>[5]</td>
      <td>[145]</td>
      <td>[68]</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1995</th>
      <td></td>
      <td>Best tv I’ve bought. The picture is perfect, b...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>1996</th>
      <td></td>
      <td>When it fits, it fits. Great visual and audio ...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>1997</th>
      <td></td>
      <td>So quick and easy.. very helpful staff and sup...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>1998</th>
      <td></td>
      <td>Perfect size for our living room! It is easy t...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
    <tr>
      <th>1999</th>
      <td></td>
      <td>This tv is amazing does all the stuff a need p...</td>
      <td>[5]</td>
      <td>[0]</td>
      <td>[0]</td>
    </tr>
  </tbody>
</table>
<p>2000 rows × 5 columns</p>
</div>


