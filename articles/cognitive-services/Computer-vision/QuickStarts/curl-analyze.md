---
title: Przewodnik Szybki start dotyczący analizowania obrazu zdalnego przy użyciu interfejsu API przetwarzania obrazów i programu cURL | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: W tym przewodniku Szybki start przeprowadzisz analizę obrazu zdalnego, korzystając z funkcji przetwarzania obrazów i programu cURL w usługach Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: c092432adbcba9cf7a131584131fd05991d1edef
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772276"
---
# <a name="quickstart-analyze-a-remote-image---rest-curl"></a>Szybki start: analizowanie obrazu zdalnego — REST, cURL

W tym przewodniku Szybki start przeprowadzisz analizę obrazu zdalnego w celu wyodrębnienia elementów wizualnych przy użyciu funkcji przetwarzania obrazów. Aby przeprowadzić analizę obrazu lokalnego, zobacz [Analyze a local image with cURL (Analizowanie obrazu lokalnego przy użyciu programu cURL)](curl-disk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z funkcji przetwarzania obrazów, musisz mieć klucz subskrypcji — zobacz [Obtaining Subscription Keys (Uzyskiwanie kluczy subskrypcji)](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="analyze-a-remote-image"></a>Analizowanie zdalnego obrazu

[Metoda Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) umożliwia wyodrębnienie elementów wizualnych na podstawie zawartości obrazu. Możesz przekazać obraz lub określić adres URL obrazu i wybrać, które elementy mają zostać zwrócone. Może być to na przykład:

* Szczegółowa lista tagów związanych z zawartością obrazu.
* Opis zawartości obrazu w formie pełnego zdania.
* Współrzędne, płeć i wiek wszystkich twarzy znajdujących się na obrazie.
* Typ obrazu (clipart lub rysunek).
* Dominujący kolor, kolor akcentu lub to, czy obraz jest czarno-biały.
* Kategoria zdefiniowana w tej [taksonomii](../Category-Taxonomy.md).
* Czy na obrazie znajduje się zawartość przeznaczona dla osób dorosłych lub o charakterze seksualnym?

Aby uruchomić przykład, wykonaj następujące kroki:

1. Skopiuj następujący kod do edytora.
1. Zastąp wartość `<Subscription Key>` prawidłowym kluczem subskrypcji.
1. Zmień adres URL żądania (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`), aby użyć lokalizacji, w której uzyskano klucze subskrypcji, jeśli jest to konieczne.
1. Opcjonalnie możesz zmienić obraz (`{\"url\":\"...`) do analizy.
1. Opcjonalnie możesz zmienić język odpowiedzi (`language=en`).
1. Otwórz okno polecenia na komputerze z zainstalowanym programem cURL.
1. Wklej kod w oknie, a następnie uruchom polecenie.

>[!NOTE]
>Lokalizacja użyta w wywołaniu REST i lokalizacja użyta do otrzymania kluczy subskrypcji muszą być takie same. Jeśli na przykład uzyskano klucze subskrypcji z lokalizacji westus, zastąp ciąg „westcentralus” w poniższym adresie URL ciągiem „westus”.

## <a name="analyze-image-request"></a>Żądanie Analyze Image

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
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
