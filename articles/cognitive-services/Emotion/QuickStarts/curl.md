---
title: Szybki start cURL emocji interfejsu API | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu interfejsu API rozpoznawania emocji — warstwa z cURL w usługach kognitywnych.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: d36a191d3589702d676e694715f34ea1c924e2d3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347541"
---
# <a name="emotion-api-curl-quick-start"></a>Szybki Start cURL emocji interfejsu API

> [!IMPORTANT]
> Interfejs API podglądu kończy się 30 października 2017 r. Testowanie nowego [wideo indeksatora interfejsu API w wersji zapoznawczej](https://azure.microsoft.com/services/cognitive-services/video-indexer/) można łatwo wyodrębnić szczegółowych informacji z wideo i celu ułatwienia pracy funkcję odnajdowania zawartości, takich jak wyniki wyszukiwania został określony poprzez wykrycie słowa rozmowy, kroje znaków i emocji. [Dowiedz się więcej](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Ten artykuł zawiera informacje i przykłady kodu, aby szybko rozpocząć korzystanie z [metody rozpoznaje interfejsu API rozpoznawania emocji — warstwa](https://dev.projectoxford.ai/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) z cURL do rozpoznawania emocji wyrażonych przez osoby na obrazie. 

## <a name="prerequisite"></a>Wymagania wstępne
* Uzyskaj swój klucz bezpłatnej subskrypcji [tutaj](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-curl-example-request"></a>Uznają, że emocji cURL przykładowe żądanie

> [!NOTE]
> W wywołania REST podczas uzyskiwania kluczy subskrypcji, należy użyć tej samej lokalizacji. Na przykład jeśli klucze subskrypcji został uzyskany z westcentralus, zastąp "westus" w adresie URL poniżej "westcentralus".

```json
@ECHO OFF

curl -v -X POST "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"

--data-ascii "{body}" 
```

## <a name="recognize-emotions-sample-response"></a>Rozpoznaje emocji przykładowa odpowiedź
Pomyślnego połączenia zwraca tablicę wpisów krój oraz wyniki emocji skojarzony, uszeregowane według rozmiaru prostokąt krój w kolejności malejącej. Pustą odpowiedź wskazuje, że zostały wykryte nie kroje. Wpis emocji zawiera następujące pola:
* faceRectangle - lokalizacji prostokąta powierzchni w obrazie.
* wyniki - emocji wyniki dla każdej powierzchni w obrazie. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]

