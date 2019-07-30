---
title: Korzystanie z rozpoznawania jednostek z interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać jednostki przy użyciu interfejsu API REST analiza tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 04/16/2019
ms.author: aahi
ms.openlocfilehash: 5a5787504d72259354f9c5eba2e2f4e22402ef0b
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619734"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Jak używać rozpoznawania jednostek nazwanych w analiza tekstu

[Interfejs API rozpoznawania jednostek nazwanych](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) Pobiera tekst bez struktury, a dla każdego dokumentu JSON zwraca listę niejednoznacznych jednostek z linkami do dodatkowych informacji na temat sieci Web (Wikipedia i Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Łączenie jednostek i rozpoznawanie nazwanych jednostek

Punkt końcowy analiza tekstu `entities` "obsługuje zarówno rozpoznawanie jednostek nazwanych (ner), jak i łączenie jednostek.

### <a name="entity-linking"></a>Łączenie jednostek
Łączenie jednostek to możliwość identyfikacji i odróżnienia tożsamości jednostki znalezionej w tekście (na przykład określenie, czy element "Mars" jest używany jako globalnej, czy jako Rzymskie akty Boże War). Ten proces wymaga obecności bazy wiedzy, do której są połączone powiązane jednostki — jest używana jako baza wiedzy dla `entities` punktu końcowego analiza tekstu.

### <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych (NER)
Funkcja rozpoznawania jednostek nazwanych (NER) to możliwość identyfikowania różnych jednostek w tekście i kategoryzowania ich w klasach wstępnie zdefiniowanych. Poniżej wymieniono obsługiwane klasy jednostek.

W analiza tekstu w [wersji 2,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634), zarówno konsolidacja jednostek, jak i nazwanego rozpoznawania jednostek (ner) są dostępne dla kilku języków. Aby uzyskać więcej informacji, zobacz artykuł [Obsługa języka](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) .

### <a name="language-support"></a>Obsługa języków

Używanie łączenia jednostek w różnych językach wymaga użycia odpowiedniej bazy wiedzy w każdym z języków. W przypadku łączenia jednostek w analiza tekstu oznacza to, że każdy język, który jest `entities` obsługiwany przez punkt końcowy, będzie łączyć się z odpowiednim korpus witryny Wikipedia w tym języku. Ponieważ rozmiar korpusy różni się między językami, oczekuje się, że odwołanie do funkcji łączenia jednostki również będzie się różnić.

## <a name="supported-types-for-named-entity-recognition"></a>Obsługiwane typy rozpoznawania jednostek nazwanych

| Type  | SubType | Przykład |
|:-----------   |:------------- |:---------|
| Person (Osoba)        | NIE DOTYCZY\*         | "Jan", "bramy Bill"     |
| Location      | NIE DOTYCZY\*         | „Redmond, Washington”, „Paris”  |
| Organizacja  | NIE DOTYCZY\*         | „Microsoft”   |
| Ilość      | Liczba        | „6”, „six”     |
| Ilość      | Wartość procentowa    | „50%”, „fifty percent”|
| Ilość      | Liczba porządkowa       | „2nd”, „second”     |
| Ilość      | Zakres liczb   | „4 to 8”     |
| Ilość      | Wiek           | "90 dzień dawna", "30 lat starych"    |
| Ilość      | Currency      | „$10,99”     |
| Ilość      | Wymiar     | „10 miles”, „40 cm”     |
| Ilość      | Temperatura   | „32 degrees”    |
| Datetime      | NIE DOTYCZY\*         | „6:30PM February 4, 2012”      |
| Datetime      | Date          | „May 2nd, 2017”, „05/02/2017”   |
| Datetime      | Time          | "8:00", "8:00"  |
| Datetime      | Zakres dat     | „May 2nd to May 5th”    |
| Datetime      | Zakres czasu     | „6pm to 7pm”     |
| Datetime      | Duration      | „1 minute and 45 seconds”   |
| Datetime      | Zestaw           | „every Tuesday”     |
| Datetime      | Strefa czasowa      |    |
| URL           | NIE DOTYCZY\*         | "https:\//www.Bing.com"    |
| Email         | NIE DOTYCZY\*         | "support@contoso.com" |

\*W zależności od danych wejściowych i wyodrębnionych niektóre jednostki mogą pominąć `SubType`.  Wszystkie obsługiwane typy jednostek są dostępne tylko w językach angielskim, chińskim, francuskim, niemieckim i hiszpańskim.



## <a name="preparation"></a>Przygotowanie

Musisz mieć dokumenty JSON w tym formacie: Identyfikator, tekst, język

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

+ Utwórz żądanie **POST**. Zapoznaj się z dokumentacją interfejsu API dla tego żądania: [Interfejs API łączenia jednostek](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Ustaw punkt końcowy HTTP na potrzeby wyodrębniania jednostek. Musi on obejmować zasób `/entities`: `https://[your-region].api.cognitive.microsoft.com/text/analytics/v2.1/entities`

+ Ustaw nagłówek żądania, tak aby zawierał klucz dostępu dla operacji analizy tekstu. Aby uzyskać więcej informacji, zobacz [How to find endpoints and access keys (Jak znajdować punkty końcowe i klucze dostępu)](text-analytics-how-to-access-key.md).

+ W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

> [!Tip]
> Użyj programu [Postman](text-analytics-how-to-call-api.md) lub otwórz **konsolę testowania interfejsu API** w [dokumentacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634), aby określić strukturę żądania i przesłać je do usługi za pomocą operacji POST.

## <a name="step-2-post-the-request"></a>Krok 2. Wysłanie żądania

Analiza jest wykonywana po odebraniu żądania. Zapoznaj się z sekcją [limity danych](../overview.md#data-limits) w temacie Omówienie dotyczącej rozmiaru i liczby żądań wysyłanych na minutę i sekundę.

Pamiętaj, że usługa jest bezstanowa. Żadne dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.

## <a name="step-3-view-results"></a>Krok 3. Wyświetlanie wyników

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
+ Żądanie POST jest wysyłane do punktu końcowego `/entities` za pomocą spersonalizowanego [klucza dostępu i punktu końcowego](text-analytics-how-to-access-key.md) prawidłowego dla używanej subskrypcji.
+ Dane wyjściowe odpowiedzi, które obejmują połączone jednostki (w tym wyniki pewności, przesunięcia i linki sieci Web dla każdego identyfikatora dokumentu) mogą być używane w dowolnej aplikacji

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Interfejs API analizy tekstu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)
* [Często zadawane pytania](../text-analytics-resource-faq.md)</br>
* [Strona produktu analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759712)
