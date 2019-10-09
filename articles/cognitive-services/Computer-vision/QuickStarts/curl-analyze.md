---
title: 'Szybki Start: analizowanie obrazu zdalnego — REST, zwinięcie'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start przeanalizujesz obraz zdalny przy użyciu interfejs API przetwarzania obrazów z zwinięciem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4f03ae62e2a455a5a5101bef45cf2d78100248f4
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177310"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-and-curl"></a>Szybki Start: analizowanie obrazu zdalnego przy użyciu interfejsu API REST przetwarzanie obrazów i zwinięcie

W tym przewodniku szybki start przeanalizuje się zdalnie przechowywany obraz w celu wyodrębnienia funkcji wizualnych przy użyciu interfejsu API REST przetwarzanie obrazów. Za pomocą metody [analizy obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) można wyodrębnić funkcje wizualne na podstawie zawartości obrazu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) .

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć [zwinięcie](https://curl.haxx.se/windows).
- Musisz mieć klucz subskrypcji dla przetwarzanie obrazów. Możesz uzyskać bezpłatny klucz wersji próbnej z usługi [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Lub postępuj zgodnie z instrukcjami w temacie [Tworzenie konta Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) , aby subskrybować przetwarzanie obrazów i uzyskać klucz. 

## <a name="create-and-run-the-sample-command"></a>Utwórz i uruchom przykładowe polecenie

Aby utworzyć i uruchomić przykład, wykonaj następujące czynności:

1. Skopiuj następujące polecenie do edytora tekstu.
1. W razie konieczności wprowadź następujące zmiany w poleceniu:
    1. Zastąp wartość `<subscriptionKey>` kluczem subskrypcji.
    1. Zastąp pierwszą część adresu URL żądania (`westcentralus`) tekstem w adresie URL własnego punktu końcowego.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Opcjonalnie Zmień parametr Language w adresie URL żądania (`language=en`), aby użyć innego obsługiwanego języka.
    1. Opcjonalnie można zmienić adres URL obrazu w treści żądania (`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`) na adres URL innego obrazu do przeanalizowania.
1. Otwórz okno wiersza polecenia.
1. Wklej polecenie z edytora tekstu do okna wiersza polecenia, a następnie uruchom polecenie.

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

## <a name="examine-the-response"></a>Badanie odpowiedzi

Pomyślna odpowiedź jest zwracana w formacie JSON. Przykładowa aplikacja analizuje i wyświetla pomyślną odpowiedź w oknie wiersza polecenia, podobnie jak w poniższym przykładzie:

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

Eksploruj interfejs API przetwarzania obrazów używany do analizowania obrazu, wykrywania osobistości i punktów orientacyjnych, tworzenia miniatury oraz wyodrębniania tekstu napisanego i odręcznego. Aby szybko eksperymentować z interfejs API przetwarzania obrazów, wypróbuj [konsolę testowania Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Eksplorowanie interfejs API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
