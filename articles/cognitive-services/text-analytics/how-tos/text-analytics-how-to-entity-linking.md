---
title: Rozpoznawanie jednostek za pomocą interfejsu API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznać jednostki przy użyciu interfejsu API REST analizy tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: dfbb31ce9f61ee28fef046120474a6a170906512
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59505579"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics-preview"></a>Jak używać o nazwie rozpoznawania jednostek w analizy tekstu (wersja zapoznawcza)

[Interfejs API rozpoznawania jednostek](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) przyjmuje tekstu bez struktury i dla każdego dokumentu JSON zwraca listę jednostek rozróżniane wraz z łączami do szczegółowych informacji w sieci web (Wikipedia i Bing). 

## <a name="entity-linking-and-named-entity-recognition"></a>Łączenie podmiotów i rozpoznawanie jednostek znaku

Text Analytics `entities` obsługuje punktu końcowego o nazwie rozpoznawanie jednostek (NER) i usługi entity linking.

### <a name="entity-linking"></a>Łączenie jednostek
Łączenie jednostek jest możliwość identyfikowania i odróżnić tożsamość jednostki w tekście (na przykład określenie, czy "Mars" jest używana jako planety lub Roman Boże z war). Ten proces wymaga obecności wiedzy, do którego został rozpoznany jednostki są połączone — Wikipedia jest używany jako knowledge base, aby `entities` punktu końcowego analizy tekstu.

W analizy tekstu [w wersji 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)tylko łączenie podmiotów jest dostępna.

### <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek znaku (NER)
O nazwie rozpoznawanie jednostek (NER) to zdolność do identyfikacji różnych obiektów w tekście i kategoryzowanie je do wstępnie zdefiniowanych klas. Poniżej wymieniono obsługiwane klas jednostek.

W analizy tekstu [wersji 2.1-Preview](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634), łączenie podmiotów i rozpoznawanie jednostek znaku (NER) są dostępne.

### <a name="language-support"></a>Obsługa języków

Korzystanie z usługi entity linking w różnych językach wymaga, przy użyciu odpowiedniej wiedzy w każdym języku. W przypadku usługi entity linking w analizy tekstu, oznacza to każdy język, który jest obsługiwany przez `entities` punktu końcowego połączy się z odpowiedniego korpus Wikipedia, w tym języku. Ponieważ rozmiar korpusy różni się między językami oczekuje się, czy usługi entity linking odwołań w funkcji również będą się różnić.

## <a name="supported-types-for-named-entity-recognition"></a>Obsługiwane typy dla rozpoznawanie jednostek znaku

| Typ  | SubType | Przykład |
|:-----------   |:------------- |:---------|
| Person (Osoba)        | N/D\*         | "Jan", "Billa Gatesa"     |
| Lokalizacja      | N/D\*         | "Redmond w stanie Waszyngton", "Paryż"  |
| Organizacja  | N/D\*         | "Microsoft"   |
| Ilość      | Liczba        | "6", "6"     | 
| Ilość      | Wartość procentowa    | "50%", "percent pięćdziesiąt"| 
| Ilość      | Liczba porządkowa       | "2", "drugi"     | 
| Ilość      | NumberRange   | "od 4 do 8"     | 
| Ilość      | Wiek           | "90 dni temu" lub "30 lat"    | 
| Ilość      | Waluta      | "$10.99"     | 
| Ilość      | Wymiar     | "10 mil", "40 cm"     | 
| Ilość      | Temperatura   | "stopni 32"    |
| DateTime      | N/D\*         | "6:30 PM 4 lutego 2012"      | 
| DateTime      | Date          | "2nd maja 2017", "2017-05-02"   | 
| DateTime      | Time          | "8: 00", "8:00"  | 
| DateTime      | DateRange     | "Maja 2 do 5 maja."    | 
| DateTime      | TimeRange     | "18: 00 – 19: 00"     | 
| DateTime      | Czas trwania      | "1 minutę i 45 sekund"   | 
| DateTime      | Set           | "każdy wtorek"     | 
| DateTime      | Strefa czasowa      |    | 
| Adres URL           | N/D\*         | "https:\//www.bing.com"    |
| Email         | N/D\*         | „support@contoso.com” |

\* W zależności od jednostki danych wejściowych i wyodrębnione może pominąć niektóre jednostki `SubType`.



## <a name="preparation"></a>Przygotowanie

Dokumenty JSON muszą mieć następujący format: identyfikator, tekst, język

Dla aktualnie obsługiwanych języków, zobacz [tej listy](../text-analytics-supported-languages.md).

Dokument musi mieć mniej niż 5120 znaków, a kolekcja może zawierać maksymalnie 1000 elementów (identyfikatorów). Kolekcja jest przesyłana w treści żądania. Poniższy przykład jest ilustrację zawartość, którą może przesłać w celu łączenia jednostek.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
                },
               {"id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>Krok 1: Określenie struktury żądania

Szczegółowe informacje na temat definicji żądania można znaleźć w artykule [Jak wywołać interfejs API analizy tekstu](text-analytics-how-to-call-api.md). Dla wygody poniżej ponownie podano odpowiednie kroki:

+ Utwórz żądanie **POST**. Zapoznaj się z dokumentacją interfejsu API dla tego żądania: [Interfejs API usługi Entity Linking](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Ustaw punkt końcowy HTTP dla działania funkcji wydobywania podmiotów. Musi on obejmować zasób `/entities`: `https://[your-region].api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities`

+ Ustaw nagłówek żądania, tak aby zawierał klucz dostępu dla operacji analizy tekstu. Aby uzyskać więcej informacji, zobacz [How to find endpoints and access keys (Jak znajdować punkty końcowe i klucze dostępu)](text-analytics-how-to-access-key.md).

+ W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

> [!Tip]
> Użyj programu [Postman](text-analytics-how-to-call-api.md) lub otwórz **konsolę testowania interfejsu API** w [dokumentacji](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634), aby określić strukturę żądania i przesłać je do usługi za pomocą operacji POST.

## <a name="step-2-post-the-request"></a>Krok 2. Wysłanie żądania

Analiza jest wykonywana po odebraniu żądania. Usługa akceptuje maksymalnie 100 żądań na drugim i 1000 żądań na minutę. Maksymalny rozmiar każdego żądania to 1 MB.

Pamiętaj, że usługa jest bezstanowa. Żadne dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.

## <a name="step-3-view-results"></a>Krok 3. Wyświetlanie wyników

Wszystkie żądania POST zwracają odpowiedź w formacie JSON z identyfikatorami i wykrytymi właściwościami.

Dane wyjściowe są zwracane natychmiast. Wyniki można przesłać strumieniowo do aplikacji, która akceptuje kod JSON, lub zapisać do pliku w systemie lokalnym, a następnie zaimportować do aplikacji, która umożliwia sortowanie i wyszukiwanie danych oraz manipulowanie nimi.

Przykład danych wyjściowych do usługi entity linking pokazano dalej:

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

W tym artykule przedstawiono pojęcia i przepływ pracy dotyczący łączenie podmiotów, za pomocą analizy tekstu w usługach Cognitive Services. Podsumowanie:

+ [Jednostki interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) jest dostępny dla wybranych języków.
+ Dokumenty JSON zawierają w treści żądania identyfikator, tekst i kod języka.
+ Żądanie POST jest wysyłane do punktu końcowego `/entities` za pomocą spersonalizowanego [klucza dostępu i punktu końcowego](text-analytics-how-to-access-key.md) prawidłowego dla używanej subskrypcji.
+ Dane wyjściowe odpowiedzi, który składa się z połączonej jednostki (w tym pewność, że wyniki, przesunięcia i linków sieci web, dla każdego dokumentu Identyfikator) mogą być używane w dowolnej aplikacji

## <a name="see-also"></a>Zobacz także 

 [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)</br>
 [Strona produktu analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Interfejs API analizy tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)
