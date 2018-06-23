---
title: Szybki Start języka Python dla programu Visual wyszukiwania usługi Bing interfejsu API | Dokumentacja firmy Microsoft
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Pokazuje, jak przekazać obraz Visual API wyszukiwania usługi Bing i wrócić szczegółowych informacji o obrazie.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: a520466825eb429e45e0500b52bd7af502c0a38c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349333"
---
# <a name="your-first-bing-visual-search-query-in-python"></a>W języku Python pierwszego zapytania Visual wyszukiwania usługi Bing

Visual API wyszukiwania usługi Bing zwraca informacje dotyczące obrazu, który podasz. Musisz podać obrazu przy użyciu adresu URL obrazu, insights token, lub przekazując obrazu. Aby uzyskać informacje o tych opcjach, zobacz [co to jest Visual API wyszukiwania usługi Bing?](../overview.md) W tym artykule przedstawiono przekazywanie obrazu. Przekazywanie obrazu może być przydatne w scenariuszach mobilnych, gdzie obrazu dzielnicę dobrze znanych i odzyskać informacje o nim. Na przykład szczegółowych danych może zawierać elementy towarzyszące składni o punkty. 

Jeśli Przekaż obraz lokalnych, poniżej przedstawiono dane formularza należy uwzględnić w treści POST. Dane formularza musi zawierać nagłówek Content-Disposition. Jego `name` parametr musi być ustawiony na "obrazu" i `filename` parametr może być ustawiony na dowolny ciąg. Zawartość formularza jest plikiem binarnym obrazu. Rozmiar maksymalny obrazu, które mogą przekazać to 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Ten artykuł zawiera prostej aplikacji konsolowej wysyła żądanie Visual API wyszukiwania usługi Bing i wyświetla wyniki wyszukiwania JSON. Ta aplikacja jest napisany w języku Python, interfejsu API jest zgodny z żadnego języka programowania, które mogą wysyłać żądania HTTP i przeanalizować składni JSON usługi sieci RESTful Web. 

## <a name="prerequisites"></a>Wymagania wstępne

Należy [Python 3](https://www.python.org/) do uruchomienia tego kodu.

Dla tego przewodnika Szybki Start, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klawisz płatną subskrypcję lub subskrypcji.

## <a name="running-the-walkthrough"></a>Uruchomiona przewodnika

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Utwórz nowy projekt języka Python w ulubionych IDE lub edytora.
2. Utwórz plik o nazwie visualsearch.py i Dodaj kodem przedstawionym w tego przewodnika Szybki Start.
3. Zastąp `SUBSCRIPTION_KEY` wartości z klucza subskrypcji.
3. Zastąp `imagePath` wartości o ścieżce obrazu do przekazania.
4. Uruchom program.



Poniżej przedstawiono sposób wysyłania wiadomości w języku Python za pomocą wieloczęściowych danych formularza.

```python
"""Bing Visual Search upload image example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json


BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

imagePath = '<pathtoyourimagetouploadgoeshere>'

file = {'image' : ('myfile', open(imagePath, 'rb'))}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```


## <a name="next-steps"></a>Kolejne kroki

[Uzyskiwanie szczegółowych informacji o obrazie przy użyciu tokenu insights](../use-insights-token.md)  
[Samouczek aplikacji jednej strony Visual wyszukiwania usługi Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Omówienie Visual wyszukiwania usługi Bing](../overview.md)  
[Wypróbuj](https://aka.ms/bingvisualsearchtryforfree)  
[Pobierz klucz bezpłatnej wersji próbnej dostępu](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Odwołanie do Visual API wyszukiwania usługi Bing](https://aka.ms/bingvisualsearchreferencedoc)
