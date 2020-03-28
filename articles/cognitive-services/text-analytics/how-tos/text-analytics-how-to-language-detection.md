---
title: Wykrywanie języka za pomocą interfejsu API REST analizy tekstu
titleSuffix: Azure Cognitive Services
description: Wykrywanie języka przy użyciu interfejsu API REST analizy tekstu z usługi Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: d34f3a03e1bcd35c270d13c4dda57d0394a36e4b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70387787"
---
# <a name="example-detect-language-with-text-analytics"></a>Przykład: wykrywanie języka za pomocą analizy tekstu

Funkcja [wykrywania języka](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) interfejsu API REST usługi Azure Text Analytics ocenia wprowadzanie tekstu dla każdego dokumentu i zwraca identyfikatory języka z wynikiem wskazującym siłę analizy.

Ta możliwość jest przydatna w przypadku magazynów zawartości przechowujących dowolne teksty, których język nie jest znany. Wyniki analizy możesz przeanalizować w celu ustalenia, który język jest używany w wejściowym dokumencie. Odpowiedź zwraca również wynik, który odzwierciedla zaufanie modelu. Wartość wyniku wynosi od 0 do 1.

Funkcja wykrywania języka może wykrywać szeroką gamę języków, wariantów, dialektów oraz niektórych języków regionalnych lub kulturowych. Dokładna lista języków tej funkcji nie jest publikowana.

Jeśli masz zawartość wyrażoną w rzadziej używanym języku, możesz wypróbować funkcję wykrywania języka, aby sprawdzić, czy zwraca kod. Odpowiedź dla języków, których nie można `unknown`wykryć, to .

> [!TIP]
> Analiza tekstu udostępnia również obraz kontenera platformy Docker oparty na systemie Linux na potrzeby wykrywania języka, można więc [zainstalować i uruchomić kontener analizy tekstu](text-analytics-how-to-install-containers.md) blisko danych.

## <a name="preparation"></a>Przygotowywanie

Dokumenty JSON muszą być zawierane w tym formacie: Identyfikator i tekst.

Rozmiar dokumentu musi być mniejszy niż 5120 znaków na dokument. Możesz mieć maksymalnie 1000 elementów (identyfikatory) na kolekcję. Kolekcja jest przesyłana w treści żądania. Poniższy przykład jest przykładem zawartości, którą można przesłać do wykrywania języka:

```json
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

Aby uzyskać więcej informacji na temat definicji żądania, zobacz [Wywołanie interfejsu API analizy tekstu](text-analytics-how-to-call-api.md). Dla wygody poniżej ponownie podano odpowiednie kroki:

+ Utwórz żądanie POST. Aby przejrzeć dokumentację interfejsu API dla tego żądania, zobacz [interfejs API wykrywania języka](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

+ Ustaw punkt końcowy HTTP dla wykrywania języka. Użyj zasobu analizy tekstu na platformie Azure lub [kontenera analizy tekstu.](text-analytics-how-to-install-containers.md) Musisz podać `/text/analytics/v2.1/languages` w adresie URL. Na przykład: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/languages`.

+ Ustaw nagłówek żądania, aby uwzględnić [klucz dostępu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dla operacji analizy tekstu.

+ W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

> [!Tip]
> Użyj programu [Postman](text-analytics-how-to-call-api.md) lub otwórz **konsolę testowania interfejsu API** w [dokumentacji](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7), aby określić strukturę żądania i przesłać je do usługi za pomocą operacji POST.

## <a name="step-2-post-the-request"></a>Krok 2: POST wniosek

Analiza jest wykonywana po odebraniu żądania. Aby uzyskać informacje na temat rozmiaru i liczby żądań, które można wysyłać na minutę i sekundę, zobacz sekcję [limitów danych](../overview.md#data-limits) w przeglądzie.

Pamiętaj, że usługa jest bezstanowa. Żadne dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.


## <a name="step-3-view-the-results"></a>Krok 3: Zobacz wyniki

Wszystkie żądania POST zwracają odpowiedź w formacie JSON z identyfikatorami i wykrytymi właściwościami.

Dane wyjściowe są zwracane natychmiast. Wyniki można przesyłać strumieniowo do aplikacji, która akceptuje JSON lub zapisać dane wyjściowe w pliku w systemie lokalnym. Następnie zaimportuj dane wyjściowe do aplikacji, której można używać do sortowania, wyszukiwania i manipulowania danymi.

Wyniki dla przykładowego żądania powinny wyglądać podobnie do następującego kodu JSON. Należy zauważyć, że jest to jeden dokument z wieloma elementami. Dane wyjściowe są w języku angielskim. Identyfikatory języka obejmują przyjazną nazwę i kod języka zgodne ze standardem [ISO 639-1](https://www.iso.org/standard/22109.html).

Wynik dodatni 1,0 wyraża najwyższy możliwy poziom ufności analizy.

```json
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
        "errors": []
    }
```

### <a name="ambiguous-content"></a>Zawartość niejednoznaczna

W niektórych przypadkach może być trudne do odróżnienia języków na podstawie danych wejściowych. Za pomocą `countryHint` tego parametru można określić 2-literowy kod kraju. Domyślnie interfejs API używa "US" jako domyślnego krajuHint, aby usunąć to zachowanie, możesz `countryHint = ""` zresetować ten parametr, ustawiając tę wartość na pusty ciąg .

Na przykład "Niemożliwe" jest wspólne zarówno dla języka angielskiego, jak i francuskiego, a jeśli zostanie podane w ograniczonym kontekście, odpowiedź będzie oparta na "amerykańskiej" podpowiedzi kraju. Jeśli wiadomo, że tekst pochodzi z Francji, można podać to we wskazówce.

**Dane wejściowe**

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "impossible"
            },
            {
                "id": "2",
                "text": "impossible",
                "countryHint": "fr"
            }
        ]
    }
```

Usługa ma teraz dodatkowy kontekst, aby lepiej osądzać: 

**Wyjście**

```json
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
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

Jeśli analizator nie może przeanalizować danych wejściowych, zwraca `(Unknown)`wartość . Przykładem jest przesłanie bloku tekstowego, który składa się wyłącznie z cyfr arabskich.

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "score": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>Treści w języku mieszanym

Zawartość w języku mieszanym w tym samym dokumencie zwraca język z największą reprezentacją w treści, ale z niższą oceną dodatnią. Ocena odzwierciedla marginalną siłę oceny. W następującym przykładzie dane wejściowe stanowią mieszankę języków angielskiego, hiszpańskiego i francuskiego. Analizator zlicza znaki w każdym segmencie w celu ustalenia dominującego języka.

**Dane wejściowe**

```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
      ]
    }
```

**Wyjście**

Wynikowy wynik składa się z dominującego języka, z wynikiem mniejszym niż 1,0, co wskazuje na słabszy poziom zaufania.

```json
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

W tym artykule poznaliście pojęcia i przepływ pracy do wykrywania języka przy użyciu analizy tekstu w usługach Azure Cognitive Services. Wyjaśniono i wykazano następujące kwestie:

+ [Wykrywanie języków](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) jest dostępne dla szerokiej gamy języków, wariantów, dialektów oraz niektórych języków regionalnych lub kulturowych.
+ Dokumenty JSON w treści żądania zawierają identyfikator i tekst.
+ Żądanie POST jest `/languages` do punktu końcowego przy użyciu [spersonalizowanego klucza dostępu i punktu końcowego,](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) który jest prawidłowy dla subskrypcji.
+ Dane wyjściowe odpowiedzi składają się z identyfikatorów języka dla każdego identyfikatora dokumentu. Dane wyjściowe mogą być przesyłane strumieniowo do dowolnej aplikacji, która akceptuje JSON. Przykładowe aplikacje obejmują programy Excel i Power BI, aby wymienić tylko kilka.

## <a name="see-also"></a>Zobacz też

 [Omówienie analizy tekstu](../overview.md) [Frequently asked questions (FAQ) (Często zadawane pytania (FAQ))](../text-analytics-resource-faq.md)</br>
 [Strona produktu analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analiza tonacji](text-analytics-how-to-sentiment-analysis.md)
