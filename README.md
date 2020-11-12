#Scraper
This is a web scraper designed to scrape reviews about vehicle insurance providers in Australia

#Loading libraries
from bs4 import BeautifulSoup
import numpy as np
import pandas as pd
import requests
from time import sleep
from random import randint

#Create objects for url to scrape from multiple pages
pages = np.arange(1,10, 1)
print(pages)
providers = ['youi-car-insurance','budget-direct-car-insurance'] #Based on popularity. More reviews, more data.

#Scraper Design
data=[]
for value in providers:#Looping through listings by providers (Outer)
 for page in pages:#Looping through pages within listings (Inner)
    
    url="https://www.productreview.com.au/listings/"+value+ "?page=" + str(page) + "#reviews" #Based on url logic
    page = requests.get(url)
    print(page)
    sleep(randint(2,10)) #To tackle response 429: too many requests
    soup = BeautifulSoup(page.text, 'html.parser')
    total_reviews = soup.findAll("div", {'itemprop':'review'})
    for consolidated in total_reviews:#Loop to append pagewise reviews
        data.append(consolidated)
    
len(total_reviews)
len(data)

#Forming dataframe outline for extracted features
reviews = []

for reviewBox in data:

    #Review title
    review_title = reviewBox.find('h3', {"class": "mb-2_3ol"}).text
    
    #Review text
    review_text = reviewBox.find('p', {"class": "mb-0_2CX"}).text
    
    #Review date
    review_date = reviewBox.find('meta', {'itemprop': 'datePublished'})['content']

    #Review star rating
    review_stars = reviewBox.find('meta', {'itemprop': 'ratingValue'})['content']
    
    # append the process review and the title to the reviews list
    reviews.append([review_title, review_text, review_date, review_stars])

#Exporting scrape results
output_column_names = ['title', 'review', 'date', 'stars']
df1 = pd.DataFrame(reviews, columns=output_column_names)
df1.to_csv('Reviews_Scrape.csv', index=None)




