---
title: Porady dotyczące wykrywania języka w interfejsie API REST analizy tekstu (Microsoft Cognitive Services na platformie Azure) | Microsoft Docs
description: Sposób wykrywania języka przy użyciu interfejsu API REST analizy tekstu w usługach Microsoft Cognitive Services na platformie Azure w tym samouczku z przewodnikiem.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: sample
ms.date: 09/12/2018
ms.author: heidist
ms.openlocfilehash: 984a027a1fe2f9c894701058452490bdf12b66c4
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338740"
---
# <a name="example-how-to-detect-language-in-text-analytics"></a>Przykład: Jak wykrywać język przy użyciu analizy tekstu

[Interfejs API wykrywania języka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) ocenia wejściowy tekst i dla każdego dokumentu zwraca identyfikatory języka z wynikiem wskazującym siłę analizy. Analiza tekstu rozpoznaje maksymalnie 120 języków.

Ta możliwość jest przydatna w przypadku magazynów zawartości przechowujących dowolne teksty, których język nie jest znany. Wyniki analizy możesz przeanalizować w celu ustalenia, który język jest używany w wejściowym dokumencie. Odpowiedź zawiera również wynik, który odzwierciedla zaufanie modelu (wartość z zakresu od 0 do 1).

> [!TIP]
> Analiza tekstu udostępnia również obraz kontenera platformy Docker oparty na systemie Linux na potrzeby wykrywania języka, można więc [zainstalować i uruchomić kontener analizy tekstu](text-analytics-how-to-install-containers.md) blisko danych.

## <a name="preparation"></a>Przygotowanie

Dokumenty JSON muszą mieć następujący format: identyfikator, tekst

Dokument musi mieć mniej niż 5000 znaków, a kolekcja może zawierać maksymalnie 1000 elementów (identyfikatorów). Kolekcja jest przesyłana w treści żądania. Oto przykład zawartości, dla której można wykrywać język.

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

## <a name="step-1-structure-the-request"></a>Krok 1. Określenie struktury żądania

Szczegółowe informacje na temat definicji żądania można znaleźć w artykule [Jak wywołać interfejs API analizy tekstu](text-analytics-how-to-call-api.md). Dla wygody poniżej ponownie podano odpowiednie kroki:

+ Utwórz żądanie **POST**. Zapoznaj się z dokumentacją interfejsu API dla tego żądania: [Interfejs API wykrywania języka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)

+ Ustaw punkt końcowy HTTP wykrywania języka przy użyciu zasobu analizy tekstu na platformie Azure lub utworzonego wystąpienia [kontenera analizy tekstu](text-analytics-how-to-install-containers.md). Musi on obejmować zasób `/languages`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`

+ Ustaw nagłówek żądania, tak aby zawierał klucz dostępu dla operacji analizy tekstu. Aby uzyskać więcej informacji, zobacz [How to find endpoints and access keys (Jak znajdować punkty końcowe i klucze dostępu)](text-analytics-how-to-access-key.md).

+ W treści żądania podaj kolekcję dokumentów JSON przygotowaną na potrzeby tej analizy.

> [!Tip]
> Użyj programu [Postman](text-analytics-how-to-call-api.md) lub otwórz **konsolę testowania interfejsu API** w [dokumentacji](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7), aby określić strukturę żądania i przesłać je do usługi za pomocą operacji POST.

## <a name="step-2-post-the-request"></a>Krok 2. Wysłanie żądania

Analiza jest wykonywana po odebraniu żądania. Usługa akceptuje maksymalnie 100 żądań na minutę. Maksymalny rozmiar każdego żądania to 1 MB.

Pamiętaj, że usługa jest bezstanowa. Żadne dane nie są przechowywane na koncie. Wyniki są zwracane natychmiast w odpowiedzi.


## <a name="step-3-view-results"></a>Krok 3. Wyświetlanie wyników

Wszystkie żądania POST zwracają odpowiedź w formacie JSON z identyfikatorami i wykrytymi właściwościami.

Dane wyjściowe są zwracane natychmiast. Wyniki można przesłać strumieniowo do aplikacji, która akceptuje kod JSON, lub zapisać do pliku w systemie lokalnym, a następnie zaimportować do aplikacji, która umożliwia sortowanie i wyszukiwanie danych oraz manipulowanie nimi.

Wyniki dla przykładowego żądania powinny wyglądać podobnie do następującego kodu JSON. Zwróć uwagę, że jest to jeden dokument z wieloma elementami. Dane wyjściowe są w języku angielskim. Identyfikatory języka obejmują przyjazną nazwę i kod języka zgodne ze standardem [ISO 639-1](https://www.iso.org/standard/22109.html).

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

Jeśli analizator nie można przeanalizować danych wejściowych (na przykład blok tekstu składa się wyłącznie z cyfr arabskich), zwraca wartość `(Unknown)`.

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
### <a name="mixed-language-content"></a>Zawartość w wielu językach

Jeśli dokument zawiera zawartość w wielu językach, zwracany jest język najczęściej występujący, lecz z mniejszą dodatnią oceną, co odzwierciedla niewielką siłę tej oceny. W następującym przykładzie dane wejściowe stanowią mieszankę języków angielskiego, hiszpańskiego i francuskiego. Analizator zlicza znaki w każdym segmencie w celu ustalenia dominującego języka.

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

Wynikowe dane wyjściowe zawierają dominujący język z wynikiem mniejszym niż 1,0, co wskazuje na niższy poziom ufności.

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

W tym artykule przedstawiono pojęcia i przepływ pracy dotyczące wykrywania języka za pomocą analizy tekstu w usłudze Cognitive Services. Oto krótkie przypomnienie głównych zagadnień, które uprzednio opisano i przedstawiono:

+ [Interfejs API wykrywania języka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) jest dostępny dla 120 języków.
+ Dokumenty JSON zawierają w treści żądania identyfikator i tekst.
+ Żądanie POST jest wysyłane do punktu końcowego `/languages` za pomocą spersonalizowanego [klucza dostępu i punktu końcowego](text-analytics-how-to-access-key.md) prawidłowego dla używanej subskrypcji.
+ Dane wyjściowe odpowiedzi składające się z identyfikatorów języka dla każdego identyfikatora dokumentu można przesłać strumieniowo do każdej aplikacji akceptującej kod JSON, na przykład programu Excel i usługi Power BI.

## <a name="see-also"></a>Zobacz też 

 [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)</br>
 [Strona produktu analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analiza opinii](text-analytics-how-to-sentiment-analysis.md)
