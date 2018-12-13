---
title: Wskaźniki nastrojów klientów wyszukiwania kognitywnego umiejętności — usługa Azure Search
description: Wyodrębnij dodatnie i ujemne tonacji z tekstu w usłudze Azure Search wzbogacony potok.
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
ms.custom: seodec2018
ms.openlocfilehash: 4b8913d64a3df8799ba1d73972121ef331aaac81
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314078"
---
#   <a name="sentiment-cognitive-skill"></a>Umiejętności cognitive tonacji

**Tonacji** umiejętności ocenia tekstu bez struktury, wzdłuż począwszy dodatnie i ujemne, a dla każdego rekordu, zwraca wynik liczbowy z zakresu od 0 do 1. Wyniki zbliżone do wartości 1 wskazują na pozytywną tonację, a wyniki zbliżone do wartości 0 wskazują na negatywną tonację.

> [!NOTE]
> Od 21 grudnia 2018 r. można skojarzyć zasobu usług Cognitive Services za pomocą usługi Azure Search zestawu umiejętności. Pozwoli to nam będą naliczane opłaty za wykonywanie zestawu umiejętności. W tym dniu również Zaczniemy naliczać opłaty do wyodrębnienia obrazu jako część etap łamania dokumentów. Wyodrębnianie tekstu z dokumentów nadal będzie oferowane bez dodatkowych opłat.
>
> Wykonanie wbudowanego umiejętności będzie powodować obciążenie opłatami istniejące [usług Cognitive Services, płatności — jako — można przejść cena](https://azure.microsoft.com/pricing/details/cognitive-services/) . Cennik wyodrębniania obrazu będzie powodować obciążenie opłatami ceny za wersję zapoznawczą i został opisany na [usługi Azure Search stronę z cennikiem](https://go.microsoft.com/fwlink/?linkid=2042400). Dowiedz się, [więcej](cognitive-search-attach-cognitive-services.md).

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