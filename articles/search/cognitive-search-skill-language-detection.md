---
title: Język wykrywania kognitywnych wyszukiwania umiejętności (Azure Search) | Dokumentacja firmy Microsoft
description: Oblicza tekstu bez struktury i dla każdego rekordu, zwraca identyfikator języka z wynikiem wskazujący siły analizy w usłudze Azure Search wzbogacenia potoku.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 338d89b47ea451efcf8300d4ac016a6946a95259
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
#   <a name="language-detection-cognitive-skill"></a>Umiejętności kognitywnych wykrywania języka

W przypadku języków maksymalnie 120 **wykrywania języka** umiejętności wykrywa język wprowadzania tekstu i raporty jednego języka kodu dla każdego dokumentu przesyłane na żądanie. Kod języka jest skojarzone z wynikiem wskazujący siły analizy.

Ta funkcja jest szczególnie przydatne, gdy trzeba przekazać język tekstu jako dane wejściowe innych umiejętności (na przykład [umiejętności wskaźniki nastrojów klientów Snalysis](cognitive-search-skill-sentiment.md) lub [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md)).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinna być 50 000 znaków mierzony przez `String.Length`. Jeśli chcesz podzielić danych przed wysłaniem ich do analizatora wskaźniki nastrojów klientów, można użyć [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Dane wejściowe umiejętności

Parametry jest rozróżniana wielkość liter.

| Dane wejściowe     | Opis |
|--------------------|-------------|
| Tekst | Tekst, który ma zostać przeanalizowany.|

## <a name="skill-outputs"></a>Dane wyjściowe umiejętności

| Nazwa wyjściowego    | Opis |
|--------------------|-------------|
| atrybutu languageCode | Kod języka ISO 6391 językowej identyfikowanej. Na przykład "en". |
| NazwaJęzyka | Nazwa języka. Na przykład "angielski". |
| Wynik | Wartość z zakresu od 0 do 1. Prawdopodobieństwo, że język jest poprawnie zidentyfikowane. Wynik może być mniejszy niż 1, jeśli zdanie zawiera różne języków.  |

##  <a name="sample-definition"></a>Przykład definicji

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill ",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
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
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>W przypadku wystąpienia błędów
Jeśli tekst jest wyrażone w nieobsługiwany język, zostanie wygenerowany błąd i nie identyfikatora języka, jest zwracany.

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)