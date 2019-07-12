---
title: Analiza tonacji za pomocą analizy tekstu usług Azure Cognitive Services | Microsoft Docs
description: Dowiedz się, jak wykrywać opinię przy użyciu interfejsu API REST analizy tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: e17b68dfd63952d0c8c81415b090b047c5808e2e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797800"
---
# <a name="example-how-to-detect-sentiment-with-text-analytics"></a>Przykład: Jak wykryć tonację przy użyciu analizy tekstu

[Interfejs API analizy tonacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) ocenia tekst wejściowy i zwraca wynik opinii dla każdego dokumentu z zakresu od 0 (negatywna) do 1 (pozytywna).

Ta możliwość jest przydatna do wykrywania pozytywnych i negatywnych opinii w mediach społecznościowych, recenzjach klientów i na forach dyskusyjnych. Zawartość udostępniasz Ty, a modele i dane uczenia są dostarczane przez usługę.

Aktualnie analiza tonacji obsługuje angielski, niemiecki, hiszpański i francuski. Inne języki są dostępne w wersji zapoznawczej. Więcej informacji, zobacz [Obsługiwane języki](../text-analytics-supported-languages.md).

> [!TIP]
> Analiza tekstu udostępnia również obraz kontenera platformy Docker oparty na systemie Linux na potrzeby analizy tonacji, można więc [zainstalować i uruchomić kontener analizy tekstu](text-analytics-how-to-install-containers.md) blisko danych.

## <a name="concepts"></a>Pojęcia

Analiza tekstu używa algorytmu klasyfikacji uczenia maszynowego do wygenerowania oceny opinii z zakresu od 0 do 1. Wyniki zbliżone do wartości 1 wskazują na pozytywną opinię, a wyniki zbliżone do wartości 0 wskazują na negatywną opinię. Model jest wstępnie uczony za pomocą rozbudowanego zestawu tekstów ze skojarzonymi opiniami. Obecnie nie można określić własnych danych uczenia. Model używa kombinacji technik podczas analizy tekstu, w tym przetwarzania tekstu, analizy części mowy, rozmieszczenia słów i skojarzeń słów. Aby uzyskać więcej informacji na temat algorytmu, zobacz [Introducing Text Analytics (Wprowadzenie do analizy tekstu)](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

Analiza opinii odbywa się dla całego dokumentu, w przeciwieństwie do wyodrębniania opinii dla konkretnej jednostki w tekście. W praktyce dokładność określania opinii ma tendencję do poprawiania się, jeśli dokumenty zawierają jedno lub dwa zdania, a nie duży blok tekstu. W fazie oceny obiektywizmu model określa, czy dokumentu jako całość jest obiektywny, czy też zawiera opinię. Dokument w większości obiektywny nie przechodzi do fazy wykrywania tonacji, co daje wynik 0,50 bez dalszego przetwarzania. W przypadku dokumentów, których przetwarzanie jest kontynuowane, następna faza generuje wynik większy lub mniejszy niż 0,50 w zależności od opinii wykrytej w dokumencie.

## <a name="preparation"></a>Przygotowywanie

Analiza tonacji daje wynik wyższej jakości, jeśli pracuje na mniejszych fragmentach tekstu. Jest to przeciwieństwo wyodrębniania kluczowych fraz, które działa lepiej na większych blokach tekstu. Aby uzyskać najlepsze wyniki dla obu operacji, rozważ odpowiednią zmianę struktury danych wejściowych.

Konieczne jest posiadanie dokumenty JSON w następującym formacie: Identyfikator, tekstu, język

Dokument musi mieć mniej niż 5120 znaków, a kolekcja może zawierać maksymalnie 1000 elementów (identyfikatorów). Kolekcja jest przesyłana w treści żądania. Oto przykład zawartości, dla której można analizować tonację.

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

Szczegółowe informacje na temat definicji żądania można znaleźć w artykule [Jak wywołać interfejs API analizy tekstu](text-analytics-how-to-call-api.md). Dla wygody poniżej ponownie podano odpowiednie kroki:

+ Utwórz żądanie **POST**. Zapoznaj się z dokumentacją interfejsu API dla tego żądania: [Interfejs API analizy tonacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

+ Ustaw punkt końcowy HTTP na potrzeby analizy tonacji przy użyciu zasobów analizy tekstu na platformie Azure lub utworzonego wystąpienia [kontenera analizy tekstu](text-analytics-how-to-install-containers.md). Musi on obejmować zasób `/sentiment`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`

+ Ustaw nagłówek żądania, tak aby zawierał klucz dostępu dla operacji analizy tekstu. Aby uzyskać więcej informacji, zobacz [How to find endpoints and access keys (Jak znajdować punkty końcowe i klucze dostępu)](text-analytics-how-to-access-key.md).

+ W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

> [!Tip]
> Użyj programu [Postman](text-analytics-how-to-call-api.md) lub otwórz **konsolę testowania interfejsu API** w [dokumentacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9), aby określić strukturę żądania i przesłać je do usługi za pomocą operacji POST.

## <a name="step-2-post-the-request"></a>Krok 2: Wysłanie żądania

Analiza jest wykonywana po odebraniu żądania. Zobacz [limity danych](../overview.md#data-limits) sekcja w przeglądzie, aby uzyskać informacje na temat rozmiaru i liczby żądań można wysyłać na minutę i sekundę.

Pamiętaj, że usługa jest bezstanowa. Żadne dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.


## <a name="step-3-view-results"></a>Krok 3. Wyświetlanie wyników

Gdy analizator opinii sklasyfikuje tekst jako przeważnie pozytywny lub negatywny, przypisze mu wartość z zakresu od 0 do 1. Wartości zbliżone do 0,5 oznaczają opinię neutralną lub brak opinii. Wynik 0,5 oznacza opinię neutralną. Jeśli ciągu nie można przeanalizować pod kątem opinii lub nie zawiera on opinii, wynik zawsze wynosi dokładnie 0,5. Na przykład jeśli przekażesz ciąg w języku hiszpańskim z kodem języka angielskiego, wynik będzie wynosić 0,5.

Dane wyjściowe są zwracane natychmiast. Wyniki można przesłać strumieniowo do aplikacji, która akceptuje kod JSON, lub zapisać do pliku w systemie lokalnym, a następnie zaimportować do aplikacji, która umożliwia sortowanie i wyszukiwanie danych oraz manipulowanie nimi.

Poniższy przykład pokazuje odpowiedź dla kolekcji dokumentów w tym artykule.

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

## <a name="sentiment-analysis-v3-public-preview"></a>Publiczna wersja zapoznawcza V3 analizy tonacji

[Następnej wersji analizy tonacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-preview/operations/56f30ceeeda5650db055a3c9) jest teraz dostępna w publicznej wersji zapoznawczej, zapewniając znaczne ulepszenia w dokładności i szczegółowe informacje na temat interfejsu API tekstu kategoryzacji i oceniania. 

> [!NOTE]
> * Format żądania v3 analizę tonacji i [limity danych](../overview.md#data-limits) jest taka sama jak poprzednia wersja.
> * W tej chwili analiza tonacji w wersji 3: 
>    * Obecnie obsługuje tylko język angielski.  
>    * Jest dostępna w następujących regionach: `Central US`, `Central Canada`, `East Asia` 

|Cecha |Opis  |
|---------|---------|
|Polepszenia dokładności     | znacznie lepsze niż w poprzednich wersjach wykrywanie pozytywnej, neutralnej, negatywnej i mieszanej tonacji dokumentów tekstowych.           |
|Dokument i tonacji na poziomie pojedynczych zdań     | wykrywanie tonacji zarówno dokumentu, jak i poszczególnych zdań. Jeśli dokument zawiera wiele zdań, wynik tonacji jest również przypisywany do każdego zdania.         |
|Kategoria opinii i oceny     | Interfejs API zwraca teraz kategorie tonacji (`positive`, `negative`, `neutral` i `mixed`) dla tekstu, oprócz wyniku tonacji.        |
| Ulepszone danych wyjściowych | analiza tonacji zwraca teraz informacje zarówno dla całego dokumentu, jak i dla poszczególnych zdań. |

### <a name="sentiment-labeling"></a>Etykietowanie tonacji

Analiza tonacji V3 może zwrócić wyniki i etykiet (`positive`, `negative`, i `neutral`) na poziomie zdania i zarządzania dokumentami. Na poziomie dokumentu `mixed` etykiety tonacji (nie score) również mogą być zwracane. Tonację dokumentu jest określany przez agregowanie wyników jego zdania.

| Zdania tonacji                                                        | Zwrócone Etykieta dokumentu |
|---------------------------------------------------------------------------|----------------|
| Neutralne są co najmniej jedno zdanie dodatnią i pozostałej części zdania. | `positive`     |
| Neutralne są co najmniej jedno zdanie ujemny i pozostałej części zdania.  | `negative`     |
| Co najmniej jedno zdanie ujemny i co najmniej jedno zdanie dodatnią.         | `mixed`        |
| Wszystkie zdania są neutralne.                                                 | `neutral`      |

### <a name="sentiment-analysis-v3-example-request"></a>Wskaźniki nastrojów klientów analizy V3 przykładowe żądanie

Następujący kod JSON jest przykładem żądania dotyczącego nową wersję analizy tonacji. Należy zwrócić uwagę na to, że formatowanie żądania jest taka sama jak poprzednia wersja:

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

### <a name="sentiment-analysis-v3-example-response"></a>Analiza tonacji V3 przykładowa odpowiedź

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

W tym artykule przedstawiono pojęcia i przepływ pracy analizy tonacji za pomocą analizy tekstu w usłudze Cognitive Services. Podsumowanie:

+ [Interfejs API analizy tonacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) jest dostępny dla wybranych języków.
+ Dokumenty JSON w treści żądania obejmują identyfikator, tekstu i języka kodu.
+ Żądanie POST jest wysyłane do punktu końcowego `/sentiment` za pomocą spersonalizowanego [klucza dostępu i punktu końcowego](text-analytics-how-to-access-key.md) prawidłowego dla używanej subskrypcji.
+ Dane wyjściowe odpowiedzi składające się z oceny opinii dla każdego identyfikatora dokumentu można przesłać strumieniowo do każdej aplikacji akceptującej kod JSON, w tym na przykład do programu Excel i usługi Power BI.

## <a name="see-also"></a>Zobacz także 

 [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)</br>
 [Strona produktu analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyodrębnianie kluczowych fraz](text-analytics-how-to-keyword-extraction.md)
