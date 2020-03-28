---
title: Przeprowadzanie analizy tonacji za pomocą interfejsu API REST analizy tekstu
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak wykryć tonację w tekście za pomocą interfejsu API REST usługi Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 03/09/2020
ms.author: aahi
ms.openlocfilehash: b3c112876bfd2578e6ebaa95c6902aa9b8f832d9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79203461"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Jak: wykrywanie tonacji za pomocą interfejsu API analizy tekstu

Funkcja analiza tonacji interfejsu API analizy treści analizy tekstu i zwraca wyniki tonacji oraz etykiety dla każdego zdania. Jest to przydatne do wykrywania pozytywnych i negatywnych nastrojów w mediach społecznościowych, opiniach klientów, forach dyskusyjnych i innych. Modele AI używane przez interfejs API są dostarczane przez usługę, wystarczy wysłać zawartość do analizy.

> [!TIP]
> Analiza tekstu udostępnia również obraz kontenera platformy Docker oparty na systemie Linux na potrzeby wykrywania języka, można więc [zainstalować i uruchomić kontener analizy tekstu](text-analytics-how-to-install-containers.md) blisko danych.

Analiza tonacji obsługuje szeroką gamę języków, a więcej w wersji zapoznawczej. Więcej informacji, zobacz [Obsługiwane języki](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Pojęcia

Interfejs API analizy tekstu używa algorytmu klasyfikacji uczenia maszynowego do generowania wyniku tonacji z 0 do 1. Wyniki zbliżone do wartości 1 wskazują na pozytywną opinię, a wyniki zbliżone do wartości 0 wskazują na negatywną opinię. Analiza tonacji jest przeprowadzana w całym dokumencie, a nie w poszczególnych jednostkach w tekście. Oznacza to, że wyniki tonacji są zwracane na poziomie dokumentu lub zdania. 

Używany model jest wstępnie przeszkolony z obszernym korpusem skojarzeń tekstu i opinii. Wykorzystuje kombinację technik do analizy, w tym przetwarzania tekstu, analizy części mowy, umieszczania wyrazów i skojarzeń wyrazów. Aby uzyskać więcej informacji na temat algorytmu, zobacz [Introducing Text Analytics (Wprowadzenie do analizy tekstu)](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/). Obecnie nie jest możliwe podanie własnych danych szkoleniowych. 

Istnieje tendencja do oceniania dokładności, aby poprawić, gdy dokumenty zawierają mniej zdań, a nie duży blok tekstu. W fazie oceny obiektywizmu model określa, czy dokumentu jako całość jest obiektywny, czy też zawiera opinię. Dokument, który jest głównie obiektywny, nie przechodzi do fazy wykrywania tonacji, co skutkuje wynikiem 0,50, bez dalszego przetwarzania. W przypadku dokumentów, które są kontynuowane w potoku, następna faza generuje wynik powyżej lub poniżej 0,50. Wynik zależy od stopnia tonacji wykrytego w dokumencie.

## <a name="sentiment-analysis-versions-and-features"></a>Wersje i funkcje analizy tonacji

Interfejs API analizy tekstu oferuje dwie wersje analizy tonacji — v2 i v3. Analiza tonacji w wersji 3 (publiczna wersja zapoznawcza) zapewnia znaczną poprawę dokładności i szczegółowości kategoryzacji i oceniania tekstu interfejsu API.

> [!NOTE]
> * Format żądania analizy tonacji w wersji 3 i [limity danych](../overview.md#data-limits) są takie same jak w poprzedniej wersji.
> * Analiza tonacji w wersji 3 `Australia East`jest `Central Canada` `Central US`dostępna `East Asia` `East US`w `East US 2` `North Europe`następujących `Southeast Asia` `South Central US`regionach: , , , , , , , , , `UK South`, `West Europe`, i `West US 2`.

| Funkcja                                   | Analiza tonacji v2 | Analiza tonacji v3 |
|-------------------------------------------|-----------------------|-----------------------|
| Metody dla pojedynczych i wsadowych żądań    | X                     | X                     |
| Wyniki tonacji dla całego dokumentu  | X                     | X                     |
| Wyniki tonacji dla poszczególnych zdań |                       | X                     |
| Etykietowanie tonacji                        |                       | X                     |
| Przechowywanie wersji modelu                   |                       | X                     |

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

### <a name="sentiment-scoring"></a>Ocenianie tonacji

Analiza tonacji w wersji 3 klasyfikuje tekst etykietami tonacji (opisanymi poniżej). Zwrócone wyniki reprezentują pewność modelu, że tekst jest dodatni, ujemny lub neutralny. Wyższe wartości oznaczają większą pewność siebie. 

### <a name="sentiment-labeling"></a>Etykietowanie tonacji

Analiza tonacji w wersji 3 może zwracać wyniki i etykiety na poziomie zdania i dokumentu. Wyniki i etykiety `positive` `negative`są `neutral`, i . Na poziomie dokumentu `mixed` etykieta tonacji również mogą być zwracane bez wyniku. Poniżej określono tonację dokumentu:

| Sentyment do zdania                                                                            | Zwrócona etykieta dokumentu |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Co najmniej `positive` jedno zdanie znajduje się w dokumencie. Reszta zdań `neutral`to . | `positive`              |
| Co najmniej `negative` jedno zdanie znajduje się w dokumencie. Reszta zdań `neutral`to . | `negative`              |
| Co najmniej `negative` jedno zdanie `positive` i co najmniej jedno zdanie znajdują się w dokumencie.    | `mixed`                 |
| Wszystkie zdania w dokumencie są `neutral`.                                                  | `neutral`               |

### <a name="model-versioning"></a>Przechowywanie wersji modelu

> [!NOTE]
> Wersja modelu do analizy tonacji `v3.0-preview.1`jest dostępna począwszy od wersji .

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>Przykładowy kod języka C#

Przykładową aplikację języka C#, która wywołuje tę wersję analizy tonacji w [usłudze GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/TextAnalyticsSentiment.cs)


#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

### <a name="sentiment-scoring"></a>Ocenianie tonacji

Analizator tonacji klasyfikuje tekst jako przeważnie dodatni lub negatywny. Przypisuje wynik w zakresie od 0 do 1. Wartości zbliżone do 0,5 oznaczają opinię neutralną lub brak opinii. Wynik 0,5 oznacza opinię neutralną. Gdy ciąg nie może być analizowany pod kątem tonacji lub nie ma sentymentu, wynik jest zawsze dokładnie 0,5. Na przykład jeśli przekażesz ciąg w języku hiszpańskim z kodem języka angielskiego, wynik będzie wynosić 0,5.

---

## <a name="sending-a-rest-api-request"></a>Wysyłanie żądania interfejsu API REST 

### <a name="preparation"></a>Przygotowywanie

Analiza tonacji daje wynik wyższej jakości, gdy dajesz mu mniejsze ilości tekstu do pracy. Jest to przeciwieństwo wyodrębniania kluczowych fraz, które działa lepiej na większych blokach tekstu. Aby uzyskać najlepsze wyniki dla obu operacji, rozważ odpowiednią zmianę struktury danych wejściowych.

Dokumenty JSON muszą być zawierane w tym formacie: identyfikator, tekst i język.

Rozmiar dokumentu musi być mniejszy niż 5120 znaków na dokument. Możesz mieć maksymalnie 1000 elementów (identyfikatory) na kolekcję. Kolekcja jest przesyłana w treści żądania.

## <a name="structure-the-request"></a>Określenie struktury żądania

Utwórz żądanie POST. [Listonosz](text-analytics-how-to-call-api.md) lub **konsoli testowania interfejsu API** w poniższych łączach referencyjnych, aby szybko struktury i wysłać jeden. 

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

[Odwołanie do analizy tonacji w wersji 3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

[Analiza tonacji w wersji 2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

Ustaw punkt końcowy HTTPS do analizy tonacji przy użyciu zasobu analizy tekstu na platformie Azure lub [kontenera analizy tekstu.](text-analytics-how-to-install-containers.md) Musisz dołączyć poprawny adres URL dla wersji, której chcesz użyć. Przykład:

> [!NOTE]
> Klucz i punkt końcowy zasobu analizy tekstu można znaleźć w witrynie Azure Portal. Będą one znajdować się na stronie **Szybki start** zasobu, w obszarze **Zarządzanie zasobami**. 

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

Ustaw nagłówek żądania, aby uwzględnić klucz interfejsu API analizy tekstu. W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

### <a name="example-sentiment-analysis-request"></a>Przykładowe żądanie analizy tonacji 

Oto przykład zawartości, dla której można analizować tonację. Format żądania jest taki sam dla obu wersji interfejsu API.
    
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

### <a name="post-the-request"></a>Wysłanie żądania

Analiza jest wykonywana po odebraniu żądania. Aby uzyskać informacje na temat rozmiaru i liczby żądań, które można wysyłać na minutę i sekundę, zobacz sekcję [limitów danych](../overview.md#data-limits) w przeglądzie.

Interfejs API analizy tekstu jest bezstanowy. Żadne dane nie są przechowywane na twoim koncie, a wyniki są zwracane natychmiast w odpowiedzi.


### <a name="view-the-results"></a>Wyświetlanie wyników

Analizator tonacji klasyfikuje tekst jako przeważnie dodatni lub negatywny. Przypisuje wynik w zakresie od 0 do 1. Wartości zbliżone do 0,5 oznaczają opinię neutralną lub brak opinii. Wynik 0,5 oznacza opinię neutralną. Gdy ciąg nie może być analizowany pod kątem tonacji lub nie ma sentymentu, wynik jest zawsze dokładnie 0,5. Na przykład jeśli przekażesz ciąg w języku hiszpańskim z kodem języka angielskiego, wynik będzie wynosić 0,5.

Dane wyjściowe są zwracane natychmiast. Wyniki można przesyłać strumieniowo do aplikacji, która akceptuje JSON lub zapisać dane wyjściowe w pliku w systemie lokalnym. Następnie zaimportuj dane wyjściowe do aplikacji, której można używać do sortowania, wyszukiwania i manipulowania danymi. Ze względu na obsługę wielojęzyczną i emoji odpowiedź może zawierać przesunięcia tekstu. Zobacz, [jak przetwarzać przesunięcia, aby](../concepts/text-offsets.md) uzyskać więcej informacji.

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>Analiza tonacji w wersji 3 przykładowa odpowiedź

Odpowiedzi z analizy tonacji w wersji 3 zawierają etykiety tonacji i wyniki dla każdego analizowanego zdania i dokumentu. `documentScores`nie jest zwracany, jeśli `mixed`etykieta tonacji dokumentu jest .

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

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>Analiza tonacji w wersji 2 przykładowa odpowiedź

Odpowiedzi z analizy tonacji w wersji 2 zawierają wyniki tonacji dla każdego wysłanego dokumentu.

```json
{
  "documents": [{
    "id": "1",
    "score": 0.98690706491470337
  }, {
    "id": "2",
    "score": 0.95202046632766724
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Podsumowanie

W tym artykule poznaliście pojęcia i przepływ pracy do analizy tonacji przy użyciu interfejsu API analizy tekstu. Podsumowanie:

+ Analiza tonacji jest dostępna dla wybranych języków w dwóch wersjach.
+ Dokumenty JSON w treści żądania zawierają identyfikator, tekst i kod języka.
+ Żądanie POST jest `/sentiment` do punktu końcowego przy użyciu [spersonalizowanego klucza dostępu i punktu końcowego,](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) który jest prawidłowy dla subskrypcji.
+ Dane wyjściowe odpowiedzi, która składa się z wyniku tonacji dla każdego identyfikatora dokumentu, mogą być przesyłane strumieniowo do dowolnej aplikacji, która akceptuje JSON. Na przykład excel i power bi.

## <a name="see-also"></a>Zobacz też

* [Analiza tekstu — omówienie](../overview.md)
* [Korzystanie z biblioteki klienta analizy tekstu](../quickstarts/text-analytics-sdk.md)
* [Co nowego](../whats-new.md)
