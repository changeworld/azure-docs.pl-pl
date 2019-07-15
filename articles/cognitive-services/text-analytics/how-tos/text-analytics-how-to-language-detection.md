---
title: Wykryj język za pomocą interfejsu API REST analizy tekstu | Dokumentacja firmy Microsoft
description: Wykryj język za pomocą interfejsu API REST analizy tekstu z usług Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: e1adeb34cf999f471bb183e4d7de9c65427252bb
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986506"
---
# <a name="example-detect-language-with-text-analytics"></a>Przykład: Wykryj język z analizy tekstu

[Wykrywanie języka](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) funkcji Azure REST interfejs API analizy tekstu ocenia wprowadzanie tekstu dla każdego dokumentu i zwraca identyfikatorów języka z oceną, która wskazuje siłę analizy.

Ta możliwość jest przydatna w przypadku magazynów zawartości przechowujących dowolne teksty, których język nie jest znany. Wyniki analizy możesz przeanalizować w celu ustalenia, który język jest używany w wejściowym dokumencie. Odpowiedzi zwraca również wartość jest wynik, który odzwierciedla ufności modelu. Wartość wyniku jest od 0 do 1.

Funkcja wykrywania języka może wykryć szerokiej gamy języków, warianty, dialekty i niektóre języki regionalne lub kultury. Dokładna lista języków dla tej funkcji nie jest opublikowana.

Jeśli masz zawartość wyrażona w języku rzadziej używane, można spróbować funkcji wykrywania języka, aby zobaczyć, jeśli zwracany jest kod. Odpowiedź dla języków, których nie można wykryć jest `unknown`.

> [!TIP]
> Analiza tekstu udostępnia również obraz kontenera platformy Docker oparty na systemie Linux na potrzeby wykrywania języka, można więc [zainstalować i uruchomić kontener analizy tekstu](text-analytics-how-to-install-containers.md) blisko danych.

## <a name="preparation"></a>Przygotowanie

Konieczne jest posiadanie dokumenty JSON w następującym formacie: Identyfikator i tekst.

Rozmiar dokumentu musi być w obszarze 5,120 znaków w dokumencie. Może mieć maksymalnie 1000 elementów (ID) na kolekcję. Kolekcja jest przesyłana w treści żądania. Poniższy przykład jest przykładem zawartości, które mogą przesyłać do wykrywania języka:

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
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Krok 1: Określenie struktury żądania

Aby uzyskać więcej informacji na temat definicji żądania, zobacz [wywołania interfejsu API analizy tekstu](text-analytics-how-to-call-api.md). Dla wygody poniżej ponownie podano odpowiednie kroki:

+ Utwórz żądanie POST. Aby zapoznać się z dokumentacją interfejsu API dla tego żądania, zobacz [interfejsu API wykrywania języka](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

+ Ustaw punkt końcowy HTTP dla wykrywania języka. Użyj zasobów analizy tekstu na platformie Azure lub wystąpieniami [kontenera analizy tekstu](text-analytics-how-to-install-containers.md). Musi on zawierać `/languages` zasobów: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`.

+ Ustaw nagłówek żądania, tak aby zawierał klucz dostępu dla operacji analizy tekstu. Aby uzyskać więcej informacji, zobacz [Znajdź punkty końcowe i klucze dostępu](text-analytics-how-to-access-key.md).

+ W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

> [!Tip]
> Użyj programu [Postman](text-analytics-how-to-call-api.md) lub otwórz **konsolę testowania interfejsu API** w [dokumentacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7), aby określić strukturę żądania i przesłać je do usługi za pomocą operacji POST.

## <a name="step-2-post-the-request"></a>Krok 2: Żądanie POST

Analiza jest wykonywana po odebraniu żądania. Aby uzyskać informacje na temat rozmiaru i liczby żądań można wysyłać na minutę i sekundę, zobacz [limity danych](../overview.md#data-limits) sekcja w przeglądzie.

Pamiętaj, że usługa jest bezstanowa. Żadne dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.


## <a name="step-3-view-the-results"></a>Krok 3: Wyświetlanie wyników

Wszystkie żądania POST Zwróć odpowiedź w formacie JSON — przy użyciu identyfikatorów i wykryto właściwości.

Dane wyjściowe są zwracane natychmiast. Można strumienia wyniki do aplikacji, która akceptuje JSON lub zapisywać dane wyjściowe do pliku w systemie lokalnym. Następnie można zaimportować dane wyjściowe do aplikacji, która umożliwia sortowanie, wyszukiwanie i manipulowania danymi.

Wyniki dla przykładowego żądania powinny wyglądać podobnie do następującego kodu JSON. Zwróć uwagę, jest jeden dokument z wieloma elementami. Dane wyjściowe są w języku angielskim. Identyfikatory języka obejmują przyjazną nazwę i kod języka zgodne ze standardem [ISO 639-1](https://www.iso.org/standard/22109.html).

Wynik dodatni 1,0 wyraża najwyższy możliwy poziom ufności analizy.



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

### <a name="ambiguous-content"></a>Zawartość niejednoznaczna

Jeżeli Analizator nie można przeanalizować danych wejściowych, zwraca `(Unknown)`. Przykładem jest, Jeśli przesyłasz blok tekstu, który składa się wyłącznie z arabskie.

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
### <a name="mixed-language-content"></a>Zawartość w językach mieszanych

W językach mieszanych zawartości w tym samym dokumencie zwraca język z największych reprezentacji w zawartości, ale z niższym ocenę dodatnią. Klasyfikacja odzwierciedla brzegowych siły oceny. W następującym przykładzie dane wejściowe stanowią mieszankę języków angielskiego, hiszpańskiego i francuskiego. Analizator zlicza znaki w każdym segmencie w celu ustalenia dominującego języka.

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

Dane wyjściowe składa się z dominujący języka, z wynikiem mniej niż 1.0, co oznacza słabszy poziom zaufania.

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

W tym artykule przedstawiono pojęcia i przepływ pracy na potrzeby wykrywania języka przy użyciu analizy tekstu w usługach Azure Cognitive Services. Następujące punkty zostały wyjaśnione i przedstawione w artykule:

+ [Wykrywanie języka](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) jest dostępna dla szerokiej gamy języków, warianty, dialekty i niektóre języki regionalne lub kultury.
+ Dokumenty JSON w treści żądania obejmują identyfikator i tekst.
+ Żądanie POST jest `/languages` punktu końcowego przy użyciu spersonalizowanego [dostępu key i punktu końcowego](text-analytics-how-to-access-key.md) , który jest poprawny dla Twojej subskrypcji.
+ Dane wyjściowe odpowiedzi składa się z identyfikatorów języka dla każdego identyfikatora dokumentu. Dane wyjściowe, może być przesyłany strumieniowo do wszystkich aplikacji, która akceptuje JSON. Przykładowe aplikacje obejmują kilka program Excel i Power BI.

## <a name="see-also"></a>Zobacz także 

 [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)</br>
 [Strona produktu analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Analiza opinii](text-analytics-how-to-sentiment-analysis.md)
