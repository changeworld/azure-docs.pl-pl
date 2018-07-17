---
title: Szybki Start języka Python API wyszukiwania wizualnego Bing | Dokumentacja firmy Microsoft
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Pokazuje, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i uzyskanie szczegółowych informacji o obrazie.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 96bd94e37c75d10726245fbcea7044d4ae2ed07e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070379"
---
# <a name="your-first-bing-visual-search-query-in-python"></a>Pierwsze zapytanie wyszukiwania wizualnego Bing w języku Python

Interfejs API wyszukiwania wizualnego Bing zwraca informacje o obrazie, który należy podać. Aby przekazać obraz, przy użyciu adresu URL obrazu usługi insights tokenu, lub przekazanie obrazu. Aby uzyskać informacje o tych opcjach, zobacz [co to jest interfejs API wyszukiwania wizualnego Bing?](../overview.md) W tym artykule przedstawiono przekazywania obrazu. Przekazywanie obrazu może być przydatne w scenariuszach mobilnych, gdzie Zrób zdjęcie dobrze znanych charakterystycznych elementów krajobrazu i uzyskanie informacji na jego temat. Na przykład szczegółowe informacje zawiera elementy towarzyszące składni o charakterystycznych elementów krajobrazu. 

Jeśli załadujesz lokalny obraz, poniżej przedstawiono dane formularza należy uwzględnić w treści wpisu. Dane mogą zawierać nagłówek Content-Disposition. Jego `name` parametru musi być równa "image" i `filename` parametru może być ustawiona na dowolny ciąg. Zawartość formularza jest plik binarny obrazu. Rozmiar maksymalny obrazu, którą możesz przekazać to 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Ten artykuł zawiera prostą aplikację konsolową, która wysyła żądanie interfejsu API wyszukiwania wizualnego Bing i wyświetla wyniki wyszukiwania JSON. Podczas tej aplikacji został napisany w języku Python, interfejs API jest zgodny z dowolnego języka programowania, który może wysyłać żądania HTTP i Przeanalizuj dane JSON usługi sieci Web typu RESTful. 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz [Python 3](https://www.python.org/) do uruchamiania tego kodu.

W tym przewodniku Szybki Start, możesz użyć [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klawisz płatna subskrypcja lub subskrypcja.

## <a name="running-the-walkthrough"></a>Uruchamianie przewodnik

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Utwórz nowy projekt języka Python w Twoim ulubionym środowiskiem IDE lub edytora.
2. Utwórz plik o nazwie visualsearch.py i Dodaj kod przedstawiony w tym przewodniku Szybki Start.
3. Zastąp `SUBSCRIPTION_KEY` wartość z kluczem subskrypcji.
3. Zastąp `imagePath` na ścieżkę obrazu do przekazania.
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

[Uzyskiwanie szczegółowych informacji o pliku obrazu przy użyciu tokenu usługi insights](../use-insights-token.md)  
[Samouczek przekazywania obrazów wyszukiwania wizualnego Bing](../tutorial-visual-search-image-upload.md)
[samouczek dotyczący aplikacji jednostronicowej wyszukiwania wizualnego Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Przegląd wyszukiwania wizualnego Bing](../overview.md)  
[Wypróbuj](https://aka.ms/bingvisualsearchtryforfree)  
[Pobierz klucz bezpłatny dostęp próbny](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Dokumentacja interfejsu API wyszukiwania wizualnego Bing](https://aka.ms/bingvisualsearchreferencedoc)
