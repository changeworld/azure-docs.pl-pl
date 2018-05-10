---
title: Wskaźniki nastrojów klientów kognitywnych wyszukiwania umiejętności (Azure Search) | Dokumentacja firmy Microsoft
description: Wyodrębnij wskaźniki nastrojów klientów z tekstu w potoku wzbogacenia usługi Azure Search.
services: search
manager: pablocas
author: luiscabrer
documentationcenter: ''
ms.assetid: ''
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 1ddbba5b881cd05a997cd24a9396d5b722376e6f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
#   <a name="sentiment-cognitive-skill"></a>Umiejętności kognitywnych wskaźniki nastrojów klientów

**Wskaźniki nastrojów klientów** umiejętności ocenia niestrukturalnych tekstu wzdłuż począwszy dodatnie i ujemne, a dla każdego rekordu, zwraca wynik numerycznych od 0 do 1. Wyniki zbliżone do wartości 1 wskazują na pozytywną tonację, a wyniki zbliżone do wartości 0 wskazują na negatywną tonację.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinna być 5000 znaków mierzony przez `String.Length`. Jeśli chcesz podzielić danych przed wysłaniem ich do analizatora wskaźniki nastrojów klientów, użyj [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametry jest rozróżniana wielkość liter.

| Nazwa parametru |                      |
|----------------|----------------------|
| defaultLanguageCode | (opcjonalnie) Kod języka stosowane do dokumentów, które nie określają jawnie języka. <br/> Zobacz [pełną listę obsługiwanych języków](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Dane wejściowe umiejętności 

| Wprowadź nazwę | Opis |
|--------------------|-------------|
| Tekst | Tekst, który ma zostać przeanalizowany.|
| atrybutu languageCode  |  (Opcjonalnie) Ciąg wskazujący język rekordy. Jeśli ten parametr nie jest określony, wartością domyślną jest "en". <br/>Zobacz [pełną listę obsługiwanych języków](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Dane wyjściowe umiejętności

| Nazwa wyjściowego | Opis |
|--------------------|-------------|
| Wynik | Wartość z zakresu od 0 do 1, który reprezentuje wskaźniki nastrojów klientów przeanalizowane tekstu. Wartości bliski 0 ma ujemną wskaźniki nastrojów klientów, bliski 0,5 neutralne wskaźniki nastrojów klientów, i wartości bliski 1 mieć dodatnią wskaźniki nastrojów klientów.|


##  <a name="sample-definition"></a>Przykład definicji

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
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
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>Uwagi
W przypadku braku wynik wskaźniki nastrojów klientów nie są zwracane dla tych rekordów.

## <a name="error-cases"></a>W przypadku wystąpienia błędów
Jeśli język nie jest obsługiwana, zostanie wygenerowany błąd i zwracany jest wynik nie wskaźniki nastrojów klientów.

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)