---
title: Umiejętność poznawcza rozpoznawania jednostek
titleSuffix: Azure Cognitive Search
description: Wyodrębnij różne typy jednostek z tekstu w potoku wzbogacania w usłudze Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6ef5952b6413563b2c2e16ff2218f709b414fb84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297816"
---
#    <a name="entity-recognition-cognitive-skill"></a>Umiejętność poznawcza rozpoznawania jednostek

Umiejętność **rozpoznawania jednostek** wyodrębnia jednostki różnych typów z tekstu. Ta umiejętność korzysta z modeli uczenia maszynowego dostarczonych przez [analizę tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) w usługach Cognitive Services.

> [!NOTE]
> W miarę rozszerzania zakresu poprzez zwiększanie częstotliwości przetwarzania, dodawanie większej liczby dokumentów lub dodawanie kolejnych algorytmów sztucznej inteligencji należy [dołączyć rozliczany zasób usług Cognitive Services.](cognitive-search-attach-cognitive-services.md) Opłaty naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu w ramach etapu pękania dokumentów w usłudze Azure Cognitive Search. Nie ma żadnych opłat za wyodrębnianie tekstu z dokumentów.
>
> Wykonanie wbudowanych umiejętności jest naliczane według istniejącej [ceny płatności zgodnie z rzeczywistymi oczekiwaniami.](https://azure.microsoft.com/pricing/details/cognitive-services/) Ceny wyodrębniania obrazów są opisane na [stronie cennika usługi Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien wynosić 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)znaków mierzonych za pomocą pliku . Jeśli musisz podzielić dane przed wysłaniem ich do ekstraktora fraz kluczowych, rozważ użycie [umiejętności Dzielenie tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry umiejętności

W parametrach rozróżniana jest wielkość liter i wszystkie są opcjonalne.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| categories    | Tablica kategorii, które powinny zostać wyodrębnione.  Możliwe typy `"Person"`kategorii: `"Quantity"`, `"Datetime"` `"URL"`, `"Email"` `"Location"` `"Organization"`, , , , . Jeśli nie podano żadnej kategorii, zwracane są wszystkie typy.|
|domyślny Kod Języka |    Kod języka tekstu wejściowego. Obsługiwane są następujące języki: `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans`. Nie wszystkie kategorie jednostek są obsługiwane dla wszystkich języków; patrz uwaga poniżej.|
|minimumPrecision (minimumPrecision) | Wartość z 0 do 1. Jeśli wynik zaufania (w danych wyjściowych) `namedEntities` jest niższa niż ta wartość, jednostka nie jest zwracana. Wartość domyślna to 0. |
|includeTypelessEntities (uwzględnij rodzaje) | Ustaw, `true` jeśli chcesz rozpoznać dobrze znane jednostki, które nie pasują do bieżących kategorii. Rozpoznane jednostki są `entities` zwracane w złożonym polu wyjściowym. Na przykład "Windows 10" jest dobrze znaną jednostką (produktem), ale ponieważ "Produkty" nie jest obsługiwaną kategorią, ta jednostka zostanie uwzględniona w polu wyjściowym jednostek. Wartość domyślna to`false` |


## <a name="skill-inputs"></a>Wprowadzanie umiejętności

| Nazwa wejściowa      | Opis                   |
|---------------|-------------------------------|
| languageCode    | Element opcjonalny. Wartość domyślna to `"en"`.  |
| tekst          | Tekst do analizy.          |

## <a name="skill-outputs"></a>Wyniki umiejętności

> [!NOTE]
> Nie wszystkie kategorie encji są obsługiwane dla wszystkich języków. `"Person"`Typy `"Location"`kategorii `"Organization"` , i encji są obsługiwane dla pełnej listy powyższych języków. Tylko _de_, _en_, _es_, _fr_, `"Datetime"`i `"URL"` _zh-hans_ wsparcie ekstrakcji `"Quantity"`, , , i `"Email"` typów. Aby uzyskać więcej informacji, zobacz [Obsługa języka i regionu interfejsu API analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support).  

| Nazwa wyjściowa      | Opis                   |
|---------------|-------------------------------|
| Osób       | Tablica ciągów, w których każdy ciąg reprezentuje nazwę osoby. |
| locations  | Tablica ciągów, w których każdy ciąg reprezentuje lokalizację. |
| organizations  | Tablica ciągów, w których każdy ciąg reprezentuje organizację. |
| Ilości  | Tablica ciągów, w których każdy ciąg reprezentuje ilość. |
| dateTimes  | Tablica ciągów, w których każdy ciąg reprezentuje DateTime (jak pojawia się w tekście) wartość. |
| Adresy url | Tablica ciągów, w których każdy ciąg reprezentuje adres URL |
| Wiadomości e-mail | Tablica ciągów, w których każdy ciąg reprezentuje wiadomość e-mail |
| namedEntities (Cechy) | Tablica typów złożonych zawierająca następujące pola: <ul><li>category</li> <li>wartość (rzeczywista nazwa jednostki)</li><li>przesunięcie (miejsce, w którym znaleziono go w tekście)</li><li>zaufanie (wyższa wartość oznacza, że bardziej jest to prawdziwy podmiot)</li></ul> |
| Podmioty | Tablica typów złożonych zawierająca zaawansowane informacje o jednostkach wyodrębnionych z tekstu z następującymi polami <ul><li> (rzeczywista nazwa encji. Reprezentuje to formularz "znormalizowany")</li><li> wikipediaId</li><li>wikipedia Język</li><li>wikipediaUrl (link do strony Wikipedii dla podmiotu)</li><li>bingId (ida)</li><li>typ (kategoria uznanego podmiotu)</li><li>podType (dostępne tylko dla niektórych kategorii, daje bardziej szczegółowy widok typu jednostki)</li><li> dopasowania (złożona kolekcja, która zawiera)<ul><li>tekst (tekst nieprzetworzony dla encji)</li><li>offset (miejsce, w którym został znaleziony)</li><li>długość (długość tekstu jednostki nieprzetworzonej)</li></ul></li></ul> |

##    <a name="sample-definition"></a>Przykładowa definicja

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
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
##    <a name="sample-input"></a>Przykładowe dane wejściowe

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

##    <a name="sample-output"></a>Przykładowe dane wyjściowe

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
            "confidence": 0.98
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

Należy zauważyć, że przesunięcia zwrócone dla jednostek w danych wyjściowych tej umiejętności są bezpośrednio zwracane z [interfejsu API analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), co oznacza, że jeśli używasz ich do indeksowania do oryginalnego ciągu, należy użyć [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) klasy w .NET w celu wyodrębnienia poprawnej zawartości.  [Więcej szczegółów można znaleźć tutaj.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-cases"></a>Przypadki błędów
Jeśli kod języka dokumentu nie jest obsługiwane, zwracany jest błąd i nie są wyodrębniane żadne jednostki.

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
