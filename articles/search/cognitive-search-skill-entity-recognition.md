---
title: Umiejętność wyszukiwania poznawczego rozpoznawania jednostek — Azure Search
description: Wyodrębnij różne typy jednostek z tekstu w Azure Search potoku wyszukiwania poznawczego.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: ad2fef96491c2d1a15ad9ff5f57d2911dfecaa36
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265788"
---
#    <a name="entity-recognition-cognitive-skill"></a>Umiejętność rozpoznawania jednostek

Umiejętność **rozpoznawania jednostek** wyodrębnia jednostki różnych typów z tekstu. Ta umiejętność używa modeli uczenia maszynowego zapewnianych przez [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) w Cognitive Services.

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w Azure Search. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien składać się z 50 000 znaków mierzonych przez [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Jeśli konieczne jest rozbicie danych przed wysłaniem ich do wyodrębniania kluczowych fraz, rozważ użycie [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry umiejętności

W parametrach jest rozróżniana wielkość liter i są one opcjonalne.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| kategorie    | Tablica kategorii, które mają zostać wyodrębnione.  Możliwe typy kategorii: `"Person"`, `"Location"`, `"Organization"`,,, `"Quantity"`, .`"Email"` `"URL"` `"Datetime"` Jeśli nie podano żadnej kategorii, zwracane są wszystkie typy.|
|defaultLanguageCode |  Kod języka tekstu wejściowego. Obsługiwane są następujące języki:`de, en, es, fr, it`|
|minimumPrecision | Przestrzeń. Zarezerwowane do użytku w przyszłości. |
|includeTypelessEntities | Po ustawieniu na wartość true, jeśli tekst zawiera dobrze znaną jednostkę, ale nie można go podzielić na jedną z obsługiwanych kategorii. zostanie on zwrócony jako część `"entities"` złożonego pola danych wyjściowych. 
Są to jednostki, które są dobrze znane, ale nie są klasyfikowane jako część obecnie obsługiwanych kategorii. Na przykład "system Windows 10" jest dobrze znaną jednostką (produkt), ale "produkty" nie należą do obecnie obsługiwanych kategorii. Wartość domyślna to`false` |


## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

| Wprowadź nazwę      | Opis                   |
|---------------|-------------------------------|
| languageCode  | Opcjonalny. Wartość domyślna to `"en"`.  |
| text          | Tekst do analizy.          |

## <a name="skill-outputs"></a>Wyniki umiejętności

> [!NOTE]
> Nie wszystkie kategorie jednostek są obsługiwane we wszystkich językach. Tylko _pl_i _es_ `"Quantity"`obsługują wyodrębnianie `"Email"` typów `"Datetime"`, `"URL"`,,.

| Nazwa wyjściowa     | Opis                   |
|---------------|-------------------------------|
| podatnicy      | Tablica ciągów, w których każdy ciąg reprezentuje nazwę osoby. |
| lokalizacje  | Tablica ciągów, w których każdy ciąg reprezentuje lokalizację. |
| organizations  | Tablica ciągów, w których każdy ciąg reprezentuje organizację. |
| stawion  | Tablica ciągów, w których każdy ciąg reprezentuje liczbę. |
| Elementy DateTime  | Tablica ciągów, w których każdy ciąg reprezentuje datę i godzinę (jak pojawia się w tekście). |
| adresy | Tablica ciągów, w których każdy ciąg reprezentuje adres URL |
| wiadomości e-mail | Tablica ciągów, w których każdy ciąg reprezentuje wiadomość e-mail |
| namedEntities | Tablica typów złożonych, które zawierają następujące pola: <ul><li>category</li> <li>wartość (rzeczywista nazwa jednostki)</li><li>Przesunięcie (lokalizacja, w której została znaleziona).</li><li>pewność (nieużywany przez teraz. Zostanie ustawiona na wartość-1)</li></ul> |
| Obiekty | Tablica typów złożonych, która zawiera bogate informacje o jednostkach wyodrębnionych z tekstu, z następującymi polami <ul><li> Nazwa (rzeczywista nazwa jednostki). Reprezentuje to "znormalizowany" formularz</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (łącze do strony Wikipedia dla jednostki)</li><li>bingId</li><li>Typ (kategoria rozpoznanej jednostki)</li><li>Podtyp (dostępny tylko dla niektórych kategorii, zapewnia bardziej szczegółowy widok typu jednostki)</li><li> dopasowania (złożona Kolekcja zawierająca)<ul><li>tekst (nieprzetworzony tekst dla jednostki)</li><li>Przesunięcie (lokalizacja, w której została znaleziona)</li><li>Długość (długość nieprzetworzonego tekstu jednostki)</li></ul></li></ul> |

##  <a name="sample-definition"></a>Definicja Przykładowa

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##  <a name="sample-input"></a>Przykładowe dane wejściowe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": -1
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Przypadki błędów
Jeśli kod języka dla dokumentu nie jest obsługiwany, zwracany jest błąd i nie są wyodrębniane żadne jednostki.

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
