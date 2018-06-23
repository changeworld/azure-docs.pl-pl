---
title: Wyodrębniania porad frazy klucza w interfejsu API REST Analytics tekst (Microsoft kognitywnych usług Azure) | Dokumentacja firmy Microsoft
description: W jaki sposób wyodrębnić klucza fraz przy użyciu interfejsu API REST analizy tekstu w kognitywnych usług Microsoft Azure w tym samouczku wskazówki.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: 78b100e737242fa9f56e50275ef2038d8895349e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347440"
---
# <a name="how-to-extract-key-phrases-in-text-analytics"></a>Jak wyodrębnić klucza fraz w Analiza tekstu

[Klucza frazy wyodrębniania API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) ocenia niestrukturalnych tekstu, a następnie dla każdego dokumentu JSON zwraca listę kluczy fraz. 

Ta funkcja jest przydatna, jeśli chcesz szybko zidentyfikować głównych punktów w kolekcji dokumentów. Na przykład dany tekst wejściowy "żywności został wyborna i wystąpiły cudowne personelu", usługa zwraca głównych punktach wystąpienia: "żywności" i "cudowne pracownicy".

Obecnie wyodrębniania frazy klucza obsługuje język angielski, niemiecki, hiszpański i japoński. Inne języki są w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [obsługiwanych języków](../text-analytics-supported-languages.md).

## <a name="preparation"></a>Przygotowanie

Hasło klucza wyodrębniania działa najlepiej, gdy zapewniają większą liczbę fragmentów tekstu do pracy w. To jest przeciwny z analizy wskaźniki nastrojów klientów, który działa lepiej na mniejsze fragmenty tekstu. Aby uzyskać najlepsze wyniki z operacjami, należy wziąć pod uwagę odpowiednio restrukturyzacji danych wejściowych.

Musi mieć dokumentów JSON w następującym formacie: id, tekstu, język

Rozmiar dokumentu musi być w obszarze 5000 znaków na dokument, i może zawierać maksymalnie 1000 elementów (ID) w jednej kolekcji. Kolekcja jest przesyłany w treści żądania. Poniższy przykład jest ilustracja zawartości, która może być wprowadzane do wyodrębniania hasło klucza.

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
    
## <a name="step-1-structure-the-request"></a>Krok 1: Struktury żądania

Szczegółowe informacje o definicji żądania można znaleźć w [sposób wywołania interfejsu API z analizy tekstu](text-analytics-how-to-call-api.md). Dla wygody są przekształcane, następujące kwestie:

+ Utwórz **POST** żądania. Zapoznaj się z dokumentacją interfejsu API dla tego żądania: [API fraz klucza](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

+ Ustaw punkt końcowy HTTP w celu wyodrębnienia hasło klucza. Musi on zawierać `/keyphrases` zasobów: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`

+ Ustaw nagłówek żądania, aby uwzględnić klucza dostępu dla operacji analizy tekstu. Aby uzyskać więcej informacji, zobacz [znajdowanie punktów końcowych i klucze dostępu](text-analytics-how-to-access-key.md).

+ W treści żądania Podaj kolekcji dokumentów JSON, które zostały przygotowane na potrzeby tej analizy

> [!Tip]
> Użyj [Postman](text-analytics-how-to-call-api.md) lub Otwórz **konsoli testowania interfejsu API** w [dokumentacji](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) struktury żądanie i OPUBLIKUJ go do usługi.

## <a name="step-2-post-the-request"></a>Krok 2: Żądanie Post

Analiza jest przeprowadzana po otrzymaniu żądania. Usługa akceptuje maksymalnie 100 żądań na minutę. Każde żądanie może zawierać maksymalnie 1 MB.

Odwołaj, że usługa jest bezstanowe. Dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.

## <a name="step-3-view-results"></a>Krok 3: Wyświetlanie wyników

Wszystkie żądania POST zwracać JSON sformatowany odpowiedzi przy użyciu identyfikatorów i wykryto właściwości.

Dane wyjściowe są zwracane natychmiast. Można strumienia wyniki do aplikacji, która akceptuje JSON lub zapisać dane wyjściowe do pliku w systemie lokalnym, a następnie zaimportuj go do aplikacji, która pozwala na sortowanie, wyszukiwanie i manipulowanie danymi.

Przykład danych wyjściowych dla wyodrębniania klucza frazy przedstawiono dalej:

```
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
```

Jak wspomniano, analizatora znajduje i odrzuca nieistotne słów i przechowuje pojedyncze terminy lub wyrażeń, które wydają się temacie lub obiekt zdania. 

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy dotyczący korzystania z analizy tekstu w usługach kognitywnych wyodrębniania hasło klucza. Podsumowując:

+ [Klucz frazy wyodrębniania interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) jest dostępna w wybranych językach.
+ Dokumenty JSON w treści żądania obejmują identyfikator, tekst i języka kodu.
+ Wysłanie żądania POST jest `/keyphrases` punktu końcowego, za pomocą spersonalizowanych [dostępu do klucza i punkt końcowy](text-analytics-how-to-access-key.md) obowiązuje dla Twojej subskrypcji.
+ Dane wyjściowe odpowiedzi, która składa się z słów kluczowych i wyrażenia dla każdego Identyfikatora dokumentu, można przesłać strumieniowo do wszystkich aplikacji, który akceptuje JSON, łącznie z programu Excel i usługi Power BI kilka.

## <a name="see-also"></a>Zobacz także 

 [Omówienie Analiza tekstu](../overview.md)  
 [Często zadawane pytania (FAQ)](../text-analytics-resource-faq.md)</br>
 [Strona produktu Analiza tekstu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu interfejsu API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
