---
title: Korzystanie z rozpoznawania jednostek z interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak identyfikować i odróżnić tożsamość jednostki znalezionej w tekście za pomocą interfejsu API REST analiza tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 10/21/2019
ms.author: aahi
ms.openlocfilehash: b139f473bb20dbecde75c3b08dd92371849f096e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835661"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Jak używać rozpoznawania jednostek nazwanych w analiza tekstu

[Interfejs API rozpoznawania jednostek nazwanych](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) Pobiera tekst bez struktury, a dla każdego dokumentu JSON zwraca listę niejednoznacznych jednostek z linkami do dodatkowych informacji na temat sieci Web (Wikipedia i Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Łączenie jednostek i rozpoznawanie nazwanych jednostek

Analiza tekstu "`entities` punkt końcowy obsługuje zarówno rozpoznawanie jednostek nazwanych (NER), jak i łączenie jednostek.

### <a name="entity-linking"></a>Łączenie jednostek
Łączenie jednostek to możliwość identyfikacji i odróżnienia tożsamości jednostki znalezionej w tekście (na przykład określenie, czy element "Mars" jest używany jako globalnej, czy jako Rzymskie akty Boże War). Ten proces wymaga obecności bazy wiedzy, do której są połączone powiązane jednostki — jest używana jako baza wiedzy dla analiza tekstu punktu końcowego `entities`.

### <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych (NER)
Funkcja rozpoznawania jednostek nazwanych (NER) to możliwość identyfikowania różnych jednostek w tekście i kategoryzowania ich w wstępnie zdefiniowanych klasach lub typach. 

## <a name="named-entity-recognition-v3-public-preview"></a>Niestandardowa wersja próbna rozpoznawania jednostek v3

[Następna wersja rozpoznawania nazwanych jednostek](https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/56f30ceeeda5650db055a3c7/console) jest teraz dostępna w publicznej wersji zapoznawczej. Udostępnia ona aktualizacje zarówno do łączenia jednostek, jak i nazwanego rozpoznawania jednostek. 

:::row:::
    :::column span="":::
        **Funkcja**
    :::column-end:::
    ::: column span="":::
        **Opis** 
    :::column-end:::
:::row-end:::
<!-- expanded types and subtypes row-->
:::row:::
    :::column span="":::
        Rozwinięte typy i podtypy jednostek
    :::column-end:::
    :::column span="":::
     Rozszerzona klasyfikacja i wykrywanie dla kilku typów jednostek nazwanych.
    :::column-end:::
:::row-end:::
<!-- separate endpoints row-->
:::row:::
    :::column span="":::
        Oddziel punkty końcowe żądania 
    :::column-end:::
    :::column span="":::
        Oddziel punkty końcowe do wysyłania połączeń jednostek i żądań NER.
    :::column-end:::
:::row-end:::
<!-- model-version row -->
:::row:::
    :::column span="":::
        `model-version` parametr
    :::column-end:::
    :::column span="":::
        Opcjonalny parametr służący do wybierania wersji modelu analiza tekstu. Obecnie tylko domyślny model jest dostępny do użycia.
    :::column-end:::
:::row-end:::

### <a name="entity-types"></a>Typy jednostek

Rozpoznawanie jednostek nazwanych v3 zapewnia rozszerzone wykrywanie w wielu typach. Obecnie NER v3 może rozpoznać następujące kategorie jednostek. Aby uzyskać szczegółową listę obsługiwanych jednostek i języków, zobacz artykuł [typy jednostek nazwanych](../named-entity-types.md) .

* Ogólne
* Dane osobowe 

### <a name="request-endpoints"></a>Punkty końcowe żądania

Nazwanego rozpoznawania jednostek v3 używa oddzielnych punktów końcowych dla żądań NER i konsolidacji jednostek. Użyj poniższego formatu adresu URL na podstawie Twojego żądania:

NER
* Jednostki ogólne — `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Jednostki danych osobowych — `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Łączenie jednostek
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Przechowywanie wersji modelu

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

## <a name="supported-types-for-named-entity-recognition-v2"></a>Obsługiwane typy dla nazwanego rozpoznawania jednostek v2

> [!NOTE]
> Następujące jednostki są obsługiwane przez funkcję rozpoznawania jednostek nazwanych (NER) w wersji 2. [Ner v3](#named-entity-recognition-v3-public-preview) jest dostępna w publicznej wersji zapoznawczej i znacząco rozszerza liczbę i głębokość jednostek rozpoznawanych w tekście.   

| Typ  | Podtyp | Przykład |
|:-----------   |:------------- |:---------|
| Person (Osoba)        | Nie dotyczy\*         | "Jan", "bramy Bill"     |
| Lokalizacja      | Nie dotyczy\*         | „Redmond, Washington”, „Paris”  |
| Organizacja  | Nie dotyczy\*         | „Microsoft”   |
| Liczba      | Liczba        | „6”, „six”     |
| Liczba      | Wartość procentowa    | „50%”, „fifty percent”|
| Liczba      | Liczba porządkowa       | „2nd”, „second”     |
| Liczba      | Wiek           | "90 dzień dawna", "30 lat starych"    |
| Liczba      | Waluta      | „$10,99”     |
| Liczba      | Wymiar     | „10 miles”, „40 cm”     |
| Liczba      | Temperatura   | „32 degrees”    |
| DateTime      | Nie dotyczy\*         | „6:30PM February 4, 2012”      |
| DateTime      | Date          | „May 2nd, 2017”, „05/02/2017”   |
| DateTime      | Time          | "8:00", "8:00"  |
| DateTime      | Zakres dat     | „May 2nd to May 5th”    |
| DateTime      | Zakres czasu     | „6pm to 7pm”     |
| DateTime      | Czas trwania      | „1 minute and 45 seconds”   |
| DateTime      | Set           | „every Tuesday”     |
| Adres URL           | Nie dotyczy\*         | "https:\//www.bing.com"    |
| Email         | Nie dotyczy\*         | „support@contoso.com” |

\* w zależności od danych wejściowych i wyodrębnionych, niektóre jednostki mogą pominąć `SubType`.  Wszystkie obsługiwane typy jednostek są dostępne tylko w językach angielskim, chińskim, francuskim, niemieckim i hiszpańskim.

### <a name="language-support"></a>Obsługa języków

Używanie łączenia jednostek w różnych językach wymaga użycia odpowiedniej bazy wiedzy w każdym z języków. W przypadku łączenia jednostek w analiza tekstu oznacza to, że każdy język, który jest obsługiwany przez punkt końcowy `entities`, zostanie połączony z odpowiednim korpus witryny Wikipedia w tym języku. Ponieważ rozmiar korpusy różni się między językami, oczekuje się, że odwołanie do funkcji łączenia jednostki również będzie się różnić. Aby uzyskać więcej informacji, zobacz artykuł [Obsługa języka](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) .

## <a name="preparation"></a>Przygotowywanie

Musisz mieć dokumenty JSON w tym formacie: ID, text, language

W przypadku obecnie obsługiwanych języków zapoznaj się z [tą listą](../text-analytics-supported-languages.md).

Dokument musi mieć mniej niż 5120 znaków, a kolekcja może zawierać maksymalnie 1000 elementów (identyfikatorów). Kolekcja jest przesyłana w treści żądania. Poniższy przykład jest ilustracją zawartości, którą można przesłać do obiektu łączącego.

```json
    {
        "documents": [
            {
                "id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
            },
            {
                "id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Krok 1: Określenie struktury żądania

Szczegółowe informacje na temat definicji żądania można znaleźć w artykule [Jak wywołać interfejs API analizy tekstu](text-analytics-how-to-call-api.md). Dla wygody poniżej ponownie podano odpowiednie kroki:

+ Utwórz żądanie **POST**. Przejrzyj dokumentację interfejsu API dla tego żądania: [interfejs API jednostek](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Ustaw punkt końcowy HTTP na potrzeby wyodrębniania fraz kluczowych przy użyciu zasobu analiza tekstu na platformie Azure lub [kontenera analiza tekstu](text-analytics-how-to-install-containers.md)wystąpienia. Musisz dołączyć `/text/analytics/v2.1/entities`. Na przykład: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`.

+ Ustaw nagłówek żądania w taki sposób [, aby zawierał klucz dostępu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dla operacji analiza tekstu.

+ W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

> [!Tip]
> Użyj programu [Postman](text-analytics-how-to-call-api.md) lub otwórz **konsolę testowania interfejsu API** w [dokumentacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634), aby określić strukturę żądania i przesłać je do usługi za pomocą operacji POST.

## <a name="step-2-post-the-request"></a>Krok 2: Wysłanie żądania

Analiza jest wykonywana po odebraniu żądania. Zapoznaj się z sekcją [limity danych](../overview.md#data-limits) w temacie Omówienie dotyczącej rozmiaru i liczby żądań wysyłanych na minutę i sekundę.

Pamiętaj, że usługa jest bezstanowa. Żadne dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.

## <a name="step-3-view-results"></a>Krok 3: Wyświetlenie wyników

Wszystkie żądania POST zwracają odpowiedź w formacie JSON z identyfikatorami i wykrytymi właściwościami.

Dane wyjściowe są zwracane natychmiast. Wyniki można przesłać strumieniowo do aplikacji, która akceptuje kod JSON, lub zapisać do pliku w systemie lokalnym, a następnie zaimportować do aplikacji, która umożliwia sortowanie i wyszukiwanie danych oraz manipulowanie nimi.

Przykład danych wyjściowych dla łączenia encji jest pokazany poniżej:

```json
    {
        "Documents": [
            {
                "Id": "1",
                "Entities": [
                    {
                        "Name": "Jeff",
                        "Matches": [
                            {
                                "Text": "Jeff",
                                "Offset": 0,
                                "Length": 4
                            }
                        ],
                        "Type": "Person"
                    },
                    {
                        "Name": "three dozen",
                        "Matches": [
                            {
                                "Text": "three dozen",
                                "Offset": 12,
                                "Length": 11
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50",
                        "Matches": [
                            {
                                "Text": "50",
                                "Offset": 49,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50%",
                        "Matches": [
                            {
                                "Text": "50%",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            },
            {
                "Id": "2",
                "Entities": [
                    {
                        "Name": "Great Depression",
                        "Matches": [
                            {
                                "Text": "The Great Depression",
                                "Offset": 0,
                                "Length": 20
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Great Depression",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                        "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                    },
                    {
                        "Name": "1929",
                        "Matches": [
                            {
                                "Text": "1929",
                                "Offset": 30,
                                "Length": 4
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "By 1933",
                        "Matches": [
                            {
                                "Text": "By 1933",
                                "Offset": 36,
                                "Length": 7
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "Gross domestic product",
                        "Matches": [
                            {
                                "Text": "GDP",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Gross domestic product",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                        "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                    },
                    {
                        "Name": "United States",
                        "Matches": [
                            {
                                "Text": "America",
                                "Offset": 56,
                                "Length": 7
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "United States",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                        "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                        "Type": "Location"
                    },
                    {
                        "Name": "25",
                        "Matches": [
                            {
                                "Text": "25",
                                "Offset": 72,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "25%",
                        "Matches": [
                            {
                                "Text": "25%",
                                "Offset": 72,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            }
        ],
        "Errors": []
    }
```

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy dotyczące łączenia jednostek przy użyciu analiza tekstu w Cognitive Services. Podsumowanie:

+ [Interfejs API jednostek](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) jest dostępny dla wybranych języków.
+ Dokumenty JSON w treści żądania obejmują identyfikator, tekst i kod języka.
+ Żądanie POST jest wysyłane do punktu końcowego `/entities` za pomocą spersonalizowanego [klucza dostępu i punktu końcowego](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) prawidłowego dla używanej subskrypcji.
+ Dane wyjściowe odpowiedzi, które obejmują połączone jednostki (w tym wyniki pewności, przesunięcia i linki sieci Web dla każdego identyfikatora dokumentu) mogą być używane w dowolnej aplikacji

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Interfejs API analizy tekstu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)
* [Często zadawane pytania](../text-analytics-resource-faq.md)</br>
* [Strona produktu analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759712)
