---
title: 'Szybki Start: Użyj wyszukiwania w Internecie Bing zestawu SDK dla języka Python'
description: Ustawienia dla aplikacji konsoli zestawu SDK sieci Web wyszukiwania.
titleSuffix: Azure Cognitive Services Web search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 08/16/2018
ms.author: v-gedod, erhopf
ms.openlocfilehash: faf43d84724cdbf799219c120f87dfc333c5026f
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888530"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-python"></a>Szybki Start: Użyj wyszukiwania w Internecie Bing zestawu SDK dla języka Python

Zestaw SDK wyszukiwania w sieci Web Bing zawiera funkcje interfejsu API REST dla sieci web kwerend i wyniki analizy.

[Źródła kodu dla zestawu SDK języka Python usługi Bing Web Wyszukiwanie przykładów](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/web_search_samples.py) jest dostępna w witrynie GitHub.

## <a name="application-dependencies"></a>Zależności aplikacji
Jeśli jeszcze nie masz, zainstaluj język Python. Zestaw SDK jest zgodny z języka Python 2.7 3.3, 3.4, 3.5 i 3.6.

Ogólne zalecenia dotyczące programowania w języku Python jest użycie [środowiska wirtualnego](https://docs.python.org/3/tutorial/venv.html).
Zainstaluj i zainicjuj środowisko wirtualne przy użyciu [modułu venv](https://pypi.python.org/pypi/virtualenv). Należy zainstalować virtualenv for Python 2.7.
```
python -m venv mytestenv
```
Instalowanie składników zależnych zestawu SDK wyszukiwania w sieci Web Bing:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```
## <a name="web-search-client"></a>Klient wyszukiwania w sieci Web
Pobierz [klucz subskrypcji usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) w obszarze *wyszukiwania*.
Dodaj import, a następnie utwórz wystąpienie obiektu `CognitiveServicesCredentials`:
```
from azure.cognitiveservices.search.websearch import WebSearchAPI
from azure.cognitiveservices.search.websearch.models import SafeSearch
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Następnie utwórz wystąpienie klienta:
```
client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Wyniki wyszukiwania i drukowanie pierwszego wyniku strony sieci Web:
```
web_data = client.web.search(query="Yosemite")
print("\r\nSearched for Query# \" Yosemite \"")

# WebPages
if web_data.web_pages.value:

    print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

    first_web_page = web_data.web_pages.value[0]
    print("First web page name: {} ".format(first_web_page.name))
    print("First web page URL: {} ".format(first_web_page.url))

else:
    print("Didn't see any Web data..")
```
Drukuj inne typy wyników, w tym obrazy, wiadomości i wideo:
```
# Images
if web_data.images.value:

    print("\r\nImage Results#{}".format(len(web_data.images.value)))

    first_image = web_data.images.value[0]
    print("First Image name: {} ".format(first_image.name))
    print("First Image URL: {} ".format(first_image.url))

else:
    print("Didn't see any Image..")

# News
if web_data.news.value:

    print("\r\nNews Results#{}".format(len(web_data.news.value)))

    first_news = web_data.news.value[0]
    print("First News name: {} ".format(first_news.name))
    print("First News URL: {} ".format(first_news.url))

else:
    print("Didn't see any News..")

# Videos
if web_data.videos.value:

    print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

    first_video = web_data.videos.value[0]
    print("First Videos name: {} ".format(first_video.name))
    print("First Videos URL: {} ".format(first_video.url))

else:
    print("Didn't see any Videos..")

```
Wyszukaj (najlepszą restauracje w Seattle), sprawdź liczbę wyników i wydrukuj `name` i `URL` pierwszego wyniku.
```
def web_results_with_count_and_offset(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
        print("\r\nSearched for Query# \" Best restaurants in Seattle \"")

        if web_data.web_pages.value:

            print("Webpage Results#{}".format(len(web_data.web_pages.value)))

            first_web_page = web_data.web_pages.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))```

```
Wyszukaj "xbox" za pomocą `response_filter` przypisane do `News`.  Drukowanie szczegółów wyniki wiadomości.
```
def web_search_with_response_filter(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(query="xbox", response_filter=["News"])
        print("\r\nSearched for Query# \" xbox \" with response filters \"News\"")

        # News attribute since I filtered "News"
        if web_data.news.value:

            print("Webpage Results#{}".format(len(web_data.news.value)))

            first_web_page = web_data.news.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Wyszukaj przy użyciu zapytania "Niagara mieści się", przy użyciu `answerCount` i `promote` parametrów. Drukowanie szczegółów wyników.
```
def web_search_with_answer_count_promote_and_safe_search(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(
            query="Niagara Falls",
            answer_count=2,
            promote=["videos"],
            safe_search=SafeSearch.strict  # or directly "Strict"
        )
        print("\r\nSearched for Query# \" Niagara Falls\"")

        if web_data.web_pages.value:

            print("Webpage Results#{}".format(len(web_data.web_pages.value)))

            first_web_page = web_data.web_pages.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
## <a name="next-steps"></a>Kolejne kroki

[Cognitive przykłady zestawu SDK języka Python usługi](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)
