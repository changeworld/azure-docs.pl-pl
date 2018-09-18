---
title: Umiejętności wyszukiwania kognitywnego tonacji (Azure Search) | Dokumentacja firmy Microsoft
description: Wyodrębnij wskaźniki nastrojów klientów z tekstu w usłudze Azure Search wzbogacony potok.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 1e4028c3a810de41efe217e6dd4347fc3bc6bf16
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730888"
---
#   <a name="sentiment-cognitive-skill"></a>Umiejętności cognitive tonacji

**Tonacji** umiejętności ocenia tekstu bez struktury, wzdłuż począwszy dodatnie i ujemne, a dla każdego rekordu, zwraca wynik liczbowy z zakresu od 0 do 1. Wyniki zbliżone do wartości 1 wskazują na pozytywną tonację, a wyniki zbliżone do wartości 0 wskazują na negatywną tonację.

> [!NOTE]
> Wyszukiwanie poznawcze jest dostępne w publicznej wersji zapoznawczej. Wykonywanie zestawu umiejętności i wyodrębniania obrazu i normalizacji są obecnie oferowane bezpłatnie. W późniejszym czasie cen tych funkcji zostanie ogłoszona. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekord powinien być 5000 znaków, gdyż jest mierzone przez `String.Length`. Jeśli chcesz podzielić dane przed wysłaniem ich do analizatora tonacji, użyj [umiejętności dzielenie tekstu](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametrów jest rozróżniana wielkość liter.

| Nazwa parametru |                      |
|----------------|----------------------|
| defaultLanguageCode | (opcjonalnie) Kod języka stosowane do dokumentów, które jawnie określać języka. <br/> Zobacz [pełną listę obsługiwanych języków](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Dane wejściowe umiejętności 

| Wprowadź nazwę | Opis |
|--------------------|-------------|
| tekst | Tekst do analizy.|
| languageCode  |  (Opcjonalnie) Ciąg wskazujący język rekordów. Jeśli ten parametr nie jest określony, wartością domyślną jest "en". <br/>Zobacz [pełną listę obsługiwanych języków](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Dane wyjściowe umiejętności

| Nazwa wyjściowego | Opis |
|--------------------|-------------|
| wynik | Wartość z zakresu od 0 do 1, która przedstawia opinie o tekście przeanalizowany. Wartości zbliżone do wartości 0 ma negatywną tonację, blisko 0,5 neutralne tonacji, i wartości zbliżone do wartości 1 mieć pozytywną tonację.|


##  <a name="sample-definition"></a>Przykładowa definicja

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
W przypadku braku wyniku tonacji nie jest zwracana dla tych rekordów.

## <a name="error-cases"></a>W przypadku wystąpienia błędów
Jeśli język nie jest obsługiwana, zostanie wygenerowany błąd i zwracany jest nie wyniku tonacji.

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)