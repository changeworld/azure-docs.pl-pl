---
title: 'Przykład: wywoływanie interfejsu API analizy obrazu — przetwarzanie obrazów'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wywołać interfejs API przetwarzania obrazów przy użyciu wzorca REST w usługach Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 97b9e0defb3f349a6e202572bc0e3005d5d87e9c
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141201"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>Przykład: jak wywoływać interfejs API przetwarzania obrazów

W tym przewodniku przedstawiono, jak wywoływać interfejs API przetwarzania obrazów przy użyciu wzorca REST. Przykłady są zapisywane w języku C# przy użyciu biblioteki klienta interfejsu API przetwarzania obrazów oraz jako wywołania HTTP POST/GET. Skupimy się na następujących kwestiach:

- Jak uzyskać obiekty „Tags”, „Description” i „Categories”.
- Jak uzyskać informacje „specyficzne dla domeny” (osobistości).

## <a name="prerequisites"></a>Wymagania wstępne

- Adres URL obrazu lub ścieżka do obrazu przechowywanego lokalnie.
- Obsługiwane metody wprowadzania danych: dane binarne nieprzetworzonego obrazu w postaci pliku typu application/octet stream lub adres URL obrazu
- Obsługiwane formaty obrazów: JPEG, PNG, GIF, BMP
- Rozmiar pliku obrazu: mniej niż 4 MB
- Wymiary obrazu: więcej niż 50 x 50 pikseli
  
W poniższych przykładach przedstawiono następujące funkcje:

1. Analizowanie obrazu i zwracanie tablicy tagów oraz opisu.
2. Analizowanie obrazu za pomocą modelu specyficznego dla domeny (w szczególności modelu „celebrities”) i uzyskanie odpowiedniego wyniku w zwróconym obiekcie JSON.

Funkcje zostały podzielone na:

- **Opcja pierwsza:** Analiza z zakresami — analizowanie tylko danego modelu
- **Opcja druga:** Analiza rozszerzona — analizowanie w celu dostarczenia dodatkowych szczegółów w oparciu o [taksonomię obejmującą 86 kategorii](../Category-Taxonomy.md)
  
## <a name="authorize-the-api-call"></a>Autoryzowanie wywołania interfejsu API

Każde wywołanie do interfejsu API przetwarzania obrazów wymaga klucza subskrypcji. Ten klucz musi zostać albo przekazany przez parametr ciągu zapytania, albo określony w nagłówku żądania.

Możesz uzyskać bezpłatny klucz wersji próbnej z usługi [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Lub postępuj zgodnie z instrukcjami w temacie [Tworzenie konta Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) , aby subskrybować przetwarzanie obrazów i uzyskać klucz.

1. Przekazywanie klucza subskrypcji za pomocą ciągu zapytania, poniżej przedstawiono przykład dotyczący interfejsu API przetwarzania obrazów:

    ```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

1. Przekazanie klucza subskrypcji możesz też określić nagłówku żądania HTTP:

    ```ocp-apim-subscription-key: <Your subscription key>```

1. Jeśli korzystasz z biblioteki klienta, klucz subskrypcji jest przekazywany w konstruktorze klasy VisionServiceClient:

    ```var visionClient = new VisionServiceClient("Your subscriptionKey");```

## <a name="upload-an-image-to-the-computer-vision-api-service-and-get-back-tags-descriptions-and-celebrities"></a>Przekazywanie obrazu do usługi interfejs API przetwarzania obrazów i pobieranie tagów, opisów i osobistości

Najprostszym sposobem wykonania wywołania interfejsu API przetwarzania obrazów jest bezpośrednie przekazanie obrazu. Odbywa się to przez wysłanie żądania „POST” z typem zawartości application/octet-stream wraz z danymi odczytanymi z obrazu. W przypadku obiektów „Tags” i „Description” ta metoda przekazywania będzie taka sama dla wszystkich wywołań interfejsu API przetwarzania obrazów. Jedyną różnicę będą stanowić parametry zapytania określone przez użytkownika. 

Poniżej przedstawiono, jak uzyskać obiekty „Tags” i „Description” dla danego obrazu:

**Opcja pierwsza:** Pobierz listę obiektów „Tags” i jeden obiekt „Description”

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using Microsoft.ProjectOxford.Vision;
using Microsoft.ProjectOxford.Vision.Contract;
using System.IO;

AnalysisResult analysisResult;
var features = new VisualFeature[] { VisualFeature.Tags, VisualFeature.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.AnalyzeImageAsync(fs, features);
}
```

**Opcja druga** Pobierz listę zawierającą tylko obiekty „Tags” lub listę zawierającą tylko obiekt „Description”:

###### <a name="tags-only"></a>Tylko Tagi:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Tylko opis:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```

### <a name="get-domain-specific-analysis-celebrities"></a>Pobierz analizę specyficzną dla domeny (osobistości)

**Opcja pierwsza:** Analiza z zakresami — analizowanie tylko danego modelu
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

Dla tej opcji wszystkie pozostałe parametry zapytania {visualFeatures, details} są nieprawidłowe. Jeśli chcesz wyświetlić wszystkie obsługiwane modele, użyj kodu:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```

**Opcja druga:** Analiza rozszerzona — analizowanie w celu dostarczenia dodatkowych szczegółów w oparciu o [taksonomię obejmującą 86 kategorii](../Category-Taxonomy.md)

W przypadku aplikacji, w których chcesz uzyskać analizę ogólną obrazu oprócz szczegółów z co najmniej jednego modelu specyficznego dla domeny, rozszerzamy interfejs API w wersji 1 za pomocą parametru zapytania o modele.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```

W przypadku wywoływania tej metody najpierw wywołamy klasyfikator obejmujący 86 kategorii. Jeśli którakolwiek z kategorii jest zgodna ze znanym/pasującym modelem, następuje drugie przekazanie wywołań klasyfikatora. Na przykład jeśli parametr „details=all” lub „details” obejmuje kategorię „celebrities”, wywołamy model celebrities po wywołaniu klasyfikatora obejmującego 86 kategorii i wynik będzie obejmować osobę z kategorii. Spowoduje to zwiększenie opóźnienia dla użytkowników zainteresowanych osobistościami (w porównaniu z użyciem opcji pierwszej).

Wszystkie parametry zapytania w wersji 1 będą w tym przypadku działać tak samo.  Jeśli parametr visualFeatures=categories nie zostanie podany, zostanie niejawnie włączony.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Pobierz i poznanie danych wyjściowych JSON na potrzeby analizy

Oto przykład:

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Pole | Type | Zawartość
------|------|------|
`Tags`  | `object` | Obiekt najwyższego poziomu dla tablicy tagów
tags[].Name | `string`  | Słowo kluczowe z klasyfikatora tagów
tags[].Score    | `number`  | Współczynnik ufności od 0 do 1.
description  | `object` | Obiekt najwyższego poziomu dla opisu.
description.tags[] |    `string`    | Lista tagów.  Jeśli występuje niewystarczający poziom ufności w zakresie tworzenia podpisu, tagi mogą stanowić jedyne informacje dostępne dla funkcji wywołującej.
description.captions[].text | `string`  | Fraza opisująca obraz.
description.captions[].confidence   | `number`  | Poziom ufności dla frazy.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Pobieranie i poznawanie danych wyjściowych w formacie JSON dla modeli specyficznych dla domeny

**Opcja pierwsza:** Analiza z zakresami — analizowanie tylko danego modelu

Dane wyjściowe będą w formie tablicy tagów, jak w tym przykładzie:

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

**Opcja druga:** Analiza rozszerzona — analizowanie w celu dostarczenia dodatkowych szczegółów w oparciu o taksonomię obejmującą 86 kategorii

W przypadku modeli specyficznych dla domeny używających opcji drugiej (analiza rozszerzona) zwracany typ kategorii zostaje rozszerzony. Oto przykład:

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

Pole kategorii jest listą co najmniej jednej z [86 kategorii](../Category-Taxonomy.md) w oryginalnej taksonomii. Należy również pamiętać, że kategorie zakończone podkreśleniem będą pasować do tej kategorii oraz jej elementów podrzędnych (na przykład people_ i people_group, dla modelu celebrities).

Pole   | Type  | Zawartość
------|------|------|
kategorie | `object`   | Obiekt najwyższego poziomu
categories[].name    | `string` | Nazwa z taksonomii obejmującej 86 kategorii
categories[].score  | `number`  | Współczynnik ufności od 0 do 1
categories[].detail  | `object?`      | Opcjonalny obiekt szczegółów

Należy pamiętać, że jeśli pasuje wiele kategorii (na przykład klasyfikator obejmujący 86 kategorii zwraca wynik dla kategorii people_ i people_young w przypadku modelu model=celebrities), szczegóły zostają dołączone do najbardziej ogólnego poziomu dopasowania (w tym przykładzie kategorii people_).

## <a name="errors-responses"></a>Błędy odpowiedzi

Są identyczne jak dla vision.analyze, ale zawierają dodatkowy błąd NotSupportedModel (HTTP 400), który może być zwracany zarówno w scenariuszu opcji pierwszej, jak i drugiej. W przypadku opcji drugiej (analiza rozszerzona), jeśli żaden z modeli podanych w szczegółach nie zostanie rozpoznany, interfejs API zwróci błąd NotSupportedModel, nawet jeśli niektóre z modeli są prawidłowe.  Użytkownicy mogą wywołać metodę listModels, aby dowiedzieć się, które modele są obsługiwane.

## <a name="next-steps"></a>Następne kroki

Aby korzystać z interfejsu API REST, przejdź do [dokumentacji dotyczącej interfejsu API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
