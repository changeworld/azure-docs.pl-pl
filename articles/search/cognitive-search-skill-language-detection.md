---
title: Umiejętność wyszukiwania w języku wykrywanie poznawcze — Azure Search
description: Oblicza niestrukturalny tekst, a dla każdego rekordu zwraca identyfikator języka z oceną wskazującą siłę analizy w potoku wzbogacenia Azure Search.
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
ms.openlocfilehash: 3685e2f2a212591788c62ba4f0384ae320d89824
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347766"
---
#   <a name="language-detection-cognitive-skill"></a>Umiejętność wykrywania języka

Umiejętność **wykrywanie języka** wykrywa język tekstu wejściowego i raportuje jeden kod języka dla każdego dokumentu przesłanego w żądaniu. Kod języka jest sparowany z wynikiem wskazującym na siłę analizy. Ta umiejętność używa modeli uczenia maszynowego zapewnianych przez [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) w Cognitive Services.

Ta funkcja jest szczególnie przydatna, gdy trzeba dostarczyć język tekstu jako dane wejściowe do innych umiejętności (na przykład [umiejętności analiza tonacji](cognitive-search-skill-sentiment.md) lub [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md)).

Wykrywanie języka korzysta z bibliotek przetwarzania języka naturalnego Bing, które przekraczają liczbę [obsługiwanych języków i regionów](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) wymienionych dla analiza tekstu. Dokładna lista języków nie jest publikowana, ale zawiera wszystkie języki szeroko wypowiadane oraz warianty, dialekty i niektóre języki regionalne i kulturowe. Jeśli masz zawartość wyrażoną w rzadziej używanym języku, możesz wypróbować [interfejs API wykrywanie języka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) , aby zobaczyć, czy zwraca kod. Odpowiedź dla języków, których nie można wykryć, `unknown`to.

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w Azure Search. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien składać się z 50 000 znaków mierzonych przez [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Jeśli musisz podzielić dane przed wysłaniem ich do analizatora tonacji, możesz użyć [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

W parametrach jest rozróżniana wielkość liter.

| Dane wejściowe     | Opis |
|--------------------|-------------|
| text | Tekst do analizy.|

## <a name="skill-outputs"></a>Wyniki umiejętności

| Nazwa wyjściowa    | Opis |
|--------------------|-------------|
| languageCode | Kod języka ISO 6391 dla zidentyfikowanego języka. Na przykład "en". |
| język | Nazwa języka. Na przykład "angielski". |
| wynik | Wartość z zakresu od 0 do 1. Prawdopodobieństwo poprawnego zidentyfikowania języka. Wynik może być mniejszy niż 1, jeśli zdanie ma Języki mieszane.  |

##  <a name="sample-definition"></a>Definicja Przykładowa

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


## <a name="error-cases"></a>Przypadki błędów
Jeśli tekst jest wyrażony w nieobsługiwanym języku, generowany jest błąd i nie jest zwracany żaden identyfikator języka.

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
