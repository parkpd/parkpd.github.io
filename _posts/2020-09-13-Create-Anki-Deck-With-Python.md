---
title: 파이썬으로 Anki Deck 생성하기
date: 2020-09-13 08:40:18+0900
categories: Python
tags: [Python, Anki]
---
[WordWeb](https://wordweb.info/) 은 검색기록을 %appdata%/WordWeb 에 .txt 형태로 저장한다. 그렇다면 내가 지금까지 찾은 영단어를 [Anki](https://apps.ankiweb.net/) 덱으로 만들 방법이 있지 않을까?  
- 먼저 영단어를 txt 포멧으로 만든다.
- lingoes-extractor 나 크롤링을 이용해 단어의 뜻을 구해서 '영단어\t의미' 형태로 csv 파일을 만든다.
  - 예: [Naver Endic Crawler](https://github.com/hoonhoons/naver_endic_crawler)
- [genanki](https://github.com/kerrickstaley/genanki) 를 활용해 anki 덱 파일을 만든 뒤 import 한다.
  - 아래 코드는 <https://charly-lersteau.com/posts/2019-11-17-create-anki-deck-csv/> 를 일부 변형했다.  

{% highlight python %}
#!/usr/bin/env python3

import csv
import random
import genanki

data_filename = "Test.txt"
deck_filename = "Test.apkg"
anki_deck_title = "Test"
anki_model_name = "Test"

# Create the deck model
model_id = random.randrange(1 << 30, 1 << 31)

#https://apps.ankiweb.net/docs/manual20.html#card-styling
style = """
.card {
 font-family: arial;
 font-size: 30px;
 text-align: center;
 color: black;
 background-color: white;
}
.quiz {
 font-size: 30px;
}
.answer {
 font-size: 20px;
}
"""
# https://apps.ankiweb.net/docs/manual20.html#special-fields
anki_model = genanki.Model(
    model_id,
    anki_model_name,
    fields=[
        {'name': 'Question'},
        {'name': 'Answer'},
    ],
    templates=[
        {
            'name': 'Card 1',
            "qfmt": '<p class="quiz">{{Question}}</p>',
            'afmt': '{{FrontSide}}<hr id="answer"><p class="answer">{{Answer}}</p>',
        },      
    ],
    css=style
)

# The list of flashcards
anki_notes = []

with open(data_filename, "r", encoding='UTF8') as csv_file:
    csv_reader = csv.reader(csv_file, delimiter="\t")

    for row in csv_reader:
        anki_note = genanki.Note(
            model=anki_model,
            fields=[row[0], row[1]],
        )
        anki_notes.append(anki_note)

# Shuffle flashcards
random.shuffle(anki_notes)

anki_deck = genanki.Deck(model_id, anki_deck_title)
anki_package = genanki.Package(anki_deck)

# Add flashcards to the deck
for anki_note in anki_notes:
    anki_deck.add_note(anki_note)

# Save the deck to a file
anki_package.write_to_file(deck_filename)

print("Created deck with {} flashcards".format(len(anki_deck.notes)))
{% endhighlight %}