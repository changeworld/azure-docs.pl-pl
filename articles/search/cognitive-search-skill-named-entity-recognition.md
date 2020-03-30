---
title: Umiejętność poznawcza rozpoznawania nazwanych jednostek
titleSuffix: Azure Cognitive Search
description: Wyodrębnianie nazwanych encji dla osób, lokalizacji i organizacji z tekstu w potoku wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 127155e492b556ce1ce02b67cf0b0846b99ebcd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791950"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Umiejętność poznawcza rozpoznawania nazwanych jednostek

Umiejętności **Rozpoznawania nazwanych jednostek** wyodrębnia nazwane jednostki z tekstu. Dostępne jednostki obejmują `person` `location` typy i `organization`.

> [!IMPORTANT]
> Nazwana umiejętność rozpoznawania encji jest teraz przerywana przez [microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). Obsługa została wstrzymana 15 lutego 2019 r., a interfejs API został usunięty z produktu 2 maja 2019 r. Postępuj zgodnie z zaleceniami w [przestarzałe umiejętności wyszukiwania poznawczego,](cognitive-search-skill-deprecated.md) aby przeprowadzić migrację do obsługiwanej umiejętności.

> [!NOTE]
> W miarę rozszerzania zakresu poprzez zwiększanie częstotliwości przetwarzania, dodawanie większej liczby dokumentów lub dodawanie kolejnych algorytmów sztucznej inteligencji należy [dołączyć rozliczany zasób usług Cognitive Services.](cognitive-search-attach-cognitive-services.md) Opłaty naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu w ramach etapu pękania dokumentów w usłudze Azure Cognitive Search. Nie ma żadnych opłat za wyodrębnianie tekstu z dokumentów.
>
> Wykonanie wbudowanych umiejętności jest naliczane według istniejącej [ceny płatności zgodnie z rzeczywistymi oczekiwaniami.](https://azure.microsoft.com/pricing/details/cognitive-services/) Ceny wyodrębniania obrazów są opisane na [stronie cennika usługi Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien wynosić 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)znaków mierzonych za pomocą pliku . Jeśli musisz podzielić dane przed wysłaniem ich do ekstraktora fraz kluczowych, rozważ użycie [umiejętności Dzielenie tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| categories    | Tablica kategorii, które powinny zostać wyodrębnione.  Możliwe typy `"Person"`kategorii: , `"Location"`, `"Organization"`. Jeśli nie podano żadnej kategorii, zwracane są wszystkie typy.|
|domyślny Kod Języka |  Kod języka tekstu wejściowego. Obsługiwane są następujące języki:`de, en, es, fr, it`|
| minimumPrecision (minimumPrecision)  | Liczba z 0 do 1. Jeśli dokładność jest niższa niż ta wartość, jednostka nie jest zwracana. Wartość domyślna to 0.|

## <a name="skill-inputs"></a>Wprowadzanie umiejętności

| Nazwa wejściowa      | Opis                   |
|---------------|-------------------------------|
| languageCode  | Element opcjonalny. Wartość domyślna to `"en"`.  |
| tekst          | Tekst do analizy.          |

## <a name="skill-outputs"></a>Wyniki umiejętności

| Nazwa wyjściowa     | Opis                   |
|---------------|-------------------------------|
| Osób      | Tablica ciągów, w których każdy ciąg reprezentuje nazwę osoby. |
| locations  | Tablica ciągów, w których każdy ciąg reprezentuje lokalizację. |
| organizations  | Tablica ciągów, w których każdy ciąg reprezentuje organizację. |
| Podmioty | Tablica typów złożonych. Każdy typ złożony obejmuje następujące pola: <ul><li>kategoria (`"person"` `"organization"`, `"location"`, lub )</li> <li>wartość (rzeczywista nazwa jednostki)</li><li>przesunięcie (miejsce, w którym znaleziono go w tekście)</li><li>zaufanie (wartość między 0 a 1, która reprezentuje tę pewność, że wartość jest rzeczywistą jednostką)</li></ul> |

##  <a name="sample-definition"></a>Przykładowa definicja

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
    "defaultLanguageCode": "en",
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
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
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
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Przypadki błędów
Jeśli kod języka dokumentu nie jest obsługiwane, zwracany jest błąd i nie są wyodrębniane żadne jednostki.

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Umiejętność rozpoznawania encji](cognitive-search-skill-entity-recognition.md)
