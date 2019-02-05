---
title: 'Szybki start: Rozpoznawanie emocji na twarzach na obrazie — interfejs API rozpoznawania emocji, cURL'
titlesuffix: Azure Cognitive Services
description: Uzyskaj informacje oraz przykłady kodu w celu szybkiego rozpoczęcia korzystania z interfejsu API rozpoznawania emocji z biblioteką cURL.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 2cd139fc47177d429bada454a5e720b7eb4f192b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220556"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Szybki start: Tworzenie aplikacji do rozpoznawania emocji na twarzach na obrazie.

> [!IMPORTANT]
> Interfejs API rozpoznawania emocji zostanie wycofany w dniu 15 lutego 2019 r. Rozpoznawanie emocji jest teraz ogólnie dostępne jako część [interfejsu API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/).

Ten artykuł zawiera informacje i przykłady kodu, które pomogą w szybkim rozpoczęciu korzystania z [metody Recognize interfejsu API rozpoznawania emocji](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) z biblioteką cURL do rozpoznawania emocji wyrażonych przez co najmniej jedną osobę na obrazie.

## <a name="prerequisite"></a>Wymagania wstępne
* Pobierz swój bezpłatny klucz subskrypcji [stąd](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-curl-example-request"></a>Przykładowe żądanie rozpoznawania emocji z użyciem biblioteki cURL

> [!NOTE]
> Lokalizacja użyta w wywołaniu REST i lokalizacja użyta do otrzymania kluczy subskrypcji muszą być takie same. Jeśli na przykład masz klucze subskrypcji z lokalizacji westcentralus, zastąp ciąg „westus” w poniższym adresie URL ciągiem „westcentralus”.

```json
@ECHO OFF

curl -v -X POST "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"

--data-ascii "{body}"
```

## <a name="recognize-emotions-sample-response"></a>Przykładowa odpowiedź dotycząca rozpoznawania emocji
Pomyślne wywołanie zwraca tablicę wpisów twarzy oraz skojarzonych z nimi ocen emocji uporządkowanych według rozmiaru prostokąta twarzy w kolejności malejącej. Pusta odpowiedź wskazuje, że nie wykryto żadnych twarzy. Wpis rozpoznawania emocji zawiera następujące pola:
* faceRectangle — lokalizacja prostokąta twarzy na obrazie.
* scores — ocena emocji dla każdej twarzy na obrazie.

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
