---
title: Wyodrębnianie fraz kluczowych przy użyciu interfejsu API REST analizy tekstu
titleSuffix: Azure Cognitive Services
description: Jak wyodrębnić frazy kluczowe przy użyciu interfejsu API REST analizy tekstu z usługi Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/29/2019
ms.author: raymondl
ms.openlocfilehash: ec5ff756d7e732430675676868bc754627a2a4a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72429028"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Przykład: Jak wyodrębnić kluczowe frazy za pomocą analizy tekstu

[Interfejs API wyodrębniania kluczowych fraz](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) ocenia tekst bez struktury i dla każdego dokumentu JSON zwraca listę kluczowych fraz.

Ta możliwość jest przydatna, jeśli chcesz szybko zidentyfikować główne tematy w kolekcji dokumentów. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” usługa zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”.

Więcej informacji, zobacz [Obsługiwane języki](../text-analytics-supported-languages.md).

> [!TIP]
> Analiza tekstu udostępnia również obraz kontenera platformy Docker oparty na systemie Linux na potrzeby wyodrębniania kluczowych fraz, można więc [zainstalować i uruchomić kontener analizy tekstu](text-analytics-how-to-install-containers.md) blisko danych.

## <a name="preparation"></a>Przygotowywanie

Wyodrębnianie kluczowych fraz działa najlepiej na większej ilości tekstu. Jest to przeciwieństwo analizy tonacji, która działa lepiej na mniejszej ilości tekstu. Aby uzyskać najlepsze wyniki dla obu operacji, rozważ odpowiednią zmianę struktury danych wejściowych.

Dokumenty JSON muszą być dostępne w tym formacie: identyfikator, tekst, język

Rozmiar dokumentu musi wynosić 5120 lub mniej znaków na dokument, a na kolekcję może znajdować się maksymalnie 1000 elementów. Kolekcja jest przesyłana w treści żądania. Poniższy przykład przedstawia zawartość, którą możesz przesłać w celu wyodrębnienia kluczowych fraz.

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

Aby uzyskać informacje na temat definicji żądania, zobacz [Jak wywołać interfejs API analizy tekstu](text-analytics-how-to-call-api.md). Dla wygody poniżej ponownie podano odpowiednie kroki:

+ Utwórz żądanie **POST**. Przejrzyj dokumentację interfejsu API dla tego żądania: [Key Phrases API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6).

+ Ustaw punkt końcowy HTTP dla wyodrębniania fraz kluczowych przy użyciu zasobu analizy tekstu na platformie Azure lub [kontenera analizy tekstu.](text-analytics-how-to-install-containers.md) Musisz podać `/text/analytics/v2.1/keyPhrases` w adresie URL. Na przykład: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v2.1/keyPhrases`.

+ Ustaw nagłówek żądania, aby uwzględnić [klucz dostępu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dla operacji analizy tekstu.

+ W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

> [!Tip]
> Użyj programu [Postman](text-analytics-how-to-call-api.md) lub otwórz **konsolę testowania interfejsu API** w [dokumentacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6), aby określić strukturę żądania i przesłać je do usługi za pomocą operacji POST.

## <a name="step-2-post-the-request"></a>Krok 2: Wysłanie żądania

Analiza jest wykonywana po odebraniu żądania. Aby uzyskać informacje o rozmiarze i liczbie żądań, które można wysyłać na minutę lub na sekundę, zobacz sekcję [limitów danych](../overview.md#data-limits) w przeglądzie .

Pamiętaj, że usługa jest bezstanowa. Żadne dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.

## <a name="step-3-view-results"></a>Krok 3: Wyświetlenie wyników

Wszystkie żądania POST zwracają odpowiedź w formacie JSON z identyfikatorami i wykrytymi właściwościami. Kolejność zwracanych fraz kluczowych jest określana wewnętrznie przez model.

Dane wyjściowe są zwracane natychmiast. Wyniki można przesłać strumieniowo do aplikacji, która akceptuje kod JSON, lub zapisać do pliku w systemie lokalnym, a następnie zaimportować do aplikacji, która umożliwia sortowanie i wyszukiwanie danych oraz manipulowanie nimi.

W tym miejscu pokazano przykład danych wyjściowych dla wyodrębniania kluczowych fraz:

```json
    {
        "documents": [
            {
                "keyPhrases": [
                    "year",
                    "trail",
                    "trip",
                    "views"
                ],
                "id": "1"
            },
            {
                "keyPhrases": [
                    "marked trails",
                    "Worst hike",
                    "goners"
                ],
                "id": "2"
            },
            {
                "keyPhrases": [
                    "trail",
                    "small children",
                    "family"
                ],
                "id": "3"
            },
            {
                "keyPhrases": [
                    "spectacular views",
                    "trail",
                    "area"
                ],
                "id": "4"
            },
            {
                "keyPhrases": [
                    "places",
                    "beautiful views",
                    "favorite trail"
                ],
                "id": "5"
            }
        ],
        "errors": []
    }
```

Jak wspomniano, analizator znajduje i odrzuca inne niż istotne słowa i zachowuje pojedyncze terminy lub wyrażenia, które wydają się być przedmiotem lub przedmiotem zdania.

## <a name="summary"></a>Podsumowanie

W tym artykule nauczysz się pojęć i przepływu pracy dla wyodrębniania fraz kluczowych przy użyciu analizy tekstu w usługach Cognitive Services. Podsumowanie:

+ [Interfejs API wyodrębniania kluczowych fraz](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) jest dostępny dla wybranych języków.
+ Dokumenty JSON w treści żądania zawierają identyfikator, tekst i kod języka.
+ Żądanie POST jest wysyłane do punktu końcowego `/keyphrases` za pomocą spersonalizowanego [klucza dostępu i punktu końcowego](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) prawidłowego dla używanej subskrypcji.
+ Dane wyjściowe odpowiedzi, które składają się ze słów kluczowych i fraz dla każdego identyfikatora dokumentu, mogą być przesyłane strumieniowo do dowolnej aplikacji, która akceptuje JSON, w tym Microsoft Office Excel i Power BI, aby wymienić tylko kilka.

## <a name="see-also"></a>Zobacz też

 [Omówienie analizy tekstu](../overview.md) [Frequently asked questions (FAQ) (Często zadawane pytania (FAQ))](../text-analytics-resource-faq.md)</br>
 [Strona produktu analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Interfejs API analizy tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
