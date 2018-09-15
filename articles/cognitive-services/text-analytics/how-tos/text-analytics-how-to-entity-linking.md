---
title: Użyj usługi entity linking przy użyciu interfejsu API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak do identyfikowania i usuwania jednostek przy użyciu interfejsu API REST analizy tekstu.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 09/12/2018
ms.author: ashmaka
ms.openlocfilehash: ad2168806f9ddd124faf66cdb5a0f51ed13dfadc
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45604743"
---
# <a name="how-to-identify-linked-entities-in-text-analytics-preview"></a>Jak zidentyfikować połączonej jednostki w analizy tekstu (wersja zapoznawcza)

[Interfejs API usługi Entity Linking](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) przyjmuje tekstu bez struktury i dla każdego dokumentu JSON zwraca listę jednostek rozróżniane wraz z łączami do szczegółowych informacji w sieci web (Wikipedia i Bing). 

## <a name="entity-linking-vs-named-entity-recognition"></a>Vs łączenie jednostek. Rozpoznawanie jednostek znaku

Podczas przetwarzania języka naturalnego, można łatwo pomylić pojęcia łączenie podmiotów i rozpoznawanie jednostek znaku (NER). W wersji zapoznawczej analizy tekstu `entities` punkt końcowy, tylko usługi entity linking jest obsługiwany.

Łączenie jednostek jest możliwość identyfikowania i odróżnić tożsamość jednostki w tekście (np. określająca, czy "Mars" jest używana jako planety lub Roman Boże z war). Ten proces wymaga obecności wiedzy, do którego został rozpoznany jednostki są połączone — Wikipedia jest używany jako knowledge base, aby `entities` punktu końcowego analizy tekstu.

### <a name="language-support"></a>Obsługa języków

Korzystanie z usługi entity linking w różnych językach wymaga, przy użyciu odpowiedniej wiedzy w każdym języku. W przypadku usługi entity linking w analizy tekstu, oznacza to każdy język, który jest obsługiwany przez `entities` punktu końcowego połączy się z odpowiedniego korpus Wikipedia, w tym języku. Ponieważ rozmiar korpusy różni się między językami oczekuje się, czy usługi entity linking odwołań w funkcji również będą się różnić.


## <a name="preparation"></a>Przygotowanie

Dokumenty JSON muszą mieć następujący format: id, tekstu, język

Obecnie obsługiwane języki, zobacz [tej listy](../text-analytics-supported-languages.md).

Rozmiar dokumentu musi być w obszarze 5000 znaków na dokument, i może mieć maksymalnie 1000 elementów (ID) na kolekcję. Kolekcja jest przesyłany w treści żądania. Poniższy przykład jest ilustrację zawartość, którą może przesłać w celu łączenia jednostek.

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

Szczegółowe informacje na temat definicji żądania można znaleźć w [sposób wywołania interfejsu API analizy tekstu](text-analytics-how-to-call-api.md). Dla wygody są przekształcane, następujące kwestie:

+ Tworzenie **WPIS** żądania. Zapoznaj się z dokumentacją interfejsu API dla tego żądania: [interfejs API usługi Entity Linking](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Ustaw punkt końcowy HTTP dla wyodrębnianie kluczowych fraz. Musi on zawierać `/entities` zasobów: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/entities`

+ Ustaw nagłówek żądania, aby uwzględnić klucz dostępu do operacji analizy tekstu. Aby uzyskać więcej informacji, zobacz [znajdowanie punktów końcowych i klucze dostępu](text-analytics-how-to-access-key.md).

+ Treść żądania zawiera kolekcję dokumentów JSON, którą przygotowane na potrzeby tej analizy

> [!Tip]
> Użyj [Postman](text-analytics-how-to-call-api.md) lub Otwórz **konsoli testowania interfejsu API** w [dokumentacji](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) struktury żądania i OPUBLIKUJ go w usłudze.

## <a name="step-2-post-the-request"></a>Krok 2: Wpis żądania

Analiza jest wykonywana po otrzymaniu żądania. Usługa akceptuje maksymalnie 100 żądań na minutę. Każde żądanie może zawierać maksymalnie 1 MB.

Pamiętaj, że usługa jest bezstanowy. Nie dane są przechowywane na koncie. Wyniki są natychmiast zwracane w odpowiedzi.

## <a name="step-3-view-results"></a>Krok 3: Wyświetlanie wyników

Wszystkie żądania POST Zwróć JSON sformatowany odpowiedzi z identyfikatorami i wykryto właściwości.

Dane wyjściowe są natychmiast zwracane. Można strumienia wyniki do aplikacji, która akceptuje JSON lub Zapisz dane wyjściowe do pliku w systemie lokalnym, a następnie zaimportować go do aplikacji, która pozwala na sortowanie, wyszukiwanie i manipulowania danymi.

Przykład danych wyjściowych do usługi entity linking pokazano dalej:

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

Jeśli są dostępne, odpowiedź zawiera identyfikator Wikipedia Wikipedia adresu URL i identyfikator Bing dla każdej jednostki wykryte. Mogą one używane w celu dodatkowego zwiększenia aplikacji przy użyciu informacji dotyczących połączonej jednostki.


## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy dotyczący łączenie podmiotów, za pomocą analizy tekstu w usługach Cognitive Services. Podsumowanie:

+ [Interfejs API usługi Entity Linking](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) jest dostępny dla wybranych języków.
+ Dokumenty JSON w treści żądania obejmują identyfikator, tekstu i języka kodu.
+ Żądanie POST jest `/entities` punktu końcowego, za pomocą spersonalizowanego [dostępu key i punktu końcowego](text-analytics-how-to-access-key.md) , który jest poprawny dla Twojej subskrypcji.
+ Dane wyjściowe odpowiedzi, który składa się z połączonej jednostki (w tym pewność, że wyniki, przesunięcia i linków sieci web, dla każdego dokumentu Identyfikator) mogą być używane w dowolnej aplikacji

## <a name="see-also"></a>Zobacz także 

 [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)</br>
 [Strona produktu analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Interfejs API analizy tekstu](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
