---
title: 'Szybki start: Rozpoznawanie emocji na twarzach na obrazie — interfejs API rozpoznawania emocji, Python'
description: Uzyskaj informacje oraz przykłady kodu w celu szybkiego rozpoczęcia korzystania z interfejsu API rozpoznawania emocji z językiem Python.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 02/05/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: bd267d40fe1f5a673f1d6b98b301665690b12f31
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233476"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Szybki start: Tworzenie aplikacji do rozpoznawania emocji na twarzach na obrazie.

> [!IMPORTANT]
> Interfejs API rozpoznawania emocji zostanie wycofany w dniu 15 lutego 2019 r. Rozpoznawanie emocji jest teraz ogólnie dostępne jako część [interfejsu API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/). 

Ten przewodnik zawiera informacje i przykłady kodu, które pomogą w szybkim rozpoczęciu korzystania z [metody Recognize interfejsu API rozpoznawania emocji](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) z językiem Python do rozpoznawania emocji wyrażonych przez co najmniej jedną osobę na obrazie.

Ten przykład można uruchomić jako notes programu Jupyter w witrynie [MyBinder](https://mybinder.org). W tym celu należy kliknąć ikonę Launch Binder: ![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=EmotionAPI.ipynb)


## <a name="prerequisite"></a>Wymagania wstępne
Pobierz swój bezpłatny klucz subskrypcji [stąd](https://azure.microsoft.com/try/cognitive-services/)

## <a name="running-the-walkthrough"></a>Uruchamianie przewodnika
Aby kontynuować pracę z tym przewodnikiem, zastąp element `subscription_key` uzyskanym wcześniej kluczem interfejsu API.


```python
subscription_key = None
assert subscription_key
```

Następnie upewnij się, że adres URL usługi jest zgodny z regionem użytym podczas konfigurowania klucza interfejsu API. Jeśli używasz klucza wersji próbnej, nie musisz wprowadzać żadnych zmian.


```python
emotion_recognition_url = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
```

Obrazy używane w tym przewodniku są przechowywane na dysku. Można również używać obrazów dostępnych za pośrednictwem publicznego adresu URL. Więcej informacji zawiera [dokumentacja interfejsu API REST](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa).

Ponieważ dane obrazu są przekazywane jako część treści żądania, nagłówek `Content-Type` musisz ustawić na wartość `application/octet-stream`. Jeśli przekazujesz obraz za pośrednictwem adresu URL, pamiętaj, aby ustawić nagłówek na wartość:
```python
header = {'Ocp-Apim-Subscription-Key': subscription_key }
```
utworzyć słownik zawierający adres URL:
```python
data = {'url': image_url}
```
i przekazać ten element do biblioteki `requests` przy użyciu kodu:
```python
requests.post(emotion_recognition_url, headers=headers, json=image_data)
```

Najpierw pobierz kilka przykładowych obrazów z witryny [interfejsu API rozpoznawania emocji](https://azure.microsoft.com/services/cognitive-services/emotion/).


```bash
%%bash
mkdir -p images
curl -Ls https://aka.ms/csnb-emotion-1 -o images/emotion_1.jpg
curl -Ls https://aka.ms/csnb-emotion-2 -o images/emotion_2.jpg
```


```python
image_path = "images/emotion_1.jpg"
image_data = open(image_path, "rb").read()
```


```python
import requests
headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
response.raise_for_status()
analysis = response.json()
analysis
```




    [{'faceRectangle': {'height': 162, 'left': 130, 'top': 141, 'width': 162},
      'scores': {'anger': 9.29041e-06,
       'contempt': 0.000118981574,
       'disgust': 3.15619363e-05,
       'fear': 0.000589638,
       'happiness': 0.06630674,
       'neutral': 0.00555004273,
       'sadness': 7.44669524e-06,
       'surprise': 0.9273863}}]



Zwrócony obiekt JSON zawiera pola ograniczenia rozpoznanych twarzy wraz z wykrytymi emocjami. Poszczególne emocje są skojarzone z wynikami z zakresu od 0 do 1, przy czym większa wartość oznacza większą pewność występowania danej emocji.

Następujące wiersze kodu umożliwiają wykrywanie emocji na twarzach na obrazie za pomocą biblioteki `matplotlib`. Aby zwiększyć czytelność, przedstawiono kod dotyczący wykrywania tylko trzech emocji.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Rectangle
from io import BytesIO
from PIL import Image

plt.figure(figsize=(5,5))

image  = Image.open(BytesIO(image_data))
ax     = plt.imshow(image, alpha=0.6)

for face in analysis:
    fr = face["faceRectangle"]
    em = face["scores"]
    origin = (fr["left"], fr["top"])
    p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
    ax.axes.add_patch(p)
    ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
    plt.text(origin[0], origin[1], ct, fontsize=20)    
_ = plt.axis("off")
```

Funkcja `annotate_image` może zostać użyta do nałożenia emocji na obraz o podanej ścieżce w systemie plików. Jest ona oparta na wcześniejszym kodzie służącym do wywoływania interfejsu API rozpoznawania emocji.


```python
def annotate_image(image_path):    
    image_data = open(image_path, "rb").read()
    headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
    response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
    response.raise_for_status()
    analysis = response.json()

    plt.figure()

    image  = Image.open(image_path)
    ax     = plt.imshow(image, alpha=0.6)

    for face in analysis:
        fr = face["faceRectangle"]
        em = face["scores"]
        origin = (fr["left"], fr["top"])
        p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
        ax.axes.add_patch(p)
        ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
        plt.text(origin[0], origin[1], ct, fontsize=20, va="bottom")    
    _ = plt.axis("off")
```

Na koniec funkcję `annotate_image` można wywołać dla pliku obrazu, co pokazano poniżej:


```python
annotate_image("images/emotion_2.jpg")
```
