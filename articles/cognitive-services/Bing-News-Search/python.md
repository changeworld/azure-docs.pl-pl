---
title: 'Szybki start: interfejs API wyszukiwania wiadomości Bing, Python'
titlesuffix: Azure Cognitive Services
description: Uzyskaj informacje oraz przykłady kodu w celu szybkiego rozpoczęcia korzystania z interfejsu API wyszukiwania wiadomości Bing.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 583b304a742d9abfd799442c9aa2999ad6783a34
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803549"
---
# <a name="quickstart-for-bing-news-search-api-with-python"></a>Szybki start: interfejs API wyszukiwania wiadomości Bing w środowisku języka Python
W tym przewodniku przedstawiono prosty przykład wywoływania interfejsu API wyszukiwania wiadomości Bing i dalszego przetwarzania wynikowego obiektu JSON. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API wyszukiwania wiadomości Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).  

Ten przykład można uruchomić jako notes programu Jupyter w witrynie [MyBinder](https://mybinder.org). W tym celu należy kliknąć ikonę Launch Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

## <a name="prerequisites"></a>Wymagania wstępne

Trzeba mieć [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z dostępem do **interfejsów API wyszukiwania Bing**. [Bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) jest wystarczająca na potrzeby tego przewodnika Szybki start. Potrzebny jest klucz dostępu podany przy aktywacji bezpłatnej wersji próbnej lub klucz płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure.

## <a name="running-the-walkthrough"></a>Uruchamianie przewodnika
Najpierw nadaj zmiennej `subscription_key` wartość klucza interfejsu API dla usługi interfejsu API Bing.


```python
subscription_key = None
assert subscription_key
```

Następnie upewnij się, że wartość `search_url` punktu końcowego jest poprawna. Obecnie tylko jeden punkt końcowy jest używany dla interfejsów API wyszukiwania Bing. Jeśli wystąpią błędy autoryzacji, dokładnie sprawdź tę wartość, porównując ją z punktem końcowym wyszukiwania w usłudze Bing na pulpicie nawigacyjnym platformy Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

Ustaw parametr `search_term` w celu wyszukiwania artykułów z wiadomościami na temat firmy Microsoft.


```python
search_term = "Microsoft"
```

Następujący blok wykorzystuje bibliotekę `requests` w języku Python do wywoływania interfejsów API wyszukiwania Bing i zwracania wyników w postaci obiektu JSON. Zobacz, jak przekazujemy klucz interfejsu API za pośrednictwem słownika `headers` i wyszukiwany termin za pośrednictwem słownika `params`. Aby wyświetlić pełną listę opcji, które mogą służyć do odfiltrowania wyników wyszukiwania, zobacz dokumentację [interfejsu API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

Obiekt `search_results` zawiera odpowiednie artykuły z wiadomościami oraz obszerne metadane. Na przykład następujący wiersz kodu wyodrębnia opisy artykułów.


```python
descriptions = [article["description"] for article in search_results["value"]]
```

Następnie opisy te można przedstawić w formie tabeli, w której słowa kluczowe wyszukiwania są wyróżnione za pomocą **pogrubienia**.


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Stronicowanie wiadomości](paging-news.md)
> [Wyróżnianie tekstu przy użyciu znaczników dekoracji](hit-highlighting.md)

## <a name="see-also"></a>Zobacz też 

 [Searching the web for news (Wyszukiwanie wiadomości w Internecie)](search-the-web.md)  
 [Wypróbuj!](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
