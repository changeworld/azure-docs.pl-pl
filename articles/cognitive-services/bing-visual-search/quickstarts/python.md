---
title: 'Szybki start: tworzenie wizualnego zapytania wyszukiwania, Python — wyszukiwanie wizualne Bing'
titleSuffix: Azure Cognitive Services
description: Pokazuje, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i uzyskać szczegółowe informacje o obrazie.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 16d3d0ddf77e37e32cc50961a3870b820ac2748e
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884245"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>Szybki start: Pierwsze zapytanie wyszukiwania wizualnego Bing w języku Python

Interfejs API wyszukiwania wizualnego Bing zwraca informacje o udostępnionym obrazie. Obraz można udostępnić przy użyciu adresu URL obrazu, tokenu szczegółowych informacji lub przez przekazanie obrazu. Aby uzyskać informacje o tych opcjach, zobacz [Czym jest interfejs API wyszukiwania wizualnego Bing?](../overview.md) W tym artykule opisano przekazywanie obrazu. Przekazywanie obrazu może być przydatne w scenariuszach mobilnych, w których po zrobieniu zdjęcia znanego charakterystycznego elementu krajobrazu zwracane są informacje na jego temat. Szczegółowe informacje mogą na przykład zawierać ciekawostki na temat charakterystycznego elementu krajobrazu. 

W przypadku przekazywania lokalnego obrazu w treści żądania POST konieczne jest podanie pokazanych poniżej danych formularza. Dane formularza muszą zawierać nagłówek Content-Disposition. Jego parametr `name` musi mieć wartość "image", a parametr `filename` może być ustawiony na dowolny ciąg. Zawartością formularza jest plik binarny obrazu. Maksymalny rozmiar obrazu, który można przekazać, wynosi 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

W tym artykule zawarto prostą aplikację konsolową, która wysyła żądanie interfejsu API wyszukiwania wizualnego Bing i wyświetla wyniki wyszukiwania w formacie JSON. Ta aplikacja została napisana w języku Python, a interfejs API jest usługą internetową RESTful zgodną z każdym językiem programowania, który może wysyłać żądania HTTP i analizować dane JSON. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić ten kod, potrzebne jest środowisko języka [Python 3](https://www.python.org/).

Na potrzeby tego przewodnika Szybki start możesz użyć klucza subskrypcji [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) lub klucza subskrypcji płatnej.

## <a name="running-the-walkthrough"></a>Uruchamianie przewodnika

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Utwórz nowy projekt języka Python przy użyciu ulubionego środowiska IDE lub edytora.
2. Utwórz plik o nazwie visualsearch.py i dodaj kod przedstawiony w tym przewodniku Szybki start.
3. Zastąp wartość elementu `SUBSCRIPTION_KEY` kluczem subskrypcji.
3. Zastąp wartość elementu `imagePath` ścieżką obrazu do przekazania.
4. Uruchom program.



Poniżej przedstawiono sposób wysyłania komunikatu przy użyciu wieloczęściowych danych formularza w języku Python.

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


## <a name="next-steps"></a>Następne kroki

[Uzyskiwanie szczegółowych informacji o obrazie przy użyciu tokenu szczegółowych informacji](../use-insights-token.md)  
[Samouczek dotyczący przekazywania obrazów na potrzeby wyszukiwania wizualnego Bing](../tutorial-visual-search-image-upload.md)
[Samouczek dotyczący aplikacji jednostronicowej wyszukiwania wizualnego Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Omówienie wyszukiwania wizualnego Bing](../overview.md)  
[Wypróbuj!](https://aka.ms/bingvisualsearchtryforfree)  
[Pobierz klucz dostępu do bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Dokumentacja interfejsu API wyszukiwania wizualnego Bing](https://aka.ms/bingvisualsearchreferencedoc)
