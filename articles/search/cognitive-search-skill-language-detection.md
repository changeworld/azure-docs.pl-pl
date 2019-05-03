---
title: Umiejętności wyszukiwania kognitywnego wykrywanie języka — usługa Azure Search
description: Ocenia tekstu bez struktury i dla każdego rekordu zwraca identyfikator języka z wynikiem wskazujący siły analizy w usłudze Azure Search wzbogacony potok.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: a076eee9818f294a8e5c4b10cebbcb9e5a55d80c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021847"
---
#   <a name="language-detection-cognitive-skill"></a>Umiejętności cognitive wykrywanie języka

**Wykrywanie języka** umiejętności wykrywa język wprowadzania tekstu i Raportowanie kodu jeden język dla każdego dokumentu na żądanie. Kod języka jest powiązany z wynikiem wskazujący siły analizy. Modele dostarczone przez uczenia maszynowego korzysta z tej umiejętności [analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) w usługach Cognitive Services.

Ta funkcja jest szczególnie przydatne w przypadku, gdy konieczne będzie podanie język tekstu jako dane wejściowe dla innych umiejętności (na przykład [umiejętności analizę tonacji](cognitive-search-skill-sentiment.md) lub [umiejętności dzielenie tekstu](cognitive-search-skill-textsplit.md)).

Wykrywanie języka wykorzystuje Bing przetwarzania języka naturalnego bibliotek, które przekracza liczbę o [obsługiwane języki i regiony](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) wymienione analizy tekstu. Dokładna lista języków nie został opublikowany, ale obejmuje wszystkie języki powszechnie używany znak plus wariantów, dialekty, a w niektórych językach regionalnych i kultury. W przypadku zawartości wyrażona w języku rzadziej używane mogą [Wypróbuj interfejs API wykrywania języka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) aby zobaczyć, jeśli zwracany jest kod. Odpowiedź dla języków, których nie można wykryć jest `unknown`.

> [!NOTE]
> Możesz rozwiń zakres, zwiększając częstotliwości przetwarzania, dodając więcej dokumentów lub dodanie więcej algorytmów sztucznej Inteligencji, konieczne będzie [dołączyć płatnych zasobu usług Cognitive Services](cognitive-search-attach-cognitive-services.md). Opłaty są naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu jako część etap łamania dokumentów w usłudze Azure Search. Opłaty nie będą naliczane do wyodrębniania tekstu z dokumentów.
>
> Wykonanie wbudowanego umiejętności podlega opłacie za istniejącą [usług Cognitive Services, płatności — jako — można przejść cena](https://azure.microsoft.com/pricing/details/cognitive-services/). Cennik wyodrębniania obraz został opisany na [usługi Azure Search stronę z cennikiem](https://go.microsoft.com/fwlink/?linkid=2042400).


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
| languageName | Nazwa języka. Na przykład "angielski". |
| wynik | Wartość z zakresu od 0 do 1. Prawdopodobieństwo, że język jest prawidłowo zidentyfikowany. Wynik może być mniejszy niż 1, jeśli zdanie zawiera różne języki.  |

##  <a name="sample-definition"></a>Przykładowa definicja

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
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
