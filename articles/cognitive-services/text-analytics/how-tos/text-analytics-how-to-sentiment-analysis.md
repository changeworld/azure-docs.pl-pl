---
title: Analiza tonacji przy użyciu interfejsu API REST analiza tekstu z platformy Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wykrywać tonacji za pomocą interfejsu API REST analiza tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: d516b4f71f78a5da113356a4bdf6647585292999
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161677"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>Przykład: wykrywanie tonacji z analiza tekstu

[Interfejs API usługi Azure analiza tonacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) oblicza dane wejściowe tekstu i zwraca wynik tonacji dla każdego dokumentu. Wyniki mieszczą się w zakresie od 0 (wartość ujemna) do 1 (wartość dodatnia).

Ta możliwość jest przydatna do wykrywania pozytywnych i negatywnych opinii w mediach społecznościowych, recenzjach klientów i na forach dyskusyjnych. Zawartość jest udostępniana przez użytkownika. Modele i dane szkoleniowe są udostępniane przez usługę.

Obecnie interfejs API analiza tonacji obsługuje w języku angielskim, niemieckim, hiszpańskim i francuskim. Inne języki są dostępne w wersji zapoznawczej. Więcej informacji, zobacz [Obsługiwane języki](../text-analytics-supported-languages.md).

> [!TIP]
> Usługa Azure interfejs API analizy tekstu udostępnia również obraz kontenera platformy Docker oparty na systemie Linux na potrzeby analizy tonacji, dzięki czemu można [zainstalować i uruchomić analiza tekstu kontener](text-analytics-how-to-install-containers.md) blisko swoich danych.

## <a name="concepts"></a>Pojęcia

Analiza tekstu używa algorytmu klasyfikacji uczenia maszynowego do wygenerowania oceny opinii z zakresu od 0 do 1. Wyniki zbliżone do wartości 1 wskazują na pozytywną opinię, a wyniki zbliżone do wartości 0 wskazują na negatywną opinię. Model jest wstępnie uczony za pomocą rozbudowanego zestawu tekstów ze skojarzonymi opiniami. Obecnie nie jest możliwe udostępnianie własnych danych szkoleniowych. Model używa kombinacji technik podczas analizy tekstu. Techniki obejmują przetwarzanie tekstu, analizę części mowy, umieszczanie słów i skojarzenia słów. Aby uzyskać więcej informacji na temat algorytmu, zobacz [Introducing Text Analytics (Wprowadzenie do analizy tekstu)](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

Analiza opinii odbywa się dla całego dokumentu, w przeciwieństwie do wyodrębniania opinii dla konkretnej jednostki w tekście. W rzeczywistości istnieje tendencja do oceny dokładności oceniania, gdy dokumenty zawierają jedno lub dwa zdania, a nie duży blok tekstu. W fazie oceny obiektywizmu model określa, czy dokumentu jako całość jest obiektywny, czy też zawiera opinię. Dokument, który jest przede wszystkim nie przechodzi do fazy wykrywania tonacji, co skutkuje wynikami 0,50, bez dalszej obróbki. W przypadku dokumentów, które kontynuują się w potoku, następna faza generuje wynik powyżej lub poniżej 0,50. Wynik zależy od stopnia wykrycia elementu tonacji w dokumencie.

## <a name="preparation"></a>Przygotowywanie

Analiza tonacji daje wynik wyższej jakości, gdy podajesz im mniejsze fragmenty tekstu do pracy. Jest to przeciwieństwo wyodrębniania kluczowych fraz, które działa lepiej na większych blokach tekstu. Aby uzyskać najlepsze wyniki dla obu operacji, rozważ odpowiednią zmianę struktury danych wejściowych.

Musisz mieć dokumenty JSON w tym formacie: ID, text i Language.

Rozmiar dokumentu musi zawierać 5 120 znaków na dokument. Możesz mieć do 1 000 elementów (identyfikatorów) na kolekcję. Kolekcja jest przesyłana w treści żądania. Poniższy przykład jest przykładem zawartości, która może zostać przesłana do analizy tonacji:

```json
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

Aby uzyskać więcej informacji na temat definicji żądania, zobacz [wywoływanie interfejs API analizy tekstu](text-analytics-how-to-call-api.md). Dla wygody poniżej ponownie podano odpowiednie kroki:

+ Utwórz żądanie POST. Aby zapoznać się z dokumentacją interfejsu API dla tego żądania, zobacz [interfejs api analiza tonacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9).

+ Ustaw punkt końcowy HTTP na potrzeby analizy tonacji przy użyciu zasobu analiza tekstu na platformie Azure lub [kontenera analiza tekstu](text-analytics-how-to-install-containers.md)wystąpienia. Należy uwzględnić `/text/analytics/v2.1/sentiment` w adresie URL. Na przykład: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`.

+ Ustaw nagłówek żądania w taki sposób, aby zawierał [klucz dostępu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dla operacji analiza tekstu.

+ W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

> [!Tip]
> Użyj programu [Poster](text-analytics-how-to-call-api.md) lub Otwórz **konsolę testowania interfejsu API** w [dokumentacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) , aby utworzyć strukturę żądania i opublikować ją w usłudze.

## <a name="step-2-post-the-request"></a>Krok 2: Wysłanie żądania

Analiza jest wykonywana po odebraniu żądania. Aby uzyskać informacje na temat rozmiaru i liczby żądań wysyłanych na minutę i sekundę, zobacz sekcję [limity danych](../overview.md#data-limits) w przeglądzie.

Pamiętaj, że usługa jest bezstanowa. Żadne dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.


## <a name="step-3-view-the-results"></a>Krok 3. Wyświetlanie wyników

Analizator tonacji klasyfikuje tekst jako "dodatnie" lub ujemne. Przypisuje wynik z zakresu od 0 do 1. Wartości zbliżone do 0,5 oznaczają opinię neutralną lub brak opinii. Wynik 0,5 oznacza opinię neutralną. Gdy nie można przeanalizować ciągu dla tonacji lub nie ma tonacji, wynik jest zawsze 0,5 dokładnie. Na przykład jeśli przekażesz ciąg w języku hiszpańskim z kodem języka angielskiego, wynik będzie wynosić 0,5.

Dane wyjściowe są zwracane natychmiast. Można przesyłać strumieniowo wyniki do aplikacji, która akceptuje kod JSON lub zapisuje dane wyjściowe do pliku w systemie lokalnym. Następnie zaimportuj dane wyjściowe do aplikacji, która może być używana do sortowania, wyszukiwania i manipulowania danymi.

Poniższy przykład przedstawia odpowiedzi dla kolekcji dokumentów w tym artykule:

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

## <a name="sentiment-analysis-v3-public-preview"></a>Publiczna wersja zapoznawcza analiza tonacji v3

[Następna wersja analiza tonacji](https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/56f30ceeeda5650db055a3c9) jest teraz dostępna w publicznej wersji zapoznawczej. Zapewnia znaczącą poprawę dokładności i szczegółowości kategoryzacji tekstu interfejsu API i oceniania.

> [!NOTE]
> * Format żądania analiza tonacji v3 i [limity danych](../overview.md#data-limits) są takie same jak w poprzedniej wersji.
> * W tej chwili analiza tonacji v3:
>    * Obecnie obsługuje angielski (`en`), japoński (`ja`), chiński uproszczony (`zh-Hans`), chiński tradycyjny (`zh-Hant`), francuski (`fr`), włoski (`it`), hiszpański (`es`), holenderski (`nl`), portugalski (`pt`) i niemiecki (`de`).
>    * Dostępne w następujących regionach: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`i `West US 2`.

|Funkcja |Opis  |
|---------|---------|
|Ulepszona dokładność     | znacznie lepsze niż w poprzednich wersjach wykrywanie pozytywnej, neutralnej, negatywnej i mieszanej tonacji dokumentów tekstowych.           |
|Wynik tonacji na poziomie dokumentu i zdania     | wykrywanie tonacji zarówno dokumentu, jak i poszczególnych zdań. Jeśli dokument zawiera wiele zdań, wynik tonacji jest również przypisywany do każdego zdania.         |
|Tonacji etykietowania i oceniania     | Interfejs API teraz zwraca tonacji kategorie dla tekstu, oprócz oceny tonacji. Kategorie są `positive`, `negative`, `neutral`i `mixed`.       |
| Ulepszone dane wyjściowe | Analiza tonacji teraz zwraca informacje dla całego dokumentu tekstowego i jego poszczególnych zdań. |
| Model — parametr wersji | Opcjonalny parametr służący do wybierania, która wersja modelu analiza tekstu jest używana w danych. |

### <a name="sentiment-labeling"></a>Etykietowanie tonacji

Analiza tonacji v3 może zwracać wyniki i etykiety na poziomie zdania i dokumentu. Wyniki i etykiety są `positive`, `negative`i `neutral`. Na poziomie dokumentu może być również zwracana etykieta `mixed` tonacji (a nie wynik). Tonacji dokumentu jest określany przez zsumowanie wyników zdań.

| Tonacji zdania                                                        | Etykieta zwracanego dokumentu |
|---------------------------------------------------------------------------|----------------|
| Co najmniej jedno zdanie dodatnie i pozostałe zdania są neutralne. | `positive`     |
| Co najmniej jedno zdanie negatywne i pozostałe zdania są neutralne.  | `negative`     |
| Co najmniej jedno wyrażenie negatywne i co najmniej jedno wyrażenie dodatnie.         | `mixed`        |
| Wszystkie zdania są neutralne.                                                 | `neutral`      |

### <a name="model-versioning"></a>Przechowywanie wersji modelu

Począwszy od wersji 3,0, interfejs API analizy tekstu pozwala wybrać model analiza tekstu używany na danych. Użyj opcjonalnego parametru `model-version`, aby wybrać wersję modelu w żądaniach. Jeśli ten parametr nie jest określony, interfejs API będzie domyślnie `latest`, Najnowsza stabilna wersja modelu.

Dostępne wersje modelu:
* `2019-10-01` (`latest`)

Każda odpowiedź z punktów końcowych v3 zawiera pole `model-version` określające używaną wersję modelu.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```

### <a name="sentiment-analysis-v3-example-request"></a>Przykładowe żądanie analiza tonacji v3

Poniższy kod JSON jest przykładem żądania wysłanego do nowej wersji analiza tonacji. Formatowanie żądania jest takie samo jak w poprzedniej wersji:

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
        ],
    }
```

### <a name="sentiment-analysis-v3-example-response"></a>Przykładowa odpowiedź analiza tonacji v3

Format żądania jest taki sam jak w poprzedniej wersji, więc format odpowiedzi został zmieniony. Poniższy kod JSON to Przykładowa odpowiedź z nowej wersji interfejsu API:

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

### <a name="example-c-code"></a>Przykładowy C# kod

Przykładową C# aplikację, która wywołuje tę wersję analiza tonacji w serwisie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs), można znaleźć.

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy analizy tonacji przy użyciu analiza tekstu na platformie Azure Cognitive Services. Podsumowanie:

+ [Interfejs API analiza tonacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) jest dostępny dla wybranych języków.
+ Dokumenty JSON w treści żądania obejmują identyfikator, tekst i kod języka.
+ Żądanie POST jest punktem końcowym `/sentiment` przy użyciu spersonalizowanego [klucza dostępu i punktu końcowego](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , który jest prawidłowy dla Twojej subskrypcji.
+ Dane wyjściowe odpowiedzi, które składają się z wyniku tonacji dla każdego identyfikatora dokumentu, mogą być przesyłane strumieniowo do dowolnej aplikacji, która akceptuje kod JSON. Przykładowe aplikacje to programy Excel i Power BI, aby podać kilka nazw.

## <a name="see-also"></a>Zobacz także

 [Omówienie analizy tekstu](../overview.md) [Frequently asked questions (FAQ) (Często zadawane pytania (FAQ))](../text-analytics-resource-faq.md)</br>
 [Strona produktu analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyodrębnianie kluczowych fraz](text-analytics-how-to-keyword-extraction.md)
