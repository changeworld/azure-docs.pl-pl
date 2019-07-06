---
title: 'Szybki start: generowanie miniatur — REST, Python'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wygenerujesz miniaturę obrazu za pomocą interfejsu API przetwarzania obrazów przy użyciu języka Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5203467eadadf1180eabf7b5a51b113ae273ee7c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603539"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Szybki start: Generowanie miniatur przy użyciu interfejsu API Computer Vision REST i Python

W tym przewodniku Szybki start wygenerujesz miniaturę na podstawie obrazu, korzystając z interfejsu API REST przetwarzania obrazów. W przypadku metody [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) możesz określać żądaną wysokość i szerokość, a interfejs API przetwarzania obrazów wykorzystuje inteligentne przycinanie, aby określić obszar zainteresowania i na jego podstawie wygenerować współrzędne przycinania.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/try/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Możesz uzyskać bezpłatnej wersji próbnej klucza z [spróbuj usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Lub, postępuj zgodnie z instrukcjami w [Tworzenie konta usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) subskrybować przetwarzania obrazów, i Uzyskaj klucz.
- Edytor kodu, taki jak program [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="create-and-run-the-sample"></a>Tworzenie i uruchamianie przykładowego kodu

Aby utworzyć i uruchomić przykład, skopiuj poniższy kod do edytora kodu. 

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the "westcentralus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

thumbnail_url = vision_base_url + "generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers,
                         params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Następnie wykonaj poniższe czynności:
1. Zastąp wartość `subscription_key` kluczem subskrypcji.
1. W razie potrzeby zastąp wartość `vision_base_url` adresem URL punktu końcowego dla zasobu funkcji przetwarzania obrazów w regionie świadczenia usługi Azure, z którego uzyskano klucze subskrypcji.
1. Opcjonalnie zastąp wartość `image_url` adresem URL innego obrazu, dla którego chcesz wygenerować miniaturę.
1. Zapisz kod jako plik z rozszerzeniem `.py`. Na przykład `get-thumbnail.py`.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Pomyślna odpowiedź jest zwracana jako dane binarne, które reprezentują dane obrazu miniatury. W przykładzie powinien zostać wyświetlony ten obraz. Jeśli żądanie zakończy się niepowodzeniem, odpowiedź zostanie wyświetlona w oknie wiersza polecenia. Powinna ona zawierać kod błędu.

## <a name="run-in-jupyter-optional"></a>Uruchamianie w aplikacji Jupyter (opcjonalnie)

Opcjonalnie możesz pracować z tym przewodnikiem Szybki start krok po kroku, korzystając z aplikacji Jupyter Notebook w usłudze [MyBinder](https://mybinder.org). Aby uruchomić usługę Binder, wybierz poniższy przycisk:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Kolejne kroki

Następnie zapoznaj się z bardziej szczegółowymi informacjami o funkcji generowania miniatur.

> [!div class="nextstepaction"]
> [Generowanie miniatur](../concept-generating-thumbnails.md)
