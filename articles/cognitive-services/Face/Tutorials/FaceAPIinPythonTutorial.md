---
title: Powierzchni interfejsu API języka Python samouczek | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Dowiedz się, jak wykryć człowieka kroje obrazu w usługach kognitywnych za pomocą interfejsu API krój przy użyciu zestawu SDK Python.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 90d74d8df2ed59e6f3313ef7c620284d1022a667
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049115"
---
# <a name="getting-started-with-face-api-in-python-tutorial"></a>Wprowadzenie do korzystania z powierzchni interfejsu API w samouczku Python

Z tego samouczka dowiesz się do wywołania interfejsu API krój przez zestaw SDK Python, aby wykryć człowieka kroje obrazu.

## <a name="prerequisites"></a> Wymagania wstępne

Aby korzystać z tego samouczka, należy wykonać następujące czynności:

- Zainstaluj środowisko Python 2.7 + lub Python 3.5 +.
- Zainstaluj narzędzie pip.
- Zainstaluj zestaw SDK Python dla powierzchni interfejsu API w następujący sposób:

```bash
pip install cognitive_face
```

- Uzyskaj [klucza subskrypcji](https://azure.microsoft.com/try/cognitive-services/) kognitywnych usług firmy Microsoft. W tym samouczku, można użyć sieci podstawowej lub dodatkowej klucza. (Należy pamiętać, że aby korzystać z żadnych powierzchni interfejsu API, musi mieć klucz ważnej subskrypcji).

## <a name="sdk-example"></a> Wykrywanie twarzy na obrazie

```python
import cognitive_face as CF

KEY = '<Subscription Key>'  # Replace with a valid subscription key (keeping the quotes in place).
CF.Key.set(KEY)

BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'  # Replace with your regional Base URL
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

Poniżej znajduje się przykład wynik. Jest `list` z wykrytych powierzchni. Każdy element na liście jest `dict` wystąpienie where `faceId` jest unikatowy identyfikator dla powierzchni wykryte i `faceRectangle` opisuje pozycji wykryto powierzchni. Identyfikator krój wygaśnie w ciągu 24 godzin.

```python
[{u'faceId': u'68a0f8cf-9dba-4a25-afb3-f9cdf57cca51', u'faceRectangle': {u'width': 89, u'top': 66, u'height': 89, u'left': 446}}]
```

## <a name="draw-rectangles-around-the-faces"></a>Rysowanie prostokątów wokół powierzchni

Przy użyciu współrzędnych json, które odebrano od poprzedniego polecenia, można Rysowanie prostokątów obrazu do wizualnego reprezentowania każdej powierzchni. Konieczne będzie `pip install Pillow` do używania `PIL` modułu obsługi obrazów.  W górnej części pliku Dodaj następujące informacje:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Następnie po `print(faces)`, są następujące w skrypcie:

```python
#Convert width height to a point in a rectangle
def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))

#Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

#For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

#Display the image in the users default image browser.
img.show()
```

## <a name='further'></a> Dalszych badań

Aby ułatwić dalszą analizę powierzchni interfejsu API, w tym samouczku omówiono przykładowe graficznego interfejsu użytkownika. Aby go uruchomić, należy najpierw zainstalować [wxPython](https://wxpython.org/pages/downloads/) następnie uruchom poniższe polecenia.

```bash
git clone https://github.com/Microsoft/Cognitive-Face-Python.git
cd Cognitive-Face-Python
python sample
```

## <a name="summary"></a> Podsumowanie

W tym samouczku uzyskanych podstawowy proces podczas korzystania z interfejsu API krój za pośrednictwem wywołania Python SDK. Aby uzyskać więcej informacji na szczegóły interfejsu API, zapoznaj się instrukcje i [dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related"></a> Tematy pokrewne

- [Wprowadzenie do powierzchni interfejsu API w CSharp](FaceAPIinCSharpTutorial.md)
- [Wprowadzenie do korzystania z powierzchni interfejsu API w języku Java dla systemu Android](FaceAPIinJavaForAndroidTutorial.md)
