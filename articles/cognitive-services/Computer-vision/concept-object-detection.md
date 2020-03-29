---
title: Wykrywanie obiektów - Wizja komputerowa
titleSuffix: Azure Cognitive Services
description: Poznaj pojęcia związane z funkcją wykrywania obiektów interfejsu API przetwarzania — użycie i limity.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3957e15a09bd7e7ecd814d169451af3241108b64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131669"
---
# <a name="detect-common-objects-in-images"></a>Wykrywanie typowych obiektów na obrazach

Wykrywanie obiektów jest podobne do [tagowania,](concept-tagging-images.md)ale interfejs API zwraca współrzędne obwiedni (w pikselach) dla każdego znalezionego obiektu. Na przykład jeśli na obrazie znajduje się pies, kot i osoba, operacja wykrywania utworzy listę tych obiektów wraz z ich współrzędnymi na obrazie. Za pomocą tej funkcji można przetwarzać relacje między obiektami na obrazie. Pozwala również określić, czy istnieje wiele wystąpień tego samego tagu w obrazie.

Interfejs API wykrywania stosuje znaczniki na podstawie obiektów lub żywych istot zidentyfikowanych na obrazie. Obecnie nie ma formalnego związku między znakowaniem taksonomii a taksonomią wykrywania obiektów. Na poziomie koncepcyjnym Wykryć interfejsu API tylko znajduje obiekty i istoty żywe, podczas gdy tag interfejsu API może również zawierać kontekstowe terminy, takie jak "indoor", które nie mogą być zlokalizowane z obwiedniami.

## <a name="object-detection-example"></a>Przykład wykrywania obiektów

Poniższa odpowiedź JSON ilustruje, co funkcja Przetwarzania Obrazów zwraca podczas wykrywania obiektów na przykładowym obrazie.

![Kobieta korzystająca z urządzenia Microsoft Surface w kuchni](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>Ograniczenia

Należy zwrócić uwagę na ograniczenia wykrywania obiektów, dzięki czemu można uniknąć lub złagodzić skutki fałszywych negatywów (nieodebranych obiektów) i ograniczonych szczegółów.

* Obiekty zazwyczaj nie są wykrywane, jeśli są małe (mniej niż 5% obrazu).
* Obiekty zazwyczaj nie są wykrywane, jeśli są rozmieszczone blisko siebie (na przykład stos płyt).
* Obiekty nie są rozróżniane według nazw marek lub produktów (na przykład różne typy napojów gazowanych na półce sklepowej). Można jednak uzyskać informacje o marce z obrazu za pomocą funkcji [wykrywania marki.](concept-brand-detection.md)

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja wykrywania obiektów jest częścią interfejsu API [analizy obrazu.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Ten interfejs API można wywołać za pośrednictwem natywnego sdk lub za pośrednictwem wywołań REST. Uwzględnij `Objects` w **parametrze kwerendy visualFeatures.** Następnie po otrzymaniu pełnej odpowiedzi JSON, po prostu przeanalizować `"objects"` ciąg zawartości sekcji.

* [Szybki start: obraz komputera .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Szybki start: analizowanie obrazu (INTERFEJS API REST)](./quickstarts/csharp-analyze.md)