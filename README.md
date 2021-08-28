
Amazon Product review sentiment using bert  

# Table of Content
1. [Project Overview](#project)
2. [Dataset Overview](#dataset)
3. [Steps](#steps)
4. [Model Choose](#model)

<a name="project"></a>
## Project Overview

I am going to implement bert-base-multilingual-uncased-sentiment for sentiment analysis <br><br>
This a bert-base-multilingual-uncased model finetuned for sentiment analysis on product reviews in six languages: English, Dutch, German, French, Spanish and Italian. It predicts the sentiment of the review as a number of stars (between 1 and 5).<br><br>
This model is intended for direct use as a sentiment analysis model for product reviews in any of the six languages above, or for further finetuning on related sentiment analysis tasks.<br><br>

<a name="dataset"></a>
## Dataset overview

Here we will use reviews of amazon products.<br><br>
 
Reviews are scarpped with the help of Beautifulsoup and splash libraries will help for scrapping reviews from the webpage.<br><br>

Install docker and splash image files on your system.
Run Splash in Docker Desktop after then run the following script.


<a name="steps"></a>
## Steps  
1. Web Scrapping

install docker and splash image files on your system. <a href='https://youtu.be/8q2K41QC2nQ'>set up splash Link</a> <br><br>
Run Splash in Docker Desktop after then run the following script.   <br><br>

```
import requests
from bs4 import BeautifulSoup
import pandas as pd
reviewlist = []
def get_soup(url):
    r = requests.get('http://localhost:8050/render.html', params={'url': url, 'wait': 2})
    soup = BeautifulSoup(r.text, 'html.parser')
    return soup
def get_reviews(soup):
    reviews = soup.find_all('div', {'data-hook': 'review'})
    try:
        for item in reviews:
            review = {
            'product': soup.title.text.replace('Amazon.in:Customer reviews:', '').strip(), #'product': soup.title.text.replace('Amazon.in:Customer reviews:', '').strip(),
            'title': item.find('a', {'data-hook': 'review-title'}).text.strip(),
            'rating':  float(item.find('i', {'data-hook': 'review-star-rating'}).text.replace('out of 5 stars', '').strip()),
            'body': item.find('span', {'data-hook': 'review-body'}).text.strip(),
            }
            reviewlist.append(review)
    except:
        pass
for x in range(1,999):
    soup = get_soup(f'https://www.amazon.in/Samsung-Galaxy-Storage-Additional-Exchange/product-reviews/B086KFBNV5/ref=cm_cr_getr_d_paging_btm_prev_2?ie=UTF8&reviewerType=all_reviews&pageNumber={x}')
    print(f'Getting page: {x}')
    get_reviews(soup)
    print(len(reviewlist))
    if not soup.find('li', {'class': 'a-disabled a-last'}):
        pass
    else:
        break
df = pd.DataFrame(reviewlist)
df.to_excel('Samsung_Galaxy_Z.xlsx', index=False)
print('Fin.')
```

2. Implement Bert 

for installing
```
!pip install torch==1.8.1+cu111 torchvision==0.9.1+cu111 torchaudio===0.8.1 -f https://download.pytorch.org/whl/torch_stable.html
!pip install transformers requests beautifulsoup4 pandas numpy
```
Import Libraries <br>
```
from transformers import AutoTokenizer, AutoModelForSequenceClassification
import torch
import requests
from bs4 import BeautifulSoup
import re
```

Instantiate Model <br>
```
tokenizer = AutoTokenizer.from_pretrained('nlptown/bert-base-multilingual-uncased-sentiment')
model = AutoModelForSequenceClassification.from_pretrained('nlptown/bert-base-multilingual-uncased-sentiment')
Load Reviews into DataFrame and Score
def sentiment_score(review):
    tokens = tokenizer.encode(review, return_tensors='pt')
    result = model(tokens)
    return int(torch.argmax(result.logits))+1
    
```    

above code return number within range 1–5 <br><br>
```
df['sentiment'] = df['body'].apply(lambda x: sentiment_score(x[:46]))
```
<a href='https://github.com/pratikp12/Bert-Folder/blob/main/bertsentiment.ipynb'>Notebook link</a>

OutPut<br>

<img src='https://miro.medium.com/max/645/1*9__jkcUcucDMaYpWLSeMSw.png'>

<a name="model"></a>
## Model Choose

