---
title: O nazwie rozpoznawanie jednostek wyszukiwania kognitywnego umiejętności (Azure Search) | Dokumentacja firmy Microsoft
description: Wyodrębnij nazwane jednostek dla organizacji, lokalizacji i osoby z pliku tekstowego w potoku usługi Azure Search w usłudze wyszukiwania poznawczego.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: luisca
ms.openlocfilehash: f9ff3f66f3a73fbaf1a4c2ca280c85f4bde65444
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442033"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Nazwane umiejętności cognitive rozpoznawanie jednostek

**Rozpoznawania jednostki o nazwie** umiejętności wyodrębnia nazwane jednostek z pliku tekstowego. Dostępne jednostki i obejmuje dodatkowe typy `person`, `location` i `organization`.

> [!NOTE]
> <ul>
> <li>Wyszukiwanie poznawcze jest dostępne w publicznej wersji zapoznawczej. Możliwości wykonywania zestawów umiejętności oraz wyodrębniania i normalizacji obrazów są obecnie oferowane bezpłatnie. Cennik dla tych możliwości zostanie opublikowany w późniejszym czasie. </li>
> <li> Umiejętności rozpoznawanie podmiotów jest traktowane jako "przestarzałe" i nie będzie oficjalnie obsługiwana od 15 lutego 2019 r. Postępuj zgodnie z zaleceniami podanymi w <a href="cognitive-search-skill-deprecated.md">przestarzałe umiejętności poznawcze wyszukiwanie</a> strony, aby dokonać migracji do obsługiwanych umiejętności</li>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekord powinien być 50 000 znaków, gdyż jest mierzone przez `String.Length`. Jeśli zachodzi potrzeba Podziel swoje dane przed wysłaniem ich do wyodrębnianie kluczowych fraz, rozważ użycie [umiejętności dzielenie tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| categories    | Tablica kategorie, które mają zostać wyodrębnione.  Typy możliwe kategorii: `"Person"`, `"Location"`, `"Organization"`. Jeśli nie podano żadnej kategorii, zwracane są wszystkie typy.|
|defaultLanguageCode |  Kod języka tekstu wejściowego. Są obsługiwane następujące języki: `de, en, es, fr, it`|
| minimumPrecision  | Liczba od 0 do 1. Jeśli dokładność jest niższa niż ta wartość, nie są zwracane jednostki. Wartość domyślna to 0.|

## <a name="skill-inputs"></a>Dane wejściowe umiejętności

| Wprowadź nazwę      | Opis                   |
|---------------|-------------------------------|
| languageCode  | Opcjonalny. Wartość domyślna to `"en"`.  |
| tekst          | Tekst do przeanalizowania.          |

## <a name="skill-outputs"></a>Dane wyjściowe umiejętności

| Nazwa wyjściowego     | Opis                   |
|---------------|-------------------------------|
| osoby      | Tablica ciągów, gdzie każdy ciąg reprezentuje imię osoby. |
| lokalizacje  | Tablica ciągów, w którym każdy ciąg reprezentuje lokalizację. |
| organizations  | Tablica ciągów, w której każdy ciąg reprezentuje organizacji. |
| jednostki | Tablica typów złożonych. Każdy typ złożony zawiera następujące pola: <ul><li>Kategoria (`"person"`, `"organization"`, lub `"location"`)</li> <li>wartość (nazwa rzeczywistego jednostki)</li><li>Przesunięcie (lokalizację, w którym zostało znalezione w tekście)</li><li>zaufania (wartość z zakresu od 0 do 1, który reprezentuje tego zaufania, wartość rzeczywista jednostki)</li></ul> |

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
             "text": "This is the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
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


## <a name="error-cases"></a>W przypadku wystąpienia błędów
Jeśli kod języka dla dokumentu nie jest obsługiwany, zwracany jest błąd, a nie jednostki są wyodrębniane.

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Umiejętności rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md)
