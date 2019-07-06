---
title: 'Szybki start: Wykrywanie i oznaczanie ramką twarzy na obrazie za pomocą zestawu SDK dla języka Python'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki Start utworzysz skrypt w języku Python, który używa interfejsu API rozpoznawania twarzy wykrywa i ramki twarzy na obrazie zdalnego.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: sbowles
ms.openlocfilehash: 741dd18a3b8da5e44d77c24d46adb8d550322281
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603297"
---
# <a name="quickstart-create-a-python-script-to-detect-and-frame-faces-in-an-image"></a>Szybki start: Tworzenie skryptu w języku Python w celu wykrywania i oznaczania ramką twarzy na obrazie

W tym przewodniku Szybki Start utworzysz skrypt w języku Python za pomocą interfejsu API do rozpoznawania twarzy z platformy Azure, za pomocą zestawu SDK języka Python, wykrywanie ludzkich twarzy na obrazie zdalnego. Aplikacja wyświetla wybrany obraz i rysuje ramkę wokół każdej wykrytej twarzy.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji interfejsu API rozpoznawania twarzy. Klucz subskrypcji bezpłatnej wersji próbnej możesz uzyskać na stronie [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Możesz też wykonać instrukcje z tematu [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Tworzenie konta usług Cognitive Services), aby subskrybować usługę interfejsu API rozpoznawania twarzy i uzyskać klucz.
- [Środowisko Python w wersji 2.7 lub nowszej albo 3.5 lub nowszej](https://www.python.org/downloads/)
- Narzędzie [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="get-the-face-sdk"></a>Pobieranie zestawu SDK interfejsu API rozpoznawania twarzy

Aby zainstalować zestaw SDK rozpoznawanie twarzy dla języka Python, otwórz wiersz polecenia i uruchom następujące polecenie:

```shell
pip install cognitive_face
```

## <a name="detect-faces-in-an-image"></a>Wykrywanie twarzy na obrazie

Utwórz nowy skrypt w języku Python o nazwie _FaceQuickstart.py_ i dodaj następujący kod. Ten kod obsługuje podstawowe funkcje wykrywania twarzy. Należy zastąpić element `<Subscription Key>` wartością klucza. Może być również konieczna zmiana wartości elementu `BASE_URL` w celu użycia poprawnego identyfikatora regionu dla klucza (zobacz [dokumentację interfejsu API rozpoznawania twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), aby zapoznać się z listą wszystkich punktów końcowych regionów). Klucze subskrypcji bezpłatnej wersji próbnej są generowane w regionie **westus**. Opcjonalnie możesz ustawić element `img_url` na adres URL dowolnego obrazu, którego chcesz użyć.

Skrypt wykryje twarze, wywołując metodę **cognitive_face.face.detect**, która opakowuje interfejs API REST [wykrywania](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) i zwraca listę twarzy.

```python
import cognitive_face as CF

# Replace with a valid subscription key (keeping the quotes in place).
KEY = '<Subscription Key>'
CF.Key.set(KEY)

# Replace with your regional Base URL
BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

### <a name="try-the-app"></a>Testowanie aplikacji

Uruchom aplikację za pomocą polecenia `python FaceQuickstart.py`. W oknie konsoli powinna pojawić się odpowiedź tekstowa podobna do poniższej:

```console
[{'faceId': '26d8face-9714-4f3e-bfa1-f19a7a7aa240', 'faceRectangle': {'top': 124, 'left': 459, 'width': 227, 'height': 227}}]
```

Dane wyjściowe reprezentuje listę wykryte twarze. Każdy element na liście to wystąpienie **dict**, w którym `faceId` jest unikatowym identyfikatorem wykrytej twarzy, a `faceRectangle` opisuje położenie wykrytej twarzy. 

> [!NOTE]
> Identyfikatory twarzy wygasają po upływie 24 godzin. Jeśli chcesz zachować dane twarzy długoterminowo, musisz jawnie je zapisać.

## <a name="draw-face-rectangles"></a>Rysowanie prostokątów twarzy

Używając współrzędnych otrzymanych w wyniku działania poprzedniego polecenia, możesz rysować na obrazie prostokąty reprezentujące wizualnie poszczególne twarze. Musisz zainstalować moduł Pillow (`pip install pillow`), aby móc korzystać z modułu Pillow Image Module. W górnej części pliku *FaceQuickstart.py* dodaj następujący kod:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Następnie dodaj poniższy kod w dolnej części skryptu. Ten kod tworzy prostą funkcję do analizowania współrzędnych prostokąta i używa poszewki Rysowanie prostokątów za na oryginalnego obrazu. Następnie wyświetla obraz w domyślnej przeglądarce obrazów.

```python
# Convert width height to a point in a rectangle


def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))


# Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

# For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

# Display the image in the users default image browser.
img.show()
```

## <a name="run-the-app"></a>Uruchamianie aplikacji

Najpierw może zostać wyświetlony monit o wybranie domyślnej przeglądarki. Następnie powinien zostać wyświetlony obraz podobny do poniższego. Powinny być również widoczne dane prostokąta wydrukowane w oknie konsoli.

![Młoda kobieta z czerwonym prostokątem narysowanym wokół twarzy](../images/face-rectangle-result.png)

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start przedstawiono podstawowy proces korzystania z zestawu SDK dla języka Python interfejsu API rozpoznawania twarzy oraz utworzono skrypt do wykrywania i oznaczania ramkami twarzy na obrazie. Następnie omówimy używanie zestawu SDK dla języka Python w bardziej złożonym przykładzie. Przejdź do przykładu Cognitive Face Python w witrynie GitHub, sklonuj go do folderu swojego projektu, a następnie postępuj zgodnie z instrukcjami w pliku _README.md_.

> [!div class="nextstepaction"]
> [Przykład Cognitive Face Python](https://github.com/Microsoft/Cognitive-Face-Python)
