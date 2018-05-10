---
title: Klucz frazy wyodrębniania kognitywnych wyszukiwania umiejętności (Azure Search) | Dokumentacja firmy Microsoft
description: Oblicza niestrukturalnych tekstu, a dla każdego rekordu, zwraca listę kluczy fraz w potoku wzbogacenia usługi Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a12efaa020e626e4a10a0708c9b84d8fe125588c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Umiejętności kognitywnych wyodrębniania frazy klucza

**Wyodrębniania frazy klucza** umiejętności ocenia niestrukturalnych tekstu, a następnie dla każdego rekordu, zwraca listę kluczy fraz.

Ta funkcja jest przydatna, jeśli chcesz szybko zidentyfikować głównych punktach wystąpienia w rekordzie. Na przykład dany tekst wejściowy "żywności został wyborna i wystąpiły cudowne personelu", usługa zwraca "żywności" i "cudowne pracownicy".

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinna być 50 000 znaków mierzony przez `String.Length`. Jeśli chcesz podzielić danych przed wysłaniem ich do wyodrębniania frazy klucza, należy rozważyć użycie [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametry jest rozróżniana wielkość liter.
| Dane wejściowe                | Opis |
|---------------------|-------------|
| defaultLanguageCode | (Opcjonalnie) Kod języka stosowane do dokumentów, które nie określają jawnie języka.  Jeśli domyślny kod języka nie jest określony, angielski (en) będzie używany jako domyślny kod języka. <br/> Zobacz [pełną listę obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Opcjonalnie) Maksymalna liczba kluczy fraz do produkcji. |

## <a name="skill-inputs"></a>Dane wejściowe umiejętności
| Dane wejściowe     | Opis |
|--------------------|-------------|
| Tekst | Tekst, który ma zostać przeanalizowany.|
| atrybutu languageCode  |  Ciąg wskazujący język rekordy. Jeśli ten parametr nie jest określony, domyślny kod języka będzie używany do analizowania rekordy. <br/>Zobacz [pełną listę obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Przykład definicji

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Błędy i ostrzeżenia
Jeśli podasz nieobsługiwany język kodu zostanie wygenerowany błąd i nie są wyodrębniane fraz klucza.
Jeśli tekst jest pusta, zostanie utworzony ostrzeżenie.
Jeśli tekst jest większy niż 50 000 znaków, będzie można analizować tylko pierwsze 50 000 znaków i zostanie wygenerowane ostrzeżenie.

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)