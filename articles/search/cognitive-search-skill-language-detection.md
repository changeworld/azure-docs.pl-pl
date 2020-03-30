---
title: Umiejętność poznawcza wykrywania języka
titleSuffix: Azure Cognitive Search
description: Oblicza tekst bez struktury i dla każdego rekordu zwraca identyfikator języka z wynikiem wskazującym siłę analizy w potoku wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8439788c63ec1b9feaea148ab52aba498791dc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045023"
---
#   <a name="language-detection-cognitive-skill"></a>Umiejętność poznawcza wykrywania języka

**Umiejętność wykrywania języka** wykrywa język tekstu wejściowego i zgłasza jeden kod języka dla każdego dokumentu przesłanego na żądanie. Kod języka jest sparowany z wynikiem wskazującym siłę analizy. Ta umiejętność korzysta z modeli uczenia maszynowego dostarczonych przez [analizę tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) w usługach Cognitive Services.

Ta funkcja jest szczególnie przydatna, gdy trzeba podać język tekstu jako dane wejściowe do innych umiejętności (na przykład [umiejętności Analiza tonacji](cognitive-search-skill-sentiment.md) lub [Dzielenie tekstu).](cognitive-search-skill-textsplit.md)

Wykrywanie języka wykorzystuje biblioteki przetwarzania języka naturalnego usługi Bing, które przekraczają liczbę [obsługiwanych języków i regionów](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) wymienionych w usłudze Text Analytics. Dokładna lista języków nie jest publikowana, ale zawiera wszystkie powszechnie używane języki, a także warianty, dialekty oraz niektóre języki regionalne i kulturowe. Jeśli masz zawartość wyrażoną w rzadziej używanym języku, możesz [wypróbować interfejs API wykrywania języka,](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) aby sprawdzić, czy zwraca kod. Odpowiedź dla języków, których nie `unknown`można wykryć, to .

> [!NOTE]
> W miarę rozszerzania zakresu poprzez zwiększanie częstotliwości przetwarzania, dodawanie większej liczby dokumentów lub dodawanie kolejnych algorytmów sztucznej inteligencji należy [dołączyć rozliczany zasób usług Cognitive Services.](cognitive-search-attach-cognitive-services.md) Opłaty naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu w ramach etapu pękania dokumentów w usłudze Azure Cognitive Search. Nie ma żadnych opłat za wyodrębnianie tekstu z dokumentów.
>
> Wykonanie wbudowanych umiejętności jest naliczane według istniejącej [ceny płatności zgodnie z rzeczywistymi oczekiwaniami.](https://azure.microsoft.com/pricing/details/cognitive-services/) Ceny wyodrębniania obrazów są opisane na [stronie cennika usługi Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien wynosić 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)znaków mierzonych za pomocą pliku . Jeśli musisz podzielić dane przed wysłaniem ich do umiejętności wykrywania języka, możesz użyć [umiejętności Dzielenie tekstu.](cognitive-search-skill-textsplit.md)

## <a name="skill-inputs"></a>Wprowadzanie umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Dane wejściowe     | Opis |
|--------------------|-------------|
| tekst | Tekst, który ma być analizowany.|

## <a name="skill-outputs"></a>Wyniki umiejętności

| Nazwa wyjściowa    | Opis |
|--------------------|-------------|
| languageCode | Kod języka ISO 6391 dla zidentyfikowanego języka. Na przykład "en". |
| językNaz. | Nazwa języka. Na przykład "angielski". |
| wynik | Wartość z 0 do 1. Prawdopodobieństwo, że język jest poprawnie zidentyfikowany. Wynik może być niższy niż 1, jeśli zdanie ma mieszane języki.  |

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


## <a name="error-cases"></a>Przypadki błędów
Jeśli tekst jest wyrażony w nieobsługiniętym języku, generowany jest błąd i nie jest zwracany identyfikator języka.

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
