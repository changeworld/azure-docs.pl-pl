---
title: Przewodnik Szybki start dotyczący analizowania obrazu lokalnego przy użyciu interfejsu API przetwarzania obrazów i programu cURL | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: W tym przewodniku Szybki start przeprowadzisz analizę obrazu lokalnego, korzystając z funkcji przetwarzania obrazów i programu cURL w usługach Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 93ca3ea6eee3743dfd0c25c9514375ae63a531ee
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772413"
---
# <a name="quickstart-analyze-a-local-image---rest-curl"></a>Szybki start: analizowanie obrazu lokalnego — REST, cURL

W tym przewodniku Szybki start przeprowadzisz analizę obrazu lokalnego w celu wyodrębnienia elementów wizualnych przy użyciu funkcji przetwarzania obrazów. Aby przeprowadzić analizę obrazu zdalnego, zobacz [Analyze a remote image with Python (Analizowanie obrazu zdalnego przy użyciu programu cURL)](curl-analyze.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z funkcji przetwarzania obrazów, musisz mieć klucz subskrypcji — zobacz [Obtaining Subscription Keys (Uzyskiwanie kluczy subskrypcji)](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="analyze-a-local-image"></a>Analizowanie lokalnego obrazu

Ten przykład jest podobny do [analizowania obrazu zdalnego przy użyciu programu cURL](curl-analyze.md), ale obraz do analizy jest odczytywany lokalnie z dysku. Należy wprowadzić trzy zmiany:

- Zmień typ zawartości na `"Content-Type: application/octet-stream"`.
- Zmień przełącznik `-d` na `--data-binary`.
- Określ obraz do analizy przy użyciu następującej składni: `@C:/Pictures/ImageToAnalyze.jpg`.

Aby uruchomić przykład, wykonaj następujące kroki:

1. Skopiuj następujący kod do edytora.
1. Zastąp wartość `<Subscription Key>` prawidłowym kluczem subskrypcji.
1. Zmień adres URL żądania (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`), aby użyć lokalizacji, w której uzyskano klucze subskrypcji, jeśli jest to konieczne.
1. Zastąp ciąg `<Image To Analyze>` obrazem lokalnym do analizy.
1. Opcjonalnie możesz zmienić język odpowiedzi (`language=en`).
1. Otwórz okno polecenia na komputerze z zainstalowanym programem cURL.
1. Wklej kod w oknie, a następnie uruchom polecenie.

>[!NOTE]
>Lokalizacja użyta w wywołaniu REST i lokalizacja użyta do otrzymania kluczy subskrypcji muszą być takie same. Jeśli na przykład uzyskano klucze subskrypcji z lokalizacji westus, zastąp ciąg „westcentralus” w poniższym adresie URL ciągiem „westus”.

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/octet-stream" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" --data-binary <Image To Analyze>
```

## <a name="analyze-image-response"></a>Odpowiedź na żądanie Analyze Image

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, na przykład:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z interfejsami API przetwarzania obrazów używanymi do analizy obrazu, wykrywania osobistości i charakterystycznych elementów krajobrazu, tworzenia miniatur oraz wyodrębniania tekstu drukowanego i odręcznego. Aby szybko zacząć eksperymentować z interfejsami API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Poznaj interfejsy API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
