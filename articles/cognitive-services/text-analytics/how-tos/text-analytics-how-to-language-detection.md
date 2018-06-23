---
title: Wykrywanie języka porad w interfejsu API REST Analytics tekst (Microsoft kognitywnych usług Azure) | Dokumentacja firmy Microsoft
description: Jak wykryć języka przy użyciu interfejsu API REST analizy tekstu w kognitywnych usług Microsoft Azure w tym samouczku wskazówki.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: f8e2d9a36533c298addcf42d3cb2061e9c2d1ac7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347437"
---
# <a name="how-to-detect-language-in-text-analytics"></a>Jak wykryć języka w Analiza tekstu

[Interfejs API języka wykrywania](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) ocenia tekstu wejściowego i dla każdego dokumentu i zwraca identyfikatorów języka z wynikiem wskazujący siły analizy. Analiza tekstu rozpoznaje języki do 120.

Ta funkcja jest przydatna w przypadku zawartości przechowują zbieranie dowolnego tekstu, gdy język jest nieznany. Wyniki analizy ustalenie język, który jest używany w dokument wejściowy może przeanalizować składni. Odpowiedź również zwraca wynik, który odzwierciedla zaufania modelu (wartość z zakresu od 0 i 1).

## <a name="preparation"></a>Przygotowanie

Musi mieć dokumentów JSON w następującym formacie: id, tekst

Rozmiar dokumentu musi być w obszarze 5000 znaków na dokument, i może zawierać maksymalnie 1000 elementów (ID) w jednej kolekcji. Kolekcja jest przesyłany w treści żądania. Oto przykład zawartości, które mogą przesyłać do wykrywania języka.

   ```
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },                
            {
                "id": "5",
                "text": "Этот документ находится на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Krok 1: Struktury żądania

Szczegółowe informacje o definicji żądania można znaleźć w [sposób wywołania interfejsu API z analizy tekstu](text-analytics-how-to-call-api.md). Dla wygody są przekształcane, następujące kwestie:

+ Utwórz **POST** żądania. Zapoznaj się z dokumentacją interfejsu API dla tego żądania: [interfejs API języka wykrywania](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)

+ Ustaw punkt końcowy HTTP do wykrywania języka. Musi on zawierać `/languages` zasobów: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`

+ Ustaw nagłówek żądania, aby uwzględnić klucza dostępu dla operacji analizy tekstu. Aby uzyskać więcej informacji, zobacz [znajdowanie punktów końcowych i klucze dostępu](text-analytics-how-to-access-key.md).

+ W treści żądania Podaj kolekcji dokumentów JSON, które zostały przygotowane na potrzeby tej analizy

> [!Tip]
> Użyj [Postman](text-analytics-how-to-call-api.md) lub Otwórz **konsoli testowania interfejsu API** w [dokumentacji](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) struktury żądanie i OPUBLIKUJ go do usługi.

## <a name="step-2-post-the-request"></a>Krok 2: Żądanie Post

Analiza jest przeprowadzana po otrzymaniu żądania. Usługa akceptuje maksymalnie 100 żądań na minutę. Każde żądanie może zawierać maksymalnie 1 MB.

Odwołaj, że usługa jest bezstanowe. Dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.


## <a name="step-3-view-results"></a>Krok 3: Wyświetlanie wyników

Wszystkie żądania POST zwracać JSON sformatowany odpowiedzi przy użyciu identyfikatorów i wykryto właściwości.

Dane wyjściowe są zwracane natychmiast. Można strumienia wyniki do aplikacji, która akceptuje JSON lub zapisać dane wyjściowe do pliku w systemie lokalnym, a następnie zaimportuj go do aplikacji, która pozwala na sortowanie, wyszukiwanie i manipulowanie danymi.

Wyniki dla żądania przykład powinien wyglądać następującego formatu JSON. Zwróć uwagę, że jest jeden dokument z wieloma elementami. Dane wyjściowe są w języku angielskim. Identyfikatory języka obejmują przyjazną nazwę i kod języka w [ISO 639 1](https://www.iso.org/standard/22109.html) format.

Wynik dodatnią 1.0 wyraża najwyższy poziom ufności możliwe analizy.



```
{
    "documents": [
        {
            "id": "1",
            "detectedLanguages": [
                {
                    "name": "English",
                    "iso6391Name": "en",
                    "score": 1
                }
            ]
        },
        {
            "id": "2",
            "detectedLanguages": [
                {
                    "name": "Spanish",
                    "iso6391Name": "es",
                    "score": 1
                }
            ]
        },
        {
            "id": "3",
            "detectedLanguages": [
                {
                    "name": "French",
                    "iso6391Name": "fr",
                    "score": 1
                }
            ]
        },
        {
            "id": "4",
            "detectedLanguages": [
                {
                    "name": "Chinese_Simplified",
                    "iso6391Name": "zh_chs",
                    "score": 1
                }
            ]
        },
        {
            "id": "5",
            "detectedLanguages": [
                {
                    "name": "Russian",
                    "iso6391Name": "ru",
                    "score": 1
                }
            ]
        }
    ],
```

### <a name="ambiguous-content"></a>Niejednoznaczne zawartości

Jeśli analizatora nie można przeanalizować danych wejściowych (na przykład przypuśćmy przesłane bloku tekstu składające się wyłącznie z Arabskie), zwraca `(Unknown)`.

```
    {
      "id": "5",
      "detectedLanguages": [
        {
          "name": "(Unknown)",
          "iso6391Name": "(Unknown)",
          "score": "NaN"
        }
      ]
```
### <a name="mixed-language-content"></a>Mieszanym językiem zawartości

Mieszanym językiem zawartości w tym samym dokumencie zwraca język z największych reprezentacja w zawartości, ale z niższym klasyfikacją dodatnią, odzwierciedlający brzegowego siły tej oceny. W poniższym przykładzie danych wejściowych jest blend angielski, hiszpański i francuski. Analizatora liczby znaków w każdym segmencie można ustalić języka dominujący.

**Dane wejściowe**

```
{
  "documents": [
    {
      "id": "1",
      "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
    }
  ]
}
```

**Dane wyjściowe**

Dane wyjściowe składa się z języka dominujący z wynikiem mniej niż 1.0 wskazująca słabszych poziom zaufania.

```
{
  "documents": [
    {
      "id": "1",
      "detectedLanguages": [
        {
          "name": "Spanish",
          "iso6391Name": "es",
          "score": 0.9375
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy do korzystania z analizy tekstu w usługach kognitywnych wykrywania języka. Bieżące informacje o głównych punktów wcześniej wyjaśniono i sprawdzono są następujące:

+ [Wykrywanie języka interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) jest dostępna dla języków 120.
+ Dokumenty JSON w treści żądania zawierają identyfikator i tekst.
+ Wysłanie żądania POST jest `/languages` punktu końcowego, za pomocą spersonalizowanych [dostępu do klucza i punkt końcowy](text-analytics-how-to-access-key.md) obowiązuje dla Twojej subskrypcji.
+ Dane wyjściowe odpowiedzi, która składa się z identyfikatorów języka dla identyfikator każdego dokumentu, można przesłać strumieniowo do wszystkich aplikacji, który akceptuje JSON, łącznie z programu Excel i usługi Power BI kilka.

## <a name="see-also"></a>Zobacz także 

 [Omówienie Analiza tekstu](../overview.md)  
 [Często zadawane pytania (FAQ)](../text-analytics-resource-faq.md)</br>
 [Strona produktu Analiza tekstu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Analizowanie wskaźniki nastrojów klientów](text-analytics-how-to-sentiment-analysis.md)
