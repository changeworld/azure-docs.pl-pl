---
title: Umiejętności wyszukiwania kognitywnego wykrywania języka (Azure Search) | Dokumentacja firmy Microsoft
description: Ocenia tekstu bez struktury i dla każdego rekordu zwraca identyfikator języka z wynikiem wskazujący siły analizy w usłudze Azure Search wzbogacony potok.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 443ac895085053b7c4c876c3deecaa1943c9f506
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171062"
---
#   <a name="language-detection-cognitive-skill"></a>Umiejętności cognitive wykrywanie języka

W przypadku maksymalnie 120 języków **wykrywanie języka** umiejętności wykrywa język wprowadzania tekstu i Raportowanie kodu jeden język dla każdego dokumentu na żądanie. Kod języka jest powiązany z wynikiem wskazujący siły analizy.

Ta funkcja jest szczególnie przydatne w przypadku, gdy konieczne będzie podanie język tekstu jako dane wejściowe dla innych umiejętności (na przykład [umiejętności analizę tonacji](cognitive-search-skill-sentiment.md) lub [umiejętności dzielenie tekstu](cognitive-search-skill-textsplit.md)).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekord powinien być 50 000 znaków, gdyż jest mierzone przez `String.Length`. Jeśli zachodzi potrzeba Podziel swoje dane przed wysłaniem ich do analizatora opinii, można użyć [umiejętności dzielenie tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Dane wejściowe umiejętności

Parametrów jest rozróżniana wielkość liter.

| Dane wejściowe     | Opis |
|--------------------|-------------|
| tekst | Tekst do analizy.|

## <a name="skill-outputs"></a>Dane wyjściowe umiejętności

| Nazwa wyjściowego    | Opis |
|--------------------|-------------|
| languageCode | Kod języka ISO 6391 dla języka zidentyfikowane. Na przykład "en". |
| NazwaJęzyka | Nazwa języka. Na przykład "angielski". |
| wynik | Wartość z zakresu od 0 do 1. Prawdopodobieństwo, że język jest prawidłowo zidentyfikowany. Wynik może być mniejszy niż 1, jeśli zdanie zawiera różne języki.  |

##  <a name="sample-definition"></a>Przykładowa definicja

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
Jeśli tekst jest będzie wyrażana nieobsługiwany język, generowany jest błąd, a nie identyfikatora języka jest zwracana.

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
