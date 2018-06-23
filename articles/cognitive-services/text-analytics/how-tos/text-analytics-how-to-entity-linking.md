---
title: Opisy wykonywania określonych zadań użyj łączenie jednostki w interfejsu API REST Analytics tekst (Microsoft kognitywnych usług Azure) | Dokumentacja firmy Microsoft
description: Jak zidentyfikować i rozwiązać przy użyciu interfejsu API REST analizy tekstu w kognitywnych usług Microsoft Azure w tym samouczku wskazówki jednostek.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: 55bec1a0223b70749a97a30e2da92ef15128038c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347760"
---
# <a name="how-to-identify-linked-entities-in-text-analytics-preview"></a>Jak zidentyfikować połączonych jednostek analiza tekstu (wersja zapoznawcza)

[API łączenie jednostki](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) przyjmuje tekstu bez struktury i dla każdego dokumentu JSON zwraca listę rozróżniane jednostki z łącza do dodatkowych informacji w sieci web (Wikipedia i Bing). 

## <a name="entity-linking-vs-named-entity-recognition"></a>Vs konsolidację jednostki. Rozpoznawanie jednostek znaku

Podczas przetwarzania języka naturalnego, można łatwo pomylić pojęcia dotyczące łączenia jednostki i rozpoznawania nazwanej jednostki (NER). W wersji zapoznawczej Analiza tekstu `entities` punktu końcowego, tylko typy jednostek konsolidacji jest obsługiwana.

Jednostka konsolidacji jest możliwość identyfikowania i odróżnienia tożsamości podmiotu w tekście (np. Określanie czy "Mars" jest używana jako świecie lub łacińskich wyższej z war). Ten proces wymaga obecności podstawowej, która rozpoznane jednostki są połączone — Wikipedia jest używany jako w bazie wiedzy knowledge `entities` punktu końcowego Analiza tekstu.

### <a name="language-support"></a>Obsługa języków

Przy użyciu jednostek konsolidacji w różnych językach wymaga przy użyciu odpowiedniej wiedzy w każdym z tych języków. Dla obiektu konsolidacji Analiza tekstu, oznacza to każdego języka, który jest obsługiwany przez `entities` punktu końcowego połączy się z odpowiedniego Boże Wikipedia, w tym języku. Ponieważ rozmiar ciałek różnie w przypadku języków, oczekuje się również zależy jednostki łączenie odwołań w funkcji.


## <a name="preparation"></a>Przygotowanie

Musi mieć dokumentów JSON w następującym formacie: id, tekstu, język

Aktualnie obsługiwanych języków, zobacz [tej listy](../text-analytics-supported-languages.md).

Rozmiar dokumentu musi być w obszarze 5000 znaków na dokument, i może zawierać maksymalnie 1000 elementów (ID) w jednej kolekcji. Kolekcja jest przesyłany w treści żądania. Poniższy przykład jest ilustrację zawartość, którą może przesłać w celu łączenia jednostki.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                },
               {"id": "2",
                "language": "en",
                "text": "The Seattle Seahawks won the Super Bowl in 2014."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>Krok 1: Struktury żądania

Szczegółowe informacje o definicji żądania można znaleźć w [sposób wywołania interfejsu API z analizy tekstu](text-analytics-how-to-call-api.md). Dla wygody są przekształcane, następujące kwestie:

+ Utwórz **POST** żądania. Zapoznaj się z dokumentacją interfejsu API dla tego żądania: [API łączenie jednostki](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Ustaw punkt końcowy HTTP w celu wyodrębnienia hasło klucza. Musi on zawierać `/entities` zasobów: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/entities`

+ Ustaw nagłówek żądania, aby uwzględnić klucza dostępu dla operacji analizy tekstu. Aby uzyskać więcej informacji, zobacz [znajdowanie punktów końcowych i klucze dostępu](text-analytics-how-to-access-key.md).

+ W treści żądania Podaj kolekcji dokumentów JSON, które zostały przygotowane na potrzeby tej analizy

> [!Tip]
> Użyj [Postman](text-analytics-how-to-call-api.md) lub Otwórz **konsoli testowania interfejsu API** w [dokumentacji](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) struktury żądanie i OPUBLIKUJ go do usługi.

## <a name="step-2-post-the-request"></a>Krok 2: Żądanie Post

Analiza jest przeprowadzana po otrzymaniu żądania. Usługa akceptuje maksymalnie 100 żądań na minutę. Każde żądanie może zawierać maksymalnie 1 MB.

Odwołaj, że usługa jest bezstanowe. Dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.

## <a name="step-3-view-results"></a>Krok 3: Wyświetlanie wyników

Wszystkie żądania POST zwracać JSON sformatowany odpowiedzi przy użyciu identyfikatorów i wykryto właściwości.

Dane wyjściowe są zwracane natychmiast. Można strumienia wyniki do aplikacji, która akceptuje JSON lub zapisać dane wyjściowe do pliku w systemie lokalnym, a następnie zaimportuj go do aplikacji, która pozwala na sortowanie, wyszukiwanie i manipulowanie danymi.

Przykład danych wyjściowych dla jednostki łączenie przedstawiono dalej:

```
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "Ultra-high-definition television",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Ultra-high-definition television",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                    "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "2013 Seattle Seahawks season",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "2013 Seattle Seahawks season",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                    "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                }
            ]
        }
    ],
    "errors": []
}
```

Jeśli jest dostępna, odpowiedź zawiera identyfikator Wikipedia Wikipedia adresu URL i identyfikator Bing dla każdego wykrytego jednostki. Mogą one być używane do zwiększyć aplikacji z informacjami dotyczącymi połączonego obiektu.


## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy dla jednostki łączenie korzystania z analizy tekstu w usługach kognitywnych. Podsumowując:

+ [Jednostki łączenie API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) jest dostępna w wybranych językach.
+ Dokumenty JSON w treści żądania obejmują identyfikator, tekst i języka kodu.
+ Wysłanie żądania POST jest `/entities` punktu końcowego, za pomocą spersonalizowanych [dostępu do klucza i punkt końcowy](text-analytics-how-to-access-key.md) obowiązuje dla Twojej subskrypcji.
+ Dane wyjściowe odpowiedzi, która składa się z połączonych obiektów (w tym pewność, że wyniki, przesunięcia i linki sieci web, dla każdego dokumentu ID) mogą być używane w dowolnej aplikacji

## <a name="see-also"></a>Zobacz także 

 [Omówienie Analiza tekstu](../overview.md)  
 [Często zadawane pytania (FAQ)](../text-analytics-resource-faq.md)</br>
 [Strona produktu Analiza tekstu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu interfejsu API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
