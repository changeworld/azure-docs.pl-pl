---
title: 'Szybki start: Wyszukiwanie wiadomości — zestaw SDK wyszukiwania wiadomości Bing dla języka Python'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby wyszukać wiadomości przy użyciu zestawu SDK wyszukiwania wiadomości Bing dla języka Python i przetworzyć odpowiedź.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/14/2018
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 3489a9634cecd776afc8619a81acd72a2649ec36
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53261201"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Szybki start: Wyszukiwanie wiadomości przy użyciu zestawu SDK wyszukiwania wiadomości Bing dla języka Python

Zestaw SDK wyszukiwania wiadomości używa funkcji interfejsu API REST do wykonywania zapytań internetowych i analizowania wyników. 

[Kod źródłowy przykładów zestawu SDK wyszukiwania wiadomości Bing dla języka Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) jest dostępny w usłudze Git Hub.

## <a name="application-dependencies"></a>Zależności aplikacji
Pobierz [klucz dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) w obszarze **Wyszukiwanie**.  Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Jeśli nie masz języka Python, zainstaluj go. Zestaw SDK jest zgodny z językiem Python w wersjach 2.7, 3.3, 3.4, 3.5 i 3.6.

W przypadku programowania w języku Python zalecane jest użycie [środowiska wirtualnego](https://docs.python.org/3/tutorial/venv.html). Zainstaluj i zainicjuj środowisko wirtualne przy użyciu [modułu venv](https://pypi.python.org/pypi/virtualenv). Zainstaluj moduł virtualenv dla języka Python 2.7.
```
python -m venv mytestenv
```
Zainstaluj zależności zestawu SDK wyszukiwania wiadomości Bing:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-newssearch
```
## <a name="news-search-client"></a>Klient wyszukiwania wiadomości
Pobierz [klucz dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) w obszarze *Wyszukiwanie*. Dodaj importy:
```
from azure.cognitiveservices.search.newssearch import NewsSearchAPI
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Utwórz wystąpienie elementu `CognitiveServicesCredentials`. Utwórz wystąpienie klienta:
```
client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Wyszukaj wyniki i wyświetl pierwszą wynikową stronę internetową:
```
news_result = client.news.search(query="Quantum Computing", market="en-us", count=10)
print("Search news for query \"Quantum Computing\" with market and count")

if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")

```
Wyszukaj najnowsze wiadomości dotyczące tematu „Artificial Intelligence” (sztucznej inteligencji), używając parametrów filtrowania `freshness` (aktualność) i `sortBy` (sortowanie według). Sprawdź liczbę wyników, a następnie pokaż parametry `totalEstimatedMatches` (szacowana liczba wyników), `name` (nazwa), `url` (adres URL), `description` (opis), `published time` (czas publikacji) i `name of provider` (nazwa dostawcy) pierwszego wyniku.
```
def news_search_with_filtering(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.search(
            query="Artificial Intelligence",
            market="en-us",
            freshness="Week",
            sort_by="Date"
        )
        print("\r\nSearch most recent news for query \"Artificial Intelligence\" with freshness and sortBy")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Wyszukaj wiadomości z kategorii filmów i telewizji przy użyciu bezpiecznego wyszukiwania. Sprawdź liczbę wyników, a następnie pokaż parametry `category` (kategoria), `name` (nazwa), `url` (adres URL), `description` (opis), `published time` (czas publikacji) i `name of provider` (nazwa dostawcy) pierwszego wyniku.
```
def news_category(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.category(
            category="Entertainment_MovieAndTV",
            market="en-us",
            safe_search="strict"
        )
        print("\r\nSearch category news for movie and TV entertainment with safe search")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news category: {}".format(first_news_result.category))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))


```
Wyszukaj popularne tematy wiadomości w usłudze Bing.  Sprawdź liczbę wyników, a następnie pokaż parametry `name` (nazwa), `text of query` (tekst zapytania), `webSearchUrl` (adres URL wyszukiwania w Internecie), `newsSearchUrl` (adres URL wyszukiwania wiadomości) i `image Url` (adres URL obrazu) pierwszego wyniku.
```
def news_trending(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        trending_topics = client.news.trending(market="en-us")
        print("\r\nSearch news trending topics in Bing")

        if trending_topics.value:
            first_topic = trending_topics.value[0]
            print("News result count: {}".format(len(trending_topics.value)))
            print("First topic name: {}".format(first_topic.name))
            print("First topic query: {}".format(first_topic.query.text))
            print("First topic image url: {}".format(first_topic.image.url))
            print("First topic webSearchUrl: {}".format(first_topic.web_search_url))
            print("First topic newsSearchUrl: {}".format(first_topic.news_search_url))
        else:
            print("Didn't see any topics result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```

## <a name="next-steps"></a>Następne kroki

[Przykłady dotyczące zestawu SDK dla języka Python dla usług Cognitive Services](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


