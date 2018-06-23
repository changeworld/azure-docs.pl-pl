---
title: Wywołanie i odpowiedź — Szybki Start języka Python dla usług Azure kognitywnych, interfejsu API Bing sieci Web wyszukiwania | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu interfejsu API wyszukiwania usługi Bing sieci Web w kognitywnych usług Microsoft Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 9/18/2017
ms.author: v-jerkin
ms.openlocfilehash: 8d4df9db60c7a74a5b9e53d4622528c0054b4f19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347576"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-python"></a>Wywołania i odpowiedzi: pierwszego zapytania wyszukiwania usługi Bing w sieci Web w języku Python

Interfejs API wyszukiwania usługi Bing sieci Web udostępnia środowisko podobne do Bing.com/Search zwracając się, że wyniki wyszukiwania, które określa Bing są istotne dla kwerendy użytkownika. Wyniki mogą obejmować stron sieci Web, obrazów, klipów wideo, wiadomości i jednostek, wraz z zapytania wyszukiwania powiązanych, pisowni stref czasowych, konwersja jednostek, tłumaczenia i obliczeń. Rodzaje otrzymane wyniki na podstawie ich przydatności i warstwy subskrybowanych interfejsy API wyszukiwania usługi Bing.

Zapoznaj się [dokumentacja interfejsu API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) uzyskać szczegółowe informacje techniczne dotyczące interfejsów API.

W tym przykładzie można uruchomić jako notesu Jupyter na [MyBinder](https://mybinder.org) , klikając polecenie Uruchom integratora znaczków: 

[![Obiekt wiążący](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)


## <a name="prerequisites"></a>Wymagania wstępne
Musi mieć [kognitywnych interfejsu API usług konta](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsy API wyszukiwania usługi Bing**. [Bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) jest wystarczająca dla tego przewodnika Szybki Start. Należy klucz dostępu podany przy wywołaniu metody aktywacji bezpłatną wersję próbną lub może używać klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure.

## <a name="running-the-walkthrough"></a>Uruchomiona przewodnika

Ustaw `subscription_key` do klucza interfejsu API dla usługi interfejsu API Bing.


```python
subscription_key = None
assert subscription_key
```

Następnie sprawdź, czy `search_url` punktu końcowego jest poprawna. W tym tylko jeden punkt końcowy jest używany do wyszukiwania usługi Bing interfejsów API. Jeśli wystąpią błędy autoryzacji dokładnie, czy ta wartość względem punktu końcowego wyszukiwania usługi Bing w pulpicie nawigacyjnym platformy Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Ustaw `search_term` zapytania Bing dla kognitywnych usług firmy Microsoft.


```python
search_term = "Microsoft Cognitive Services"
```

Z poniższego bloku używa `requests` biblioteki w języku Python do wyróżnienia do wyszukiwania usługi Bing interfejsów API i zwrócenie wyników jako obiekt JSON. Sprawdź, czy jest przekazywana klucza interfejsu API za pomocą `headers` słownika i wyszukiwanie termin za pośrednictwem `params` słownika. Aby zapoznać się z pełną listą opcje, które mogą służyć do filtrowania wyników wyszukiwania, zapoznaj się [interfejsu API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) dokumentacji.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` Obiekt zawiera wyniki wyszukiwania, oraz rozbudowane metadane, takie jak powiązane zapytania i strony. Następujące wiersze kodu formatu najpopularniejsze strony zwróconych przez kwerendę.


```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek aplikacji jednej strony wyszukiwania usługi Bing w sieci Web](../tutorial-bing-web-search-single-page-app.md)

## <a name="see-also"></a>Zobacz także 

[Wyszukiwania usługi Bing w sieci Web — Omówienie](../overview.md)  
[Wypróbuj](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[Pobierz klucz bezpłatnej wersji próbnej dostępu](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)
[dokumentacja interfejsu API wyszukiwania sieci Web usługi Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
