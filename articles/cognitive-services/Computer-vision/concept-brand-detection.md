---
title: Wykrywanie marki - przetwarzania obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia dotyczące wykrywania logo/na marki przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: d32beaa51471ccab19804122bfbcb33a6b1a5e3d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60203038"
---
# <a name="detect-popular-brands-in-images"></a>Wykrywanie marek popularne obrazy

Wykrywanie marki to wyspecjalizowane tryb [obiektu wykrywania](concept-object-detection.md) używający tysięcy logo globalnej bazy danych do identyfikowania marek handlowych na obrazach lub wideo. Za pomocą tej funkcji można na przykład ustalać, które marki są najpopularniejsze w mediach społecznościowych lub najpowszechniej promowane za pomocą lokowania produktów w mediach.

Usługa przetwarzania obrazów wykrywa, czy do danego obrazu; są logo na marki Jeśli tak, zwraca nazwę marki, współczynnik ufności i współrzędne pola ograniczeń wokół logo.

Logo wbudowanej bazy danych obejmuje popularnych marek z elektroniką, odzież i nie tylko. Jeśli okaże się, że marki, czego szukasz nie została wykryta przez usługę przetwarzania obrazów, możesz mogą być lepiej obsłużone tworzenie i szkolenie własne logo wykrywacz wartości za pomocą [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) usługi.

## <a name="brand-detection-example"></a>Przykład wykrywania marki

Następujące odpowiedzi JSON ilustrują, co przetwarzania obrazów zwraca podczas wykrywania marek w obrazach przykładu.

![Szara sweatshirt etykiety firmy Microsoft i logo na nim](./Images/gray-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.706,
         "rectangle":{
            "x":470,
            "y":862,
            "w":338,
            "h":327
         }
      }
   ],
   "requestId":"5fda6b40-3f60-4584-bf23-911a0042aa13",
   "metadata":{
      "width":2286,
      "height":1715,
      "format":"Jpeg"
   }
}
```
W niektórych przypadkach wykrywacz marki przejmą obraz logo i nazwę marki stylizowane jako dwa osobne logo.

![Wydarzenie red shirt etykiety firmy Microsoft i logo na nim](./Images/red-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.657,
         "rectangle":{
            "x":436,
            "y":473,
            "w":568,
            "h":267
         }
      },
      {
         "name":"Microsoft",
         "confidence":0.85,
         "rectangle":{
            "x":101,
            "y":561,
            "w":273,
            "h":263
         }
      }
   ],
   "requestId":"10dcd2d6-0cf6-4a5e-9733-dc2e4b08ac8d",
   "metadata":{
      "width":1286,
      "height":1715,
      "format":"Jpeg"
   }
}
```

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja wykrywania marki jest częścią [analizowanie obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) interfejsu API. Można wywołać tego interfejsu API za pomocą natywnego zestawu SDK lub wywołania REST. Obejmują `Brands` w **visualFeatures** parametr zapytania. Następnie, po otrzymaniu pełną odpowiedź JSON po prostu przeanalizować składni ciągu zawartości `"brands"` sekcji.

* [Szybki start: Analizowanie obrazu (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Szybki start: Analizowanie obrazu (interfejs API REST)](./quickstarts/csharp-analyze.md)
