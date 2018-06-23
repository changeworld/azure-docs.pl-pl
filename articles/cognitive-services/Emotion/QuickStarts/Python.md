---
title: Szybki start Python interfejsu API rozpoznawania emocji — warstwa | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu interfejsu API rozpoznawania emocji — warstwa języka Python w usługach kognitywnych.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/05/2018
ms.author: anroth
ms.openlocfilehash: ff1f6b2ddc872d0ee63d9885b04b1f007bc86e33
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347037"
---
# <a name="emotion-api-python-quickstart"></a>Szybki Start Python interfejsu API rozpoznawania emocji — warstwa

> [!IMPORTANT]
> Podgląd wideo interfejsu API upłynął 30 października 2017 r. Testowanie nowego [wideo indeksatora interfejsu API w wersji zapoznawczej](https://azure.microsoft.com/services/cognitive-services/video-indexer/) można łatwo wyodrębnić szczegółowych informacji z wideo i celu ułatwienia pracy funkcję odnajdowania zawartości, takich jak wyniki wyszukiwania został określony poprzez wykrycie słowa rozmowy, kroje znaków i emocji. [Dowiedz się więcej](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Ten przewodnik zawiera informacje i przykłady kodu, aby szybko rozpocząć korzystanie z [metody rozpoznaje interfejsu API rozpoznawania emocji — warstwa](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) języka Python do rozpoznawania emocji wyrażonych przez osoby na obrazie. 

W tym przykładzie można uruchomić jako notesu Jupyter na [MyBinder](https://mybinder.org) , klikając polecenie Uruchom integratora znaczków: [ ![integratora](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=EmotionAPI.ipynb)


## <a name="prerequisite"></a>Wymagania wstępne
Uzyskaj swój klucz bezpłatnej subskrypcji [tutaj](https://azure.microsoft.com/try/cognitive-services/)

## <a name="running-the-walkthrough"></a>Uruchomiona przewodnika
Aby kontynuować, z tym przewodnikiem, Zastąp `subscription_key` przy użyciu klucza interfejsu API uzyskanymi wcześniej.


```python
subscription_key = None
assert subscription_key
```

Następnie sprawdź, czy adres URL usługi odnosi się do regionu, używane podczas konfigurowania klucz interfejsu API. Jeśli używasz klucza wersji próbnej, nie trzeba wprowadzać żadnych zmian.


```python
emotion_recognition_url = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
```

W tym przewodniku zastosowano obrazów, które są przechowywane na dysku. Można również użyć obrazów, które są dostępne za pośrednictwem publicznie dostępny adres URL. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa).

Ponieważ dane obrazu są przekazywane jako część treści żądania, zwróć uwagę, należy ustawić `Content-Type` nagłówka do `application/octet-stream`. Jeśli obraz jest przekazywany za pomocą adresu URL, pamiętaj, aby ustawić nagłówka:
```python
header = {'Ocp-Apim-Subscription-Key': subscription_key }
```
Utwórz słownik zawierający adres URL:
```python
data = {'url': image_url}
```
i że w celu przekazania `requests` przy użyciu biblioteki:
```python
requests.post(emotion_recognition_url, headers=headers, json=image_data)
```

Należy najpierw pobrać kilka przykładowych obrazów z [API rozpoznawania emocji — warstwa](https://azure.microsoft.com/services/cognitive-services/emotion/) lokacji.


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



Zwrócony obiekt JSON zawiera obwiedni powierzchnie, które zostały rozpoznane wraz z wykrytym emocji. Każdego emocji jest skojarzony z wynikiem od 0 do 1, gdzie więcej świadczy o emocji niż niższa wynik jest więcej punktów. 

Następujące wiersze kodu wykrytych emocji na stronach za pomocą obrazu `matplotlib` biblioteki. Aby zwiększyć czytelność, wyświetlane są tylko górny emocji trzy.


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

`annotate_image` Funkcja wyświetlane dalej może służyć do nakładki emocji u góry pliku obrazu z podanej ścieżki w systemie plików. Jest on oparty na kod wywołanie interfejsu API rozpoznawania emocji — warstwa przedstawiona wcześniej.


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

Na koniec `annotate_image` funkcja może zostać wywołana w pliku obrazu, jak pokazano w następującym wierszu:


```python
annotate_image("images/emotion_2.jpg")
```
