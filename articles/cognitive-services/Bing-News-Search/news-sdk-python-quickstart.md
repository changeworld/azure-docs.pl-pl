---
title: Szybki Start wyszukiwania zestawu SDK Python wiadomości | Dokumentacja firmy Microsoft
description: Instalator dla aplikacji konsoli SDK wyszukiwania wiadomości.
titleSuffix: Azure News Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 6d212d1477ecf583a038e33e72aab3d60f6aa050
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349465"
---
# <a name="news-search-sdk-python-quickstart"></a>Szybki Start wyszukiwania zestawu SDK Python wiadomości

Zestaw SDK wyszukiwania wiadomości zawiera funkcje interfejsu API REST dla zapytań sieci web oraz wyniki analizy. 

[Źródła kodu dla przykładów zestaw SDK Python usługi Bing wiadomości wyszukiwania](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) jest dostępna w Centrum Git.

## <a name="application-dependencies"></a>Zależności aplikacji
Jeśli nie masz jeszcze go, należy zainstalować Python. Zestaw SDK jest zgodny z języka Python 2.7, 3.3 3.4, 3.5 i 3,6.

Ogólne zalecenia dotyczące programowania Python jest użycie [środowiska wirtualnego](https://docs.python.org/3/tutorial/venv.html). Zainstaluj i zainicjować środowiska wirtualnego z [venv modułu](https://pypi.python.org/pypi/virtualenv). Musisz zainstalować virtualenv dla języka Python 2.7.
```
python -m venv mytestenv
```
Zainstaluj zestaw SDK wyszukiwania wiadomości Bing zależności:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-newssearch
```
## <a name="news-search-client"></a>Klient wyszukiwania wiadomości
Pobierz [klucz dostępu usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) w obszarze *wyszukiwania*. Dodaj importów:
```
from azure.cognitiveservices.search.newssearch import NewsSearchAPI
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Utwórz wystąpienie `CognitiveServicesCredentials`. Utwórz wystąpienie klienta:
```
client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Wyniki wyszukiwania i drukowanie pierwszego wyniku strony sieci Web:
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
Wyszukiwanie z filtrów, aby uzyskać najnowsze wiadomości o "Sztucznego analizy" z `freshness` i `sortBy` parametrów. Sprawdź liczbę wyników, a następnie wydrukuj `totalEstimatedMatches`, `name`, `url`, `description`, `published time`, i `name of provider` pierwszego wyniku elementu wiadomości.
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
Wyszukiwanie kategorii wiadomości dla filmu i rozrywka TV z bezpiecznym wyszukiwania. Sprawdź liczbę wyników, a następnie wydrukuj `category`, `name`, `url`, `description`, `published time`, i `name of provider` pierwszego wyniku elementu wiadomości.
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
Wyszukiwanie tematów trendów wiadomości Bing.  Sprawdź liczbę wyników, a następnie wydrukuj `name`, `text of query`, `webSearchUrl`, `newsSearchUrl`, i `image Url` pierwszego wyniku wiadomości.
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

## <a name="next-steps"></a>Kolejne kroki

[Zestaw SDK Python usługi kognitywnych — przykłady](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


