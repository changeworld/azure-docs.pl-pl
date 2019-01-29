---
title: Wykrywanie obiektów - przetwarzania obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia dotyczące wykrywania obiektów przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3468f9341e0a8406733877a05798e427dd454fff
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167567"
---
# <a name="object-detection"></a>Wykrywanie obiektów

Wykrywanie obiektów jest podobny do [znakowanie](concept-tagging-images.md), ale interfejs API zwraca otaczający współrzędne pola (w pikselach) dla każdego obiektu znaleziono. Na przykład jeśli na obrazie znajduje się pies, kot i osoba, operacja wykrywania utworzy listę tych obiektów wraz z ich współrzędnymi na obrazie. Ta funkcja służy do przetwarzania relacje między obiektami w obrazie. Umożliwia ona także ustalić, czy istnieje wiele wystąpień tego samego tagu obrazu.

Interfejs API wykrywania dotyczy tagi na podstawie obiektów lub rzeczy życia zidentyfikowane na obrazie. Należy pamiętać, że w tym momencie, nie ma formalnych relacji między taksonomii użycia w celu znakowania i taksonomii używane do wykrywania obiektu. W koncepcyjny poziom interfejsu API wykrywania tylko znajduje obiekty i elementy życia podczas API tagu może również obejmować kontekstowych terminy, takie jak "wewnętrzne", które nie zlokalizowane z blokujących pola.

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

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [kategoryzowanie obrazów](concept-categorizing-images.md) i [opisujące obrazów](concept-describing-images.md).
