---
title: Wykrywanie obiektów - przetwarzania obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z funkcji wykrywania obiektu interfejsu API przetwarzania obrazów — użycia i limitów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 012ab849c926de332da55361c79c76c5a1311169
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001851"
---
# <a name="detect-common-objects-in-images"></a>Wykryć wspólnych obiektów na obrazach

Wykrywanie obiektów jest podobny do [znakowanie](concept-tagging-images.md), ale interfejs API zwraca otaczający współrzędne pola (w pikselach) dla każdego obiektu znaleziono. Na przykład jeśli na obrazie znajduje się pies, kot i osoba, operacja wykrywania utworzy listę tych obiektów wraz z ich współrzędnymi na obrazie. Ta funkcja służy do przetwarzania relacje między obiektami w obrazie. Umożliwia ona także ustalić, czy istnieje wiele wystąpień tego samego tagu obrazu.

Interfejs API wykrywania dotyczy tagi na podstawie obiektów lub rzeczy życia zidentyfikowane na obrazie. Obecnie nie ma formalnych relacji między znakowania taksonomii i taksonomii wykrywanie obiektów. W koncepcyjny poziom interfejsu API wykrywania tylko znajduje obiekty i elementy życia podczas API tagu może również obejmować kontekstowych terminy, takie jak "wewnętrzne", które nie zlokalizowane z blokujących pola.

## <a name="object-detection-example"></a>Przykład wykrywania obiektów

Następującą odpowiedź JSON przedstawiono, jakie przetwarzania obrazów zwraca podczas wykrywania obiektów na przykładowym obrazie.

![Kobieta w kuchni przy użyciu urządzenia Microsoft Surface](./Images/windows-kitchen.jpg)

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

Należy zauważyć ograniczenia wykrywanie obiektów, co pozwala uniknąć lub łagodzenia skutków fałszywych wyników negatywnych (brakujących obiektów) i szczegóły ograniczone.

* Obiekty zazwyczaj nie są wykrywane, gdy są one mały (mniej niż 5% obrazu).
* Obiekty zazwyczaj nie są wykrywane, jeśli są ułożone ściśle ze sobą (stos talerzy, na przykład).
* Obiekty nie są zróżnicowane według marki lub produktu nazwy (różnego rodzaju sodas na półce magazynu, na przykład). Jednak można uzyskać informacji na marki z obrazu za pomocą [oznaczyć wykrywania](concept-brand-detection.md) funkcji.

## <a name="use-the-api"></a>Za pomocą interfejsu API

Funkcja wykrywania obiektu jest częścią [analizowanie obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) interfejsu API. Można wywołać tego interfejsu API za pomocą natywnego zestawu SDK lub wywołania REST. Obejmują `Objects` w **visualFeatures** parametr zapytania. Następnie, po otrzymaniu pełną odpowiedź JSON po prostu przeanalizować składni ciągu zawartości `"objects"` sekcji.

* [Szybki start: Analizowanie obrazu (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Szybki start: Analizowanie obrazu (interfejs API REST)](./quickstarts/csharp-analyze.md)