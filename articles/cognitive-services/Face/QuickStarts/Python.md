---
title: 'Szybki start: wykrywanie twarzy na obrazie przy użyciu interfejsu API REST i języka Python'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start omówiono wykrywanie twarzy na obrazie przy użyciu interfejsu API rozpoznawania twarzy z językiem Python.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/24/2018
ms.author: pafarley
ms.openlocfilehash: 76a9260d534057e3a03f8c8f1d7420329713ea80
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957279"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-python"></a>Szybki start: wykrywanie twarzy na obrazie przy użyciu interfejsu API REST i języka Python

W tym przewodniku Szybki start wykryjesz ludzką twarz na obrazie zdalnym za pomocą usługi rozpoznawania twarzy. Wykryte twarze są oznaczane prostokątami z nałożoną płcią i wiekiem poszczególnych osób. Aby użyć obrazu lokalnego, należy zapoznać się ze składnią w temacie [Przetwarzanie obrazów: analizowanie obrazu lokalnego przy użyciu języka Python](../../Computer-vision/QuickStarts/python-disk.md).

Ten przewodnik Szybki start można uruchomić jako notes Jupyter w programie [MyBinder](https://mybinder.org). Aby uruchomić usługę Binder, wybierz poniższy przycisk:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=FaceAPI.ipynb)

## <a name="prerequisites"></a>Wymagania wstępne

Do uruchomienia przykładu potrzebny jest klucz subskrypcji. Klucze subskrypcji bezpłatnej wersji próbnej możesz uzyskać na stronie [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

## <a name="detect-faces-in-an-image"></a>Wykrywanie twarzy na obrazie

Użyj metody [Face — Detect](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), aby wykryć twarze na obrazie i zwrócić atrybuty twarzy, w tym:

* Identyfikator Face ID: unikatowy identyfikator używany w kilku scenariuszach dotyczących interfejsu API rozpoznawania twarzy.
* Obrys twarzy: lewy górny róg oraz szerokość i wysokość wskazujące na lokalizację twarzy na obrazie.
* Charakterystyczne punkty twarzy: układ 27 charakterystycznych punktów twarzy wskazujący położenie istotnych części twarzy.
* Atrybuty twarzy, takie jak wiek, płeć, intensywność uśmiechu, położenie głowy i zarost.

Aby uruchomić przykład, wykonaj następujące kroki:

1. Skopiuj poniższy kod do nowego pliku skryptu w języku Python.
1. Zastąp wartość `<Subscription Key>` prawidłowym kluczem subskrypcji.
1. Zmień wartość `face_api_url` na lokalizację, z której uzyskano klucze subskrypcji, jeśli jest to konieczne.
1. Opcjonalnie możesz zmienić wartość `image_url` na inny obraz.
1. Uruchom skrypt.

### <a name="face---detect-request"></a>Żądanie Face — Detect

W poniższym kodzie użyto biblioteki `requests` języka Python, aby wywołać interfejs API wykrywania twarzy. Wyniki są zwracane w formie obiektu JSON. Klucz interfejsu API jest przekazywany za pośrednictwem słownika `headers`. Typy elementów, które mają zostać rozpoznane, są przekazywane za pośrednictwem słownika `params`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from matplotlib import patches
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the westcentralus region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
face_api_url = 'https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect'

# Set image_url to the URL of an image that you want to analyze.
image_url = 'https://how-old.net/Images/faces2/main007.jpg'

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {
    'returnFaceId': 'true',
    'returnFaceLandmarks': 'false',
    'returnFaceAttributes': 'age,gender,headPose,smile,facialHair,glasses,' +
    'emotion,hair,makeup,occlusion,accessories,blur,exposure,noise'
}
data = {'url': image_url}
response = requests.post(face_api_url, params=params, headers=headers, json=data)
faces = response.json()

# Display the original image and overlay it with the face information.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.figure(figsize=(8, 8))
ax = plt.imshow(image, alpha=0.6)
for face in faces:
    fr = face["faceRectangle"]
    fa = face["faceAttributes"]
    origin = (fr["left"], fr["top"])
    p = patches.Rectangle(
        origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
    ax.axes.add_patch(p)
    plt.text(origin[0], origin[1], "%s, %d"%(fa["gender"].capitalize(), fa["age"]),
             fontsize=20, weight="bold", va="bottom")
_ = plt.axis("off")
```

### <a name="face---detect-response"></a>Odpowiedź na żądanie Face — Detect

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, na przykład:

```json
[
  {
    "faceId": "35102aa8-4263-4139-bfd6-185bb0f52d88",
    "faceRectangle": {
      "top": 208,
      "left": 228,
      "width": 91,
      "height": 91
    },
    "faceAttributes": {
      "smile": 1,
      "headPose": {
        "pitch": 0,
        "roll": 4.3,
        "yaw": -0.3
      },
      "gender": "female",
      "age": 27,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 1,
        "neutral": 0,
        "sadness": 0,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "low",
        "value": 0
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.65
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
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
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "blond",
            "confidence": 0.5
          },
          {
            "color": "black",
            "confidence": 0.34
          },
          {
            "color": "red",
            "confidence": 0.32
          },
          {
            "color": "gray",
            "confidence": 0.14
          },
          {
            "color": "other",
            "confidence": 0.03
          }
        ]
      }
    }
  },
  {
    "faceId": "42502166-31bb-4ac8-81c0-a7adcb3b3e70",
    "faceRectangle": {
      "top": 109,
      "left": 125,
      "width": 79,
      "height": 79
    },
    "faceAttributes": {
      "smile": 1,
      "headPose": {
        "pitch": 0,
        "roll": 1.7,
        "yaw": 2.1
      },
      "gender": "male",
      "age": 32,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.4
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 1,
        "neutral": 0,
        "sadness": 0,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.11
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.74
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.02,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "blond",
            "confidence": 0.94
          },
          {
            "color": "red",
            "confidence": 0.76
          },
          {
            "color": "gray",
            "confidence": 0.2
          },
          {
            "color": "other",
            "confidence": 0.03
          },
          {
            "color": "black",
            "confidence": 0.01
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat interfejsów API rozpoznawania twarzy używanych do wykrywania ludzkich twarzy na obrazach, rozpoznawania ich obrysów i zwracania atrybutów, takich jak wiek i płeć.

> [!div class="nextstepaction"]
> [Interfejsy API rozpoznawania twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
