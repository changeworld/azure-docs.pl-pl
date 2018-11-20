---
title: 'Szybki start: wykrywanie twarzy na obrazie przy użyciu interfejsu API REST platformy Azure i środowiska Node.js'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start użyjesz interfejsu API REST rozpoznawania twarzy platformy Azure i środowiska Node.js do wykrywania twarzy na obrazie.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: 76747f7e9f1a95ee14ee570dcc29b42f98c26838
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51577997"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-nodejs"></a>Szybki start: wykrywanie twarzy na obrazie przy użyciu interfejsu API REST rozpoznawania twarzy i środowiska Node.js

W tym przewodniku Szybki start użyjesz interfejsu API REST rozpoznawania twarzy platformy Azure i środowiska Node.js do wykrywania ludzkich twarzy na obrazie.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji interfejsu API rozpoznawania twarzy. Klucz subskrypcji bezpłatnej wersji próbnej możesz uzyskać na stronie [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Możesz też wykonać instrukcje z tematu [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Tworzenie konta usług Cognitive Services), aby subskrybować usługę interfejsu API rozpoznawania twarzy i uzyskać klucz.

## <a name="create-the-nodejs-script"></a>Tworzenie skryptu środowiska Node.js

Poniższy kod służy do wywołania interfejsu API rozpoznawania twarzy i pobrania danych atrybutów twarzy z obrazu. Najpierw skopiuj kod do edytora tekstów &mdash; konieczne będzie wprowadzenie pewnych zmian przed jego uruchomieniem.

```nodejs
'use strict';

const request = require('request');

// Replace <Subscription Key> with your valid subscription key.
const subscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to get your
// subscription keys. For example, if you got your subscription keys from
// westus, replace "westcentralus" in the URL below with "westus".
const uriBase = 'https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg';

// Request parameters.
const params = {
    'returnFaceId': 'true',
    'returnFaceLandmarks': 'false',
    'returnFaceAttributes': 'age,gender,headPose,smile,facialHair,glasses,' +
        'emotion,hair,makeup,occlusion,accessories,blur,exposure,noise'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

### <a name="subscription-key"></a>Klucz subskrypcji
Zastąp wartość `<Subscription Key>` prawidłowym kluczem subskrypcji interfejsu API rozpoznawania twarzy.

### <a name="face-endpoint-url"></a>Adres URL punktu końcowego interfejsu API rozpoznawania twarzy

Adres URL `https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect` wskazuje punkt końcowy interfejsu API rozpoznawania twarzy platformy Azure. Należy zmienić pierwszą część tego adresu URL, aby była zgodna z regionem, który odpowiada Twojemu kluczowi subskrypcji (chyba że ustawiony jest już poprawny region).

### <a name="url-query-string"></a>Ciąg zapytania adresu URL

Pole `returnFaceAttributes` określa atrybuty twarzy do pobrania. Możesz zmienić ten ciąg w zależności od zamierzonego użycia.

### <a name="image-source-url"></a>Adres URL źródła obrazu
Pole `imageUrl` wskazuje obraz, który zostanie użyty jako dane wejściowe. Możesz go zmienić, aby wskazywał dowolny obraz, który chcesz przeanalizować.

## <a name="save-and-run-the-script"></a>Zapisywanie i uruchamianie skryptu

Po wprowadzeniu zmian zapisz plik jako skrypt JavaScript (plik JS). Następnie otwórz wiersz polecenia i uruchom go za pomocą polecenia `node`.

```
node myfile.js
```

Informacje o twarzy powinny zostać wyświetlone jako dane JSON w oknie konsoli. Na przykład:

```json
[
  {
    "faceId": "ae8952c1-7b5e-4a5a-a330-a6aa351262c9",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 6.8,
        "yaw": 3.7
      },
      "gender": "male",
      "age": 37,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.1
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.999,
        "sadness": 0.001,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.89
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.51
      },
      "noise": {
        "noiseLevel": "medium",
        "value": 0.59
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.04,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.98
          },
          {
            "color": "brown",
            "confidence": 0.87
          },
          {
            "color": "gray",
            "confidence": 0.85
          },
          {
            "color": "other",
            "confidence": 0.25
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.02
          }
        ]
      }
    }
  },
  {
    "faceId": "b1bb3cbe-5a73-4f8d-96c8-836a5aca9415",
    "faceRectangle": {
      "top": 693,
      "left": 1503,
      "width": 180,
      "height": 180
    },
    "faceAttributes": {
      "smile": 0.003,
      "headPose": {
        "pitch": 0,
        "roll": 2,
        "yaw": -2.2
      },
      "gender": "female",
      "age": 56,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0.001,
        "disgust": 0,
        "fear": 0,
        "happiness": 0.003,
        "neutral": 0.984,
        "sadness": 0.011,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.83
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.41
      },
      "noise": {
        "noiseLevel": "high",
        "value": 0.76
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.99
          },
          {
            "color": "gray",
            "confidence": 0.89
          },
          {
            "color": "other",
            "confidence": 0.64
          },
          {
            "color": "brown",
            "confidence": 0.34
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.03
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zostało utworzone polecenie cURL służące do wywoływania interfejsu API rozpoznawania twarzy platformy Azure w celu wykrywania twarzy na obrazie i zwracania ich atrybutów. Następnie zapoznaj się z dokumentacją referencyjną interfejsu API rozpoznawania twarzy, aby dowiedzieć się więcej.

> [!div class="nextstepaction"]
> [Interfejs API rozpoznawania twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)