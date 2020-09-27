---
title: 알라딘에서 데이터 가져오는 간단한 python 코드
date: 2020-09-26 23:00:00+0900
categories: Python
tags: [Python, BeautifulSoup, Json]
---
이게 1단계. 다음으로는 chrome extension 을 만들어야 한다.
{% highlight python %}
import requests
from bs4 import BeautifulSoup
import json
 
def find_meta_data(soup, property_id, property_name):
    meta_data = soup.find("meta", {property_id:property_name})
    return meta_data["content"] if meta_data else ""
  
def main() :
    item_id = input("알라딘 ItemId 숫자를 입력해 주세요\n")
    # item_id = "247808074"
    url = "https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=" + item_id
    response = requests.get(url)
    soup = BeautifulSoup(response.content, "html.parser")
 
    book_title = find_meta_data(soup, "property", "og:title")
    book_price = find_meta_data(soup, "property", "og:price")
    book_isbn13 = find_meta_data(soup, "property", "og:barcode")
    book_date = find_meta_data(soup, "itemprop", "datePublished")
 
    output_str = "제목 : " + book_title + "\n"
    output_str += "발행일자 : " + book_date + "\n"
 
    ld_json = soup.select_one("script", type="application/ld+json").string
    js = json.loads(ld_json)
    output_str += "발행처 : " + js["publisher"]["name"] + "\n"
     
    output_str += "ISBN : " + book_isbn13 + "\n"
    output_str += "정가 : " + book_price + "\n"
 
    print(output_str)
 
    with open(item_id + ".txt", "w") as f:
        f.write(output_str)
 
if __name__ == "__main__" :
    main()
{% endhighlight %}