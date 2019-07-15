---
title: Analiza tonacji przy użyciu interfejsu API REST analizy tekstu z usług Azure Cognitive Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do wykrywania tonacji za pomocą interfejsu API REST analizy tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: c3004dd3910dd5fdafc933efa213c9f097310e87
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68001705"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>Przykład: Wykryj tonację z analizy tekstu

[Interfejsu API analizy tonacji Azure](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) ocenia tekst wejściowy i zwraca ocenę tonacji, dla każdego dokumentu. Wyniki z zakresu od 0 (negatywna) do 1 (pozytywna).

Ta możliwość jest przydatna do wykrywania pozytywnych i negatywnych opinii w mediach społecznościowych, recenzjach klientów i na forach dyskusyjnych. Zawartość została udostępniona przez użytkownika. Modele i dane szkoleniowe są dostarczane przez usługę.

Obecnie interfejs API analizy tonacji obsługuje język angielski, niemiecki, hiszpański i francuskim. Inne języki są dostępne w wersji zapoznawczej. Więcej informacji, zobacz [Obsługiwane języki](../text-analytics-supported-languages.md).

> [!TIP]
> Interfejs API analizy tekstu platformy Azure udostępnia również platformy Docker opartych na systemie Linux obraz kontenera do analizy tonacji, można więc [zainstalować i uruchomić kontener analizy tekstu](text-analytics-how-to-install-containers.md) blisko danych.

## <a name="concepts"></a>Pojęcia

Analiza tekstu używa algorytmu klasyfikacji uczenia maszynowego do wygenerowania oceny opinii z zakresu od 0 do 1. Wyniki zbliżone do wartości 1 wskazują na pozytywną opinię, a wyniki zbliżone do wartości 0 wskazują na negatywną opinię. Model jest wstępnie uczony za pomocą rozbudowanego zestawu tekstów ze skojarzonymi opiniami. Obecnie nie jest możliwe podanie danych szkoleniowych. Model używa kombinacji metod podczas analizy tekstu. Techniki obejmują przetwarzanie tekstu, analiza części mowy, umieszczania programu word i skojarzenia programu word. Aby uzyskać więcej informacji na temat algorytmu, zobacz [Introducing Text Analytics (Wprowadzenie do analizy tekstu)](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

Analiza opinii odbywa się dla całego dokumentu, w przeciwieństwie do wyodrębniania opinii dla konkretnej jednostki w tekście. W praktyce istnieje tendencja do oceny dokładności usprawniających gdy dokumenty zawierają jeden lub dwa zdania, a nie w dużych bloków tekstu. W fazie oceny obiektywizmu model określa, czy dokumentu jako całość jest obiektywny, czy też zawiera opinię. Dokument, który jest przeważnie cel nie postępu do fazy wykrywanie tonacji, co skutkuje 0,50 wynik, za pomocą żadne dalsze przetwarzanie. W przypadku dokumentów, które nadal w potoku do następnej fazy generuje wynik powyżej lub poniżej 0,50. Wynik zależy od stopnia tonacji wykryte w dokumencie.

## <a name="preparation"></a>Przygotowanie

Analiza tonacji daje wynik wyższej jakości, gdy tworzonemu elementowi nadawana mniejsze fragmenty tekstu do pracy nad. Jest to przeciwieństwo wyodrębniania kluczowych fraz, które działa lepiej na większych blokach tekstu. Aby uzyskać najlepsze wyniki dla obu operacji, rozważ odpowiednią zmianę struktury danych wejściowych.

Konieczne jest posiadanie dokumenty JSON w następującym formacie: Identyfikator, tekstu i język.

Rozmiar dokumentu musi być w obszarze 5,120 znaków w dokumencie. Może mieć maksymalnie 1000 elementów (ID) na kolekcję. Kolekcja jest przesyłana w treści żądania. Poniższy przykład jest przykładem zawartości, które mogą przesyłać do analizy tonacji:

```
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Krok 1: Określenie struktury żądania

Aby uzyskać więcej informacji na temat definicji żądania, zobacz [wywołania interfejsu API analizy tekstu](text-analytics-how-to-call-api.md). Dla wygody poniżej ponownie podano odpowiednie kroki:

+ Utwórz żądanie POST. Aby zapoznać się z dokumentacją interfejsu API dla tego żądania, zobacz [interfejsu API analizy tonacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9).

+ Ustaw punkt końcowy HTTP do analizy tonacji przy użyciu zasobów analizy tekstu na platformie Azure lub wystąpieniami [kontenera analizy tekstu](text-analytics-how-to-install-containers.md). Musi on zawierać `/sentiment` zasobów: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`.

+ Ustaw nagłówek żądania, tak aby zawierał klucz dostępu dla operacji analizy tekstu. Aby uzyskać więcej informacji, zobacz [Znajdź punkty końcowe i klucze dostępu](text-analytics-how-to-access-key.md).

+ W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

> [!Tip]
> Użyj [Postman](text-analytics-how-to-call-api.md) lub Otwórz **konsoli testowania interfejsu API** w [dokumentacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) struktury żądania i opublikuj go w usłudze.

## <a name="step-2-post-the-request"></a>Krok 2: Wysłanie żądania

Analiza jest wykonywana po odebraniu żądania. Aby uzyskać informacje na temat rozmiaru i liczby żądań można wysyłać na minutę i sekundę, zobacz [limity danych](../overview.md#data-limits) sekcja w przeglądzie.

Pamiętaj, że usługa jest bezstanowa. Żadne dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.


## <a name="step-3-view-the-results"></a>Krok 3: Wyświetlanie wyników

Analizator tonacji klasyfikuje tekstu jako głównie dodatnia lub ujemna. Przypisuje wynik do zakresu od 0 do 1. Wartości zbliżone do 0,5 oznaczają opinię neutralną lub brak opinii. Wynik 0,5 oznacza opinię neutralną. Ciąg nie może zostać przeanalizowane pod kątem tonacji lub ma nie tonacji, wynik jest zawsze 0,5 dokładnie. Na przykład jeśli przekażesz ciąg w języku hiszpańskim z kodem języka angielskiego, wynik będzie wynosić 0,5.

Dane wyjściowe są zwracane natychmiast. Można strumienia wyniki do aplikacji, która akceptuje JSON lub zapisywać dane wyjściowe do pliku w systemie lokalnym. Następnie można zaimportować dane wyjściowe do aplikacji, która umożliwia sortowanie, wyszukiwanie i manipulowania danymi.

Poniższy przykład przedstawia odpowiedź dla kolekcji dokumentów, w tym artykule:

```json
{
    "documents": [
        {
            "score": 0.9999237060546875,
            "id": "1"
        },
        {
            "score": 0.0000540316104888916,
            "id": "2"
        },
        {
            "score": 0.99990355968475342,
            "id": "3"
        },
        {
            "score": 0.980544924736023,
            "id": "4"
        },
        {
            "score": 0.99996328353881836,
            "id": "5"
        }
    ],
    "errors": []
}
```

## <a name="sentiment-analysis-v3-public-preview"></a>Publiczna wersja zapoznawcza tonacji analizy v3

[Następnej wersji analizy tonacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-preview/operations/56f30ceeeda5650db055a3c9) jest teraz dostępna w publicznej wersji zapoznawczej. Zapewnia znaczne ulepszenia w dokładności i szczegółowe informacje na temat interfejsu API tekstu kategoryzacji i oceniania. 

> [!NOTE]
> * Format żądania v3 analizę tonacji i [limity danych](../overview.md#data-limits) jest taka sama jak poprzednia wersja.
> * W tej chwili analiza tonacji w wersji 3: 
>    * Obecnie obsługuje tylko język angielski.  
>    * Jest dostępna w następujących regionach: `Central US`, `Central Canada`, i `East Asia`.

|Cecha |Opis  |
|---------|---------|
|Polepszenia dokładności     | znacznie lepsze niż w poprzednich wersjach wykrywanie pozytywnej, neutralnej, negatywnej i mieszanej tonacji dokumentów tekstowych.           |
|Dokument i tonacji na poziomie pojedynczych zdań     | wykrywanie tonacji zarówno dokumentu, jak i poszczególnych zdań. Jeśli dokument zawiera wiele zdań, wynik tonacji jest również przypisywany do każdego zdania.         |
|Kategoria opinii i oceny     | Interfejs API zwraca teraz kategorie tonacji dla tekstu, oprócz wyniku tonacji. Kategorie są `positive`, `negative`, `neutral`, i `mixed`.       |
| Ulepszone danych wyjściowych | Analiza tonacji teraz zwraca informacje dotyczące zarówno dokumentu cały tekst, jak i jej poszczególnych zdań. |

### <a name="sentiment-labeling"></a>Etykietowanie tonacji

Analiza tonacji w wersji 3 może zwrócić wyniki i etykiety na poziomie zdania i zarządzania dokumentami. Wyniki i etykiety były `positive`, `negative`, i `neutral`. Na poziomie dokumentu `mixed` etykiety tonacji (nie score) również mogą być zwracane. Tonację dokumentu jest określany przez agregację wyników zdania.

| Zdania tonacji                                                        | Zwrócone Etykieta dokumentu |
|---------------------------------------------------------------------------|----------------|
| Neutralne są co najmniej jedno zdanie dodatnią i pozostałej części zdania. | `positive`     |
| Neutralne są co najmniej jedno zdanie ujemny i pozostałej części zdania.  | `negative`     |
| Co najmniej jedno zdanie ujemny i co najmniej jedno zdanie dodatnią.         | `mixed`        |
| Wszystkie zdania są neutralne.                                                 | `neutral`      |

### <a name="sentiment-analysis-v3-example-request"></a>Żądania przykład v3 analizy tonacji

Następujący kod JSON jest przykładem żądania dotyczącego nową wersję analizy tonacji. Żądanie formatowania jest taka sama jak poprzednia wersja:

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Hello world. This is some input text that I love."
    },
    {
      "language": "en",
      "id": "2",
      "text": "It's incredibly sunny outside! I'm so happy."
    }
  ]
}
```

### <a name="sentiment-analysis-v3-example-response"></a>Wskaźniki nastrojów klientów analizy v3 przykładowa odpowiedź

Format żądania jest taka sama jak poprzednia wersja, zmienił się format odpowiedzi. Następujące dane JSON zamieszczono przykładową odpowiedź z nowej wersji interfejsu API:

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```

### <a name="example-c-code"></a>Przykład C# kodu

Przykład można znaleźć C# aplikacji, która wywołuje tę wersję analizę tonacji na [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy do analizy tonacji za pomocą analizy tekstu w usługach Azure Cognitive Services. Podsumowanie:

+ [Interfejsu API analizy tonacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) jest dostępny dla wybranych języków.
+ Dokumenty JSON w treści żądania obejmują identyfikator, tekstu i języka kodu.
+ Żądanie POST jest `/sentiment` punktu końcowego przy użyciu spersonalizowanego [dostępu key i punktu końcowego](text-analytics-how-to-access-key.md) , który jest poprawny dla Twojej subskrypcji.
+ Dane wyjściowe odpowiedzi, który składa się z wynikiem tonacji dla każdego Identyfikatora dokumentu, może być przesyłany strumieniowo do wszystkich aplikacji, która akceptuje JSON. Przykładowe aplikacje obejmują kilka program Excel i Power BI.

## <a name="see-also"></a>Zobacz także 

 [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)</br>
 [Strona produktu analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyodrębnianie kluczowych fraz](text-analytics-how-to-keyword-extraction.md)
