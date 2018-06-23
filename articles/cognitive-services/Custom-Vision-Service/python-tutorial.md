---
title: Tworzenie samouczek niestandardowy wizji usługi Python — kognitywnych usług Azure | Dokumentacja firmy Microsoft
description: Poznaj podstawowe aplikacji Python, wykorzystujący interfejs API wizji niestandardowe w kognitywnych usług firmy Microsoft. Utwórz projekt, Dodaj tagi przekazywanie obrazów, uczenia projektu i prognozowanie przy użyciu domyślnego punktu końcowego.
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/07/2018
ms.author: areddish
ms.openlocfilehash: 0359935bf266d4f2a5cf845dd0d23183f4f77b72
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348141"
---
# <a name="custom-vision-api-python-tutorial"></a>Niestandardowy samouczek wizji interfejsu API w języku Python

Dowiedz się, jak utworzyć projekt klasyfikacji obrazu z usługą wizji niestandardowe i podstawowe skrypt w języku Python. Po jego utworzeniu zostanie można Dodawanie tagów, przekazywanie obrazów uczenia projektu, uzyskać URL punktu końcowego prognozowania domyślnego projektu i użyć go do testowania programistyczne obrazu. Użyj w tym przykładzie open source jako szablon do tworzenia własnych aplikacji przy użyciu interfejsu API przetwarzania obrazów niestandardowych.



## <a name="prerequisites"></a>Wymagania wstępne

- Python 2.7 + lub Python 3.5 +.
- Narzędzie pip.

## <a name="get-the-training-and-prediction-keys"></a>Pobieranie kluczy szkolenia i prognozowanie

Aby uzyskać klucze używane w tym przykładzie, odwiedź stronę [wizji niestandardowe strony sieci web](https://customvision.ai) i wybierz __koło zębate ikonę__ w prawym górnym rogu. W __kont__ sekcji, skopiuj wartości z __klucza szkolenia__ i __klucza prognozowania__ pola.

![Obraz kluczy interfejsu użytkownika](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Zainstaluj usługę wizji niestandardowego zestawu SDK

Aby zainstalować zestaw SDK usług wizji niestandardowe, użyj następującego polecenia:

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>Pobierz przykład obrazów

W tym przykładzie użyto obrazów z `Samples/Images` katalog [ https://github.com/Microsoft/Cognitive-CustomVision-Windows ](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images) projektu. Klonuj lub pobierać i wyodrębniać projektu w środowisku deweloperskim.

## <a name="create-a-custom-vision-service-project"></a>Tworzenie projektu usługi wizji niestandardowe

Aby utworzyć nowy projekt usługi wizji niestandardowe, Utwórz nowy plik o nazwie `sample.py`. Zawartość pliku, należy użyć poniższego kodu:

> [!IMPORTANT]
> Ustaw `training_key` wartość klucza szkolenia pobranym wcześniej.
>
> Ustaw `prediction_key` do prognozowania wartości klucza został wcześniej pobrany.

```python
from azure.cognitiveservices.vision.customvision.training import training_api
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = training_api.TrainingApi(training_key)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Project")
```

## <a name="add-tags-to-your-project"></a>Dodawanie tagów do projektu

Aby dodać tagi do projektu, Dodaj następujący kod na końcu `sample.py` pliku:

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>Przekaż obrazy do projektu

Aby dodać przykładowe obrazy do projektu, Wstaw następujący kod po utworzeniu tagu. Ten kod przekazuje obraz z odpowiedniego tagu:

> [!IMPORTANT]
>
> Zmienianie ścieżki do obrazów, według którego pobrano projektu Cognitive CustomVision Windows wcześniej.

```python
base_image_url = "https://raw.githubusercontent.com/Microsoft/Cognitive-CustomVision-Windows/master/Samples/"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])


# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following:
#
#import os
#hemlock_dir = "Images\\Hemlock"
#for image in os.listdir(os.fsencode("Images\\Hemlock")):
#    with open(hemlock_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ hemlock_tag.id ])
#
#cherry_dir = "Images\\Japanese Cherry"
#for image in os.listdir(os.fsencode("Images\\Japanese Cherry")):
#    with open(cherry_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ cherry_tag.id ])
```

## <a name="train-the-project"></a>Szkolenie projektu

W celu przeszkolenia klasyfikatora, Dodaj następujący kod na końcu `sample.py` pliku:

```python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Make it the default project endpoint
trainer.update_iteration(project.id, iteration.id, is_default=True)
print ("Done!")
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>Pobierz i użyj domyślnego punktu końcowego prognozowania

Wysyłanie obrazu do punktu końcowego prognozowania i pobrać prognozowania, Dodaj następujący kod na końcu `sample.py` pliku:

```python
from azure.cognitiveservices.vision.customvision.prediction import prediction_endpoint
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = prediction_endpoint.PredictionEndpoint(prediction_key)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following.
#
# Open the sample image and get back the prediction results.
# with open("Images\\test\\test_image.jpg", mode="rb") as test_data:
#     results = predictor.predict_image(project.id, test_data, iteration.id)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-example"></a>Uruchomić przykład

Uruchom rozwiązanie. Wyniki prognozowania są wyświetlane na konsoli.

```
python sample.py
```

Dane wyjściowe aplikacji jest podobny do następującego tekstu:

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```