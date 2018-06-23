---
title: Analiza porad wskaźniki nastrojów klientów w programie interfejsu API REST Analytics tekst (Microsoft kognitywnych usług Azure) | Dokumentacja firmy Microsoft
description: Jak wykryć przy użyciu interfejsu API REST analizy tekstu w kognitywnych usług Microsoft Azure w tym samouczku wskazówki wskaźniki nastrojów klientów.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 12/11/2017
ms.author: heidist
ms.openlocfilehash: 7ffd8bbe47409b459fdd308cd8d670d32f56649b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347441"
---
# <a name="how-to-detect-sentiment-in-text-analytics"></a>Jak wykryć wskaźniki nastrojów klientów w Analiza tekstu

[API analizy wskaźniki nastrojów klientów](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) ocenia wprowadzanie tekstu i zwraca wynik wskaźniki nastrojów klientów dla każdego dokumentu z zakresu od 0 (ujemne) do 1 (dodatni).

Ta funkcja jest przydatna do wykrywania mediów społecznościowych, recenzje klientów i forów dyskusyjnych dodatnie i ujemne wskaźniki nastrojów klientów. Zawartość jest dostarczana przez Ciebie; modele i dane szkoleniowe są dostarczane przez usługę.

Obecnie analizy wskaźniki nastrojów klientów obsługuje język angielski, niemiecki, hiszpański i francuski. Inne języki są w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [obsługiwanych języków](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Pojęcia

Analiza tekstu używa klasyfikacji Algorytm uczenia maszynowego można wygenerować wynik wskaźniki nastrojów klientów od 0 do 1. Wyniki bliższa 1, wskazują dodatnią wskaźniki nastrojów klientów, gdy wyniki bliższa 0 wskazuje ujemna wskaźniki nastrojów klientów. Model jest pretrained z dużą ilością treścią tekstu z skojarzenia wskaźniki nastrojów klientów. Obecnie nie jest możliwe zapewnienie danych szkoleniowych. Model korzysta z kombinacji technik podczas analizy tekstu, w tym tekst przetwarzania, analizy części z mowy umieszczania programu word i skojarzenia programu word. Aby uzyskać więcej informacji na temat algorytm zobacz [analiza wprowadzenie tekstu](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

Analiza wskaźniki nastrojów klientów odbywa się na cały dokument, w przeciwieństwie do wyodrębniania wskaźniki nastrojów klientów dla określonej jednostki w tekście. W praktyce istnieje tendencja do oceniania dokładność zwiększające gdy dokumenty zawiera jeden lub dwa zdania zamiast dużej bloku tekstu. W fazie oceny obiektywizmu modelu określa, czy jest celem dokumentu jako całość, czy zawiera wskaźniki nastrojów klientów. A dokumentu, który nie jest przeważnie celu ma postępu frazy wykrywania wskaźniki nastrojów klientów, co powoduje.50 wynik, z żadne dalsze przetwarzanie nie. W przypadku dokumentów kontynuowanie w potoku kolejną fazą generuje wynik powyżej lub poniżej.50, w zależności od stopnia wykryte w dokumencie wskaźniki nastrojów klientów.

## <a name="preparation"></a>Przygotowanie

Analizy wskaźniki nastrojów klientów zapewnia nadaj mu mniejsze fragmenty tekstu do pracy w wyniku wyższej jakości. To jest przeciwny ekstrakcji frazy klucza, który działa lepiej na większych bloków tekstu. Aby uzyskać najlepsze wyniki z operacjami, należy wziąć pod uwagę odpowiednio restrukturyzacji danych wejściowych.

Musi mieć dokumentów JSON w następującym formacie: id, tekstu, język

Rozmiar dokumentu musi być w obszarze 5000 znaków na dokument, i może zawierać maksymalnie 1000 elementów (ID) w jednej kolekcji. Kolekcja jest przesyłany w treści żądania. Oto przykład zawartości, które mogą przesyłać do analizy wskaźniki nastrojów klientów.

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

+ Utwórz **POST** żądania. Zapoznaj się z dokumentacją interfejsu API dla tego żądania: [API analizy wskaźniki nastrojów klientów](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9)

+ Ustaw punkt końcowy HTTP w celu wyodrębnienia hasło klucza. Musi on zawierać `/sentiment` zasobów: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`

+ Ustaw nagłówek żądania, aby uwzględnić klucza dostępu dla operacji analizy tekstu. Aby uzyskać więcej informacji, zobacz [znajdowanie punktów końcowych i klucze dostępu](text-analytics-how-to-access-key.md).

+ W treści żądania Podaj kolekcji dokumentów JSON, które zostały przygotowane na potrzeby tej analizy.

> [!Tip]
> Użyj [Postman](text-analytics-how-to-call-api.md) lub Otwórz **konsoli testowania interfejsu API** w [dokumentacji](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) struktury żądania i OPUBLIKUJ go do usługi.

## <a name="step-2-post-the-request"></a>Krok 2: Żądanie Post

Analiza jest przeprowadzana po otrzymaniu żądania. Usługa akceptuje maksymalnie 100 żądań na minutę. Każde żądanie może zawierać maksymalnie 1 MB.

Odwołaj, że usługa jest bezstanowe. Dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.


## <a name="step-3-view-results"></a>Krok 3: Wyświetlanie wyników

Analizator wskaźniki nastrojów klientów klasyfikuje tekst zawiera głównie dodatnie lub ujemne, przypisując z zakresu od 0 do 1. Wartości bliski 0,5 są neutralne lub nieokreślony. Wynik 0,5 oznacza nienaruszalności. Ciąg nie może być przeanalizowane pod kątem wskaźniki nastrojów klientów lub ma nie wskaźniki nastrojów klientów, wynik jest zawsze 0,5 dokładnie. Na przykład w przypadku przekazania w hiszpańskim ciąg z kodem języka angielskiego, wynik wynosi 0,5.

Dane wyjściowe są zwracane natychmiast. Można strumienia wyniki do aplikacji, która akceptuje JSON lub zapisać dane wyjściowe do pliku w systemie lokalnym, a następnie zaimportuj go do aplikacji, która pozwala na sortowanie, wyszukiwanie i manipulowanie danymi.

Poniższy przykład przedstawia odpowiedzi dla kolekcji dokumentów, w tym artykule.

```
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

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy do korzystania z analizy tekstu w usługach kognitywnych analizy wskaźniki nastrojów klientów. Podsumowując:

+ [Wskaźniki nastrojów klientów analizy interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) jest dostępna w wybranych językach.
+ Dokumenty JSON w treści żądania obejmują identyfikator, tekst i języka kodu.
+ Wysłanie żądania POST jest `/sentiment` punktu końcowego, za pomocą spersonalizowanych [dostępu do klucza i punkt końcowy](text-analytics-how-to-access-key.md) obowiązuje dla Twojej subskrypcji.
+ Dane wyjściowe odpowiedzi, która składa się z wynikiem wskaźniki nastrojów klientów dla każdego Identyfikatora dokumentu, można przesłać strumieniowo do wszystkich aplikacji, który akceptuje JSON, łącznie z programu Excel i usługi Power BI kilka.

## <a name="see-also"></a>Zobacz także 

 [Omówienie Analiza tekstu](../overview.md)  
 [Często zadawane pytania (FAQ)](../text-analytics-resource-faq.md)</br>
 [Strona produktu Analiza tekstu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wyodrębnij fraz klucza](text-analytics-how-to-keyword-extraction.md)
