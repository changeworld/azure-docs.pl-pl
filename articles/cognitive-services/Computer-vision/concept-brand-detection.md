---
title: Wykrywanie marki - Wizja komputerowa
titleSuffix: Azure Cognitive Services
description: W tym artykule omówiono wyspecjalizowany tryb wykrywania obiektów; marki i/lub logo za pomocą interfejsu API Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 50e4fe1e2573c8566bbdf5697bb81b025a00935c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131736"
---
# <a name="detect-popular-brands-in-images"></a>Wykrywanie popularnych marek na obrazach

Wykrywanie marki to wyspecjalizowany tryb [wykrywania obiektów,](concept-object-detection.md) który wykorzystuje bazę danych tysięcy globalnych logo do identyfikacji marek komercyjnych w obrazach lub wideo. Za pomocą tej funkcji można na przykład ustalać, które marki są najpopularniejsze w mediach społecznościowych lub najpowszechniej promowane za pomocą lokowania produktów w mediach.

Usługa Computer Vision wykrywa, czy na danym obrazie znajdują się logo marki; Jeśli tak, zwraca nazwę marki, wynik zaufania i współrzędne obwiedni wokół logo.

Wbudowana baza danych logo obejmuje popularne marki w elektronice użytkowej, odzieży i innych. Jeśli okaże się, że marka, której szukasz, nie jest wykrywana przez usługę Computer Vision, możesz lepiej tworzyć i szkolić własny detektor logo za pomocą usługi [Custom Vision.](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)

## <a name="brand-detection-example"></a>Przykład wykrywania marki

Poniższe odpowiedzi JSON ilustrują, co funkcja Przetwarzania Obrazów zwraca podczas wykrywania marek w przykładowych obrazach.

![Czerwona koszula z etykietą i logo Firmy Microsoft](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

W niektórych przypadkach detektor marki podniesie zarówno obraz logo, jak i stylizowaną markę jako dwa oddzielne logo.

![Szara bluza z etykietą i logo Firmy Microsoft](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja wykrywania marki jest częścią interfejsu API [analizowania obrazu.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Ten interfejs API można wywołać za pośrednictwem natywnego sdk lub za pośrednictwem wywołań REST. Uwzględnij `Brands` w **parametrze kwerendy visualFeatures.** Następnie po otrzymaniu pełnej odpowiedzi JSON, po prostu przeanalizować `"brands"` ciąg zawartości sekcji.

* [Szybki start: obraz komputera .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Szybki start: analizowanie obrazu (INTERFEJS API REST)](./quickstarts/csharp-analyze.md)
