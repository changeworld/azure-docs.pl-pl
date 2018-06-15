---
title: O nazwie rozpoznawanie jednostek wyszukiwania kognitywnych umiejętności (Azure Search) | Dokumentacja firmy Microsoft
description: Wyodrębnij nazwane jednostki dla organizacji, lokalizacji i osoby z tekstu w potoku kognitywnych wyszukiwania usługi Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 73ffcf5e2ced63fddaf0f5ef2ca7e72a7d94b966
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33791042"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Nazwane umiejętności kognitywnych rozpoznawania jednostek

**Rozpoznawanie jednostek o nazwie** umiejętności wyodrębnia nazwane jednostki w tekście. Dostępne jednostki obejmują `person`, `location`, i `organization`.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametry jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| kategorie    | Tablica kategorie, które należy wyodrębnić.  Typy możliwe kategorii: `"Person"`, `"Location"`, `"Organization"`. Jeśli podano bez kategorii, zwracane są wszystkie typy.|
|defaultLanguageCode |  Kod języka wejściowego tekstu. Obsługiwane są następujące języki: `ar, cs, da, de, en, es, fi, fr, he, hu, it, ko, pt-br, pt`|
| minimumPrecision  | Liczbą z zakresu od 0 do 1. Jeśli dokładność jest starsza niż ta wartość, nie są zwracane jednostki. Wartość domyślna to 0.|

## <a name="skill-inputs"></a>Dane wejściowe umiejętności

| Wprowadź nazwę      | Opis                   |
|---------------|-------------------------------|
| atrybutu languageCode  | Opcjonalny. Wartość domyślna to `"en"`.  |
| Tekst          | Tekst do analizy.          |

## <a name="skill-outputs"></a>Dane wyjściowe umiejętności

| Nazwa wyjściowego     | Opis                   |
|---------------|-------------------------------|
| osoby      | Tablica ciągów, gdzie każdy ciąg reprezentuje imię osoby. |
| Lokalizacje  | Tablica ciągów, gdzie każdy ciąg reprezentuje lokalizację. |
| Organizacje  | Tablica ciągów, gdzie każdy ciąg reprezentuje organizacji. |
| jednostki | Tablica typów złożonych. Każdy typ złożony zawiera następujące pola: <ul><li>Kategoria (`"person"`, `"organization"`, lub `"location"`)</li> <li>wartość (nazwa rzeczywista jednostki)</li><li>Przesunięcie (lokalizację, w którym zostało znalezione w tekście)</li><li>zaufania (wartość z zakresu od 0 do 1, która reprezentuje tego zaufania, wartość rzeczywista jednostki)</li></ul> |

##  <a name="sample-definition"></a>Przykład definicji

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person"],
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
             "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
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
        "locations": ["Seattle"],
        "organizations":["Microsoft Corporation"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 45,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 59,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Seattle",
            "offset": 5,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft Corporation",
            "offset": 120,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>W przypadku wystąpienia błędów
Określ kod nieobsługiwany język lub jeśli zawartość nie jest zgodny język określony, zwracany jest błąd i są wyodrębniane żadnych jednostek.

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)