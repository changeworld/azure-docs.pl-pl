---
title: 'Szybki start: przeprowadzanie wyszukiwania za pomocą języka Python — interfejs API wyszukiwania w sieci Web Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby wysyłać żądania do interfejsu API wyszukiwania w sieci Web Bing przy użyciu języka Python i otrzymywać odpowiedzi w formacie JSON.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 8/16/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 4aab5c51d36b3db01175216be9f4e62a897d6132
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178226"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Szybki start: wywoływanie interfejsu API wyszukiwania w sieci Web Bing za pomocą języka Python  
Pobierz [klucz dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) w obszarze **Wyszukiwanie**.  Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Ten przewodnik Szybki start umożliwi Ci utworzenie Twojego pierwszego wywołania interfejsu API wyszukiwania w sieci Web Bing i odebranie odpowiedzi w formacie JSON w mniej niż 10 minut.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Ten przykład działa jako notes Jupyter w usłudze [MyBinder](https://mybinder.org). Kliknij etykietę uruchamiania usługi Binder:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="define-variables"></a>Definiowanie zmiennych

Zamień wartość `subscription_key` na odpowiedni klucz subskrypcji ze swojego konta platformy Azure.

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Zadeklaruj punkt końcowy interfejsu API wyszukiwania w sieci Web Bing. Jeśli wystąpią błędy autoryzacji, dokładnie sprawdź tę wartość, porównując ją z punktem końcowym wyszukiwania w usłudze Bing na pulpicie nawigacyjnym platformy Azure.

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Możesz dostosować zapytanie wyszukiwania, zamieniając wartość `search_term`.

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>Wysyłanie żądania

W tym bloku za pomocą biblioteki `requests` wywoływany jest interfejs API wyszukiwania w sieci Web Bing i zwracane są wyniki w postaci obiektu JSON. Klucz interfejsu API jest przekazywany do słownika `headers`, a wyszukiwany termin i parametry zapytania są przekazywane do słownika `params`. Aby uzyskać pełną listę opcji i parametrów, zobacz dokumentację [interfejsu API wyszukiwania w sieci Web Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).

```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Formatowanie i wyświetlanie odpowiedzi

Obiekt `search_results` zawiera wyniki wyszukiwania i metadane, takie jak powiązane zapytania i strony. Ten kod formatuje i wyświetla odpowiedź w przeglądarce za pomocą biblioteki `IPython.display`.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Kod przykładowy w witrynie GitHub

Jeśli chcesz uruchomić ten kod lokalnie, pełny [przykład jest dostępny w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingWebSearchv7.js).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący jednostronicowej aplikacji wyszukiwania w sieci Web Bing](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
