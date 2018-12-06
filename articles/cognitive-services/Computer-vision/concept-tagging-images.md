---
title: Tagowanie obrazów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z tagowanie obrazów przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 7e9181b6e805a768ca5dd8ed558b8feea45f22d3
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960911"
---
# <a name="tagging-images"></a>Tagowanie obrazów

Przetwarzania obrazów zwraca tagi na podstawie tysiące rozpoznawalnych obiektów, istot żywych, scenerii i akcji. Gdy tagi są niejednoznaczne lub wykraczają poza popularną wiedzę, odpowiedź interfejsu API zawiera „wskazówki”, aby wyjaśnić ich znaczenie w kontekście znanego otoczenia. Tagi nie są zorganizowane w formie taksonomii i nie występują hierarchie dziedziczenia. Kolekcja tagów zawartości stanowi podstawę „opisu” obrazu wyświetlanego jako język zrozumiały dla użytkownika w formie pełnych zdań. Należy pamiętać, że obecnie jedynym obsługiwanym językiem opisu obrazu jest język angielski.

Po przekazaniu obrazu lub określając adres URL obrazu, algorytmów przetwarzania obrazów w danych wyjściowych tagi na podstawie obiektów, istot żywych i działań określonych na obrazie. Tagowanie nie jest ograniczone do głównego tematu, na przykład do osoby na pierwszym planie, ale uwzględnia także otoczenie (wewnątrz lub na zewnątrz), meble, narzędzia, rośliny, zwierzęta, akcesoria, gadżety itd.

## <a name="image-tagging-example"></a>Przykład znakowania obrazu

Następującą odpowiedź JSON przedstawiono, jakie przetwarzania obrazów zwraca znakowanie wykryte w przykładowym obrazie funkcje programu visual.

![House_Yard](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [kategoryzowanie obrazów](concept-categorizing-images.md) i [opisujące obrazów](concept-describing-images.md).