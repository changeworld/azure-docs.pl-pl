---
title: Tagowanie obrazów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z tagowanie obrazów przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 159a1b9404f6ce635dcc8bb6007e52b7346ef98d
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982969"
---
# <a name="tagging-images"></a>Tagowanie obrazów

Przetwarzania obrazów zwraca tagi oparte na ponad 2000 rozpoznawalnych obiektów, istot żywych, scenerii i akcji. Gdy tagi są niejednoznaczne lub nie popularną wiedzę, odpowiedź interfejsu API zapewnia wskazówek, wyjaśnienie tagu w kontekście znanych ustawienie. Tagi nie są zorganizowane w postaci taksonomię i Brak hierarchii dziedziczenia istnieje. Kolekcja znaczników zawartości stanowi podstawę dla obrazu "opis" jest wyświetlany jako ludzi język do odczytu w postaci kompletnych zdań. Należy pamiętać, że w tym momencie język angielski jest jedynym obsługiwanym językiem opis obrazu.

Po przekazaniu obrazu lub określając adres URL obrazu, algorytmów przetwarzania obrazów w danych wyjściowych tagi na podstawie obiektów, istot żywych i działań określonych na obrazie. Oznakowanie nie jest ograniczona do głównego tematów, takich jak osoby na pierwszym planie, ale obejmuje również ustawienie meble (wewnątrz lub na zewnątrz), narzędzia, zakładów, zwierząt, Akcesoria, gadżety itp.

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