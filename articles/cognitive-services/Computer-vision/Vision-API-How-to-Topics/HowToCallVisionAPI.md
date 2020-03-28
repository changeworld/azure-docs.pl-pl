---
title: Wywoływanie interfejsu API przetwarzania obrazów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wywołać interfejs API przetwarzania obrazów komputerowych przy użyciu interfejsu API REST w usługach Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 298228eedb73298f00654f4f72c201d9ed671090
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72177064"
---
# <a name="call-the-computer-vision-api"></a>Wywoływanie interfejsu API przetwarzania obrazów

W tym artykule pokazano, jak wywołać interfejs API przetwarzania danych przy użyciu interfejsu API REST. Przykłady są zapisywane zarówno w języku C# przy użyciu biblioteki klienta interfejsu API interfejsu API i wywołania HTTP POST lub GET. Artykuł koncentruje się na:

- Uzyskiwanie tagów, opisu i kategorii
- Uzyskiwanie informacji specyficznych dla domeny lub "gwiazdy"

## <a name="prerequisites"></a>Wymagania wstępne

- Adres URL obrazu lub ścieżka do obrazu przechowywanego lokalnie
- Obsługiwane metody wprowadzania: plik binarny obrazu nieprzetworzonego w postaci aplikacji/strumienia oktetu lub adres URL obrazu
- Obsługiwane formaty plików obrazów: JPEG, PNG, GIF i BMP
- Rozmiar pliku obrazu: 4 MB lub mniej
- Wymiary obrazu: &times; 50 50 pikseli lub więcej
  
Przykłady w tym artykule pokazują następujące funkcje:

* Analizowanie obrazu w celu zwrócenia tablicy znaczników i opisu
* Analizowanie obrazu za pomocą modelu specyficznego dla domeny (w szczególności modelu "gwiazdy" w celu zwrócenia odpowiedniego wyniku w JSON

Funkcje oferują następujące opcje:

- **Opcja 1**: Analiza o określonym zakresie — analizowanie tylko określonego modelu
- **Opcja 2**: Ulepszona analiza - Analiza w celu dostarczenia dodatkowych szczegółów przy użyciu [taksonomii 86 kategorii](../Category-Taxonomy.md)
  
## <a name="authorize-the-api-call"></a>Autoryzowanie wywołania interfejsu API

Każde wywołanie do interfejsu API przetwarzania obrazów wymaga klucza subskrypcji. Ten klucz musi być przekazywany przez parametr ciągu zapytania lub określony w nagłówku żądania.

Aby uzyskać bezpłatny klucz próbny, wykonaj jedną z następujących czynności:
* Przejdź do strony [Wypróbuj usługi Cognitive Services.](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) 
* Przejdź do strony [Tworzenie konta usług Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) aby zasubskrybować wizję komputera.

Klucz subskrypcji można przekazać, wykonując dowolną z następujących czynności:

* Przekaż go przez ciąg zapytania, jak w tym przykładzie interfejsu API przetwarzania:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Określ to w nagłówku żądania HTTP:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* Korzystając z biblioteki klienta, przekazać klucz za pośrednictwem konstruktora ComputerVisionClient i określić region we właściwości klienta:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Przekazywanie obrazu do usługi interfejsu API przetwarzania obrazów

Podstawowym sposobem wykonania wywołania interfejsu API wizji komputerowej jest przesłanie obrazu bezpośrednio do tagów zwrotnych, opisu i gwiazd. Można to zrobić, wysyłając żądanie "POST" z obrazem binarnym w treści HTTP wraz z danymi odczytanych z obrazu. Metoda przekazywania jest taka sama dla wszystkich wywołań interfejsu API przetwarzania obrazów komputerowych. Jedyną różnicą są parametry kwerendy, które określisz. 

W przypadku określonego obrazu pobierz znaczniki i opis, korzystając z jednej z następujących opcji:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>Opcja 1: Uzyskaj listę tagów i opis

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>Opcja 2: Uzyskaj tylko listę tagów lub tylko opis

Tylko w przypadku tagów uruchom:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Tylko w przypadku opisu uruchom:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Uzyskaj analizę specyficzną dla domeny (gwiazdy)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Opcja 1: Analiza o określonym zakresie — analizowanie tylko określonego modelu
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

Dla tej opcji wszystkie pozostałe parametry zapytania {visualFeatures, details} są nieprawidłowe. Jeśli chcesz wyświetlić wszystkie obsługiwane modele, użyj kodu:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>Opcja 2: Ulepszona analiza — analiza w celu dostarczenia dodatkowych szczegółów przy użyciu taksonomii 86 kategorii

W przypadku aplikacji, w których chcesz uzyskać ogólną analizę obrazu oprócz szczegółów z jednego lub więcej modeli specyficznych dla domeny, rozszerz interfejs API w wersji 1 przy użyciu parametru zapytania modeli.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

Podczas wywoływania tej metody, należy najpierw wywołać [klasyfikator 86 kategorii.](../Category-Taxonomy.md) Jeśli którakolwiek z kategorii odpowiada kategorii znanego lub pasującego modelu, występuje drugi przebieg wywołań klasyfikatora. Na przykład, jeśli "details=all" lub "details" zawiera "gwiazdy", dzwonisz do modelu gwiazd po wywołaniu klasyfikatora 86 kategorii. Wynik obejmuje osobę kategorii. W przeciwieństwie do opcji 1, ta metoda zwiększa opóźnienia dla użytkowników, którzy są zainteresowani gwiazdami.

W takim przypadku wszystkie parametry kwerendy w wersji 1 zachowują się w ten sam sposób. Jeśli nie określisz visualFeatures=categories, jest niejawnie włączona.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Pobieranie i rozumienie danych wyjściowych JSON do analizy

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

Pole | Typ | Zawartość
------|------|------|
Tagi  | `object` | Obiekt najwyższego poziomu dla tablicy znaczników.
tags[].Name | `string`  | Słowo kluczowe z klasyfikatora tagów.
tags[].Score    | `number`  | Wskaźnik pewności siebie, od 0 do 1.
description  | `object` | Obiekt najwyższego poziomu dla opisu.
description.tags[] |    `string`    | Lista tagów.  Jeśli nie ma wystarczającego zaufania do możliwości tworzenia podpisu, tagi mogą być jedynymi informacjami dostępnymi dla obiektu wywołującego.
description.captions[].text | `string`  | Fraza opisująca obraz.
description.captions[].confidence   | `number`  | Wynik zaufania dla wyrażenia.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Pobieranie i rozumienie danych wyjściowych JSON modeli specyficznych dla domeny

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Opcja 1: Analiza o określonym zakresie — analizowanie tylko określonego modelu

Dane wyjściowe są tablicą znaczników, jak pokazano w poniższym przykładzie:

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

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>Opcja 2: Ulepszona analiza - Analiza w celu dostarczenia dodatkowych szczegółów przy użyciu taksonomii "86 kategorii"

W przypadku modeli specyficznych dla domeny przy użyciu opcji 2 (analiza rozszerzona) typ zwracany kategorii jest rozszerzany, jak pokazano w poniższym przykładzie:

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

Pole kategorii jest listą co najmniej jednej z [86 kategorii](../Category-Taxonomy.md) w oryginalnej taksonomii. Kategorie, które kończą się na dopasowaniu podkreślenia tej kategorii i jej obrażeń (na przykład "people_" lub "people_group" dla modelu gwiazd).

Pole   | Typ  | Zawartość
------|------|------|
categories | `object`   | Obiekt najwyższego poziomu.
categories[].name    | `string` | Nazwa z listy taksonomii 86 kategorii.
categories[].score  | `number`  | Wskaźnik pewności siebie, od 0 do 1.
categories[].detail  | `object?`      | (Opcjonalnie) Obiekt szczegółów.

Jeśli wiele kategorii jest zgodnych (na przykład klasyfikator 86 kategorii zwraca wynik zarówno dla "people_" i "people_young", gdy model = gwiazdy), szczegóły są dołączone do najbardziej ogólnego dopasowania poziomu ("people_", w tym przykładzie).

## <a name="error-responses"></a>Odpowiedzi na błędy

Błędy te są identyczne z tymi w vision.analyze, z dodatkowym błędem NotSupportedModel (HTTP 400), który może zostać zwrócony zarówno w scenariuszach opcji 1, jak i opcji 2. W przypadku opcji 2 (analiza rozszerzona), jeśli którykolwiek z modeli, które są określone w szczegółach nie jest rozpoznawany, interfejs API zwraca NotSupportedModel, nawet jeśli jeden lub więcej z nich są prawidłowe. Aby dowiedzieć się, jakie modele są obsługiwane, można wywołać listModels.

## <a name="next-steps"></a>Następne kroki

Aby korzystać z interfejsu API REST, przejdź do [dokumentacji dotyczącej interfejsu API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
