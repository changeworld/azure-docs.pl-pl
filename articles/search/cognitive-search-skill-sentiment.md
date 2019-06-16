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
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f52f5200f33d11db44d94b5a5f26d246f711e224
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65023800"
---
#   <a name="sentiment-cognitive-skill"></a>Umiejętności cognitive tonacji

**Tonacji** umiejętności ocenia tekstu bez struktury, wzdłuż począwszy dodatnie i ujemne, a dla każdego rekordu, zwraca wynik liczbowy z zakresu od 0 do 1. Wyniki zbliżone do wartości 1 wskazują na pozytywną tonację, a wyniki zbliżone do wartości 0 wskazują na negatywną tonację. Modele dostarczone przez uczenia maszynowego korzysta z tej umiejętności [analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) w usługach Cognitive Services.

> [!NOTE]
> Możesz rozwiń zakres, zwiększając częstotliwości przetwarzania, dodając więcej dokumentów lub dodanie więcej algorytmów sztucznej Inteligencji, konieczne będzie [dołączyć płatnych zasobu usług Cognitive Services](cognitive-search-attach-cognitive-services.md). Opłaty są naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu jako część etap łamania dokumentów w usłudze Azure Search. Opłaty nie będą naliczane do wyodrębniania tekstu z dokumentów.
>
> Wykonanie wbudowanego umiejętności podlega opłacie za istniejącą [usług Cognitive Services, płatności — jako — można przejść cena](https://azure.microsoft.com/pricing/details/cognitive-services/). Cennik wyodrębniania obraz został opisany na [usługi Azure Search stronę z cennikiem](https://go.microsoft.com/fwlink/?linkid=2042400).


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
