---
title: Umiejętność wyszukiwania poznawczego tonacji — Azure Search
description: Wyodrębnij pozytywnie negatywny wynik tonacji z tekstu w potoku wzbogacenia Azure Search.
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
ms.openlocfilehash: f950bea4ea32ecc95b1721c6930903c3afaee848
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347657"
---
#   <a name="sentiment-cognitive-skill"></a>Umiejętność tonacji

Umiejętność **tonacji** ocenia niestrukturalny tekst wzdłuż zwracanej do dodatniej wartości składnika Continuum, a dla każdego rekordu zwraca wynik liczbowy z zakresu od 0 do 1. Wyniki zbliżone do wartości 1 wskazują na pozytywną tonację, a wyniki zbliżone do wartości 0 wskazują na negatywną tonację. Ta umiejętność używa modeli uczenia maszynowego zapewnianych przez [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) w Cognitive Services.

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w Azure Search. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien składać się z 5000 znaków mierzonych przez [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Jeśli musisz podzielić dane przed wysłaniem ich do analizatora tonacji, użyj [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parametry umiejętności

W parametrach jest rozróżniana wielkość liter.

| Nazwa parametru |                      |
|----------------|----------------------|
| defaultLanguageCode | obowiązkowe Kod języka, który ma zostać zastosowany do dokumentów, które nie określają jawnie języka. <br/> Zapoznaj się [z pełną listą obsługiwanych języków](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji 

| Nazwa wejściowa | Opis |
|--------------------|-------------|
| text | Tekst do analizy.|
| languageCode  |  Obowiązkowe Ciąg wskazujący język rekordów. Jeśli ten parametr nie jest określony, wartością domyślną jest "en". <br/>Zapoznaj się [z pełną listą obsługiwanych języków](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Wyniki umiejętności

| Nazwa wyjściowa | Opis |
|--------------------|-------------|
| wynik | Wartość z zakresu od 0 do 1 reprezentująca tonacji przeanalizowanego tekstu. Wartości zbliżone do 0 mają ujemną tonacji, blisko 0,5 mają neutralną tonacji, a wartości zbliżone do 1 mają dodatnie tonacji.|


##  <a name="sample-definition"></a>Definicja Przykładowa

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
Jeśli wartość jest pusta, wynik tonacji nie jest zwracany dla tych rekordów.

## <a name="error-cases"></a>Przypadki błędów
Jeśli język nie jest obsługiwany, zostanie wygenerowany błąd i nie zostanie zwrócony żaden wynik tonacji.

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
