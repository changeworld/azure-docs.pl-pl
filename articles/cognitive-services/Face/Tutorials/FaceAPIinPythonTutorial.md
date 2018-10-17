---
title: 'Samouczek: Wykrywanie i oznaczanie ramką twarzy na obrazie — interfejs API rozpoznawania twarzy, Python'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak za pomocą interfejsu API rozpoznawania twarzy z zestawem SDK języka Python wykrywać ludzkie twarze na obrazie.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 6cc3ac25d2196c0275b445503b79b9ac06a791d3
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127741"
---
# <a name="tutorial-detect-and-frame-faces-with-the-face-api-and-python"></a>Samouczek: Wykrywanie i oznaczanie ramką twarzy za pomocą interfejsu API rozpoznawania twarzy i języka Python 

W ramach tego samouczka nauczysz się wywoływać interfejs API rozpoznawania twarzy za pomocą zestawu SDK języka Python w celu wykrywania ludzkich twarzy na obrazie.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z tego samouczka, musisz wykonać następujące czynności:

- Zainstaluj język Python 2.7+ lub Python 3.5+.
- Zainstaluj program pip.
- Zainstaluj zestaw SDK języka Python na potrzeby interfejsu API rozpoznawania twarzy w następujący sposób:

```bash
pip install cognitive_face
```

- Uzyskaj [klucz subskrypcji](https://azure.microsoft.com/try/cognitive-services/) dla usług Microsoft Cognitive Services. W tym samouczku możesz użyć klucza podstawowego lub pomocniczego. (Zwróć uwagę, że aby korzystać z dowolnego interfejsu API rozpoznawania twarzy, musisz mieć ważny klucz subskrypcji).

## <a name="detect-a-face-in-an-image"></a>Wykrywanie twarzy na obrazie

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

Poniżej przedstawiono przykładowy wynik. Jest to `list` wykrytych twarzy. Każdy element na liście to wystąpienie `dict`, w którym `faceId` jest unikatowym identyfikatorem wykrytej twarzy, a `faceRectangle` opisuje położenie wykrytej twarzy. Identyfikator twarzy wygasa w ciągu 24 godzin.

```python
[{u'faceId': u'68a0f8cf-9dba-4a25-afb3-f9cdf57cca51', u'faceRectangle': {u'width': 89, u'top': 66, u'height': 89, u'left': 446}}]
```

## <a name="draw-rectangles-around-the-faces"></a>Rysowanie prostokątów wokół twarzy

Używając współrzędnych json otrzymanych w wyniku działania poprzedniego polecenia, możesz rysować na obrazie prostokąty reprezentujące wizualnie poszczególne twarze. Aby używać modułu przetwarzania obrazów `PIL`, potrzebujesz polecenia `pip install Pillow`.  W górnej części pliku dodaj następujący fragment kodu:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Następnie po instrukcji `print(faces)` dodaj w skrypcie następujący fragment kodu:

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

## <a name="further-exploration"></a>Dalsza eksploracja

Aby ułatwić dalszą eksplorację interfejsu API rozpoznawania twarzy, ten samouczek zawiera przykład graficznego interfejsu użytkownika. Aby go uruchomić, najpierw zainstaluj program [wxPython](https://wxpython.org/pages/downloads/), a następnie uruchom poniższe polecenia.

```bash
git clone https://github.com/Microsoft/Cognitive-Face-Python.git
cd Cognitive-Face-Python
python sample
```

## <a name="summary"></a>Podsumowanie

W ramach tego samouczka poznaliśmy podstawowy proces korzystania z interfejsu API rozpoznawania twarzy za pośrednictwem wywołania zestawu SDK języka Python. Aby uzyskać więcej szczegółowych informacji na temat interfejsu API, zapoznaj się z instrukcjami i [dokumentacją interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related-topics"></a>Tematy pokrewne

- [Wprowadzenie do interfejsu API rozpoznawania twarzy w języku CSharp](FaceAPIinCSharpTutorial.md)
- [Wprowadzenie do interfejsu API rozpoznawania twarzy w języku Java dla systemu Android](FaceAPIinJavaForAndroidTutorial.md)
