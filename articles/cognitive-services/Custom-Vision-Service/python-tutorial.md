---
title: Utwórz samouczek Custom Vision Service dla języka Python — Azure Cognitive Services | Dokumentacja firmy Microsoft
description: Zapoznaj się z podstawową aplikację języka Python, który używa niestandardowego interfejsu API przetwarzania w usługach Microsoft Cognitive Services. Utwórz projekt, dodać tagi, przekazywać obrazy, szkolenie projektu i przewiduje przy użyciu domyślnego punktu końcowego.
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: df0bdc0bbd2768566336323851f366c9ae280a88
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301603"
---
# <a name="custom-vision-api-python-tutorial"></a>Niestandardowy samouczek Vision API Python

Dowiedz się, jak utworzyć projekt Klasyfikacja obrazów przy użyciu usługi Custom Vision Service i podstawowe skrypt w języku Python. Po jego utworzeniu można można Dodawanie tagów, przekazywać obrazy, szkolenie projektu, pobrać projektu domyślne prognozowania — adres URL punktu końcowego i go używać do testowania programowo obrazu. Użyj w tym przykładzie typu open-source jako szablon do tworzenia własnych aplikacji przy użyciu interfejsu API usługi Custom Vision.



## <a name="prerequisites"></a>Wymagania wstępne

- Python 2.7 + lub Python 3.5 +.
- Narzędzie pip.

## <a name="get-the-training-and-prediction-keys"></a>Pobierz klucze uczenia i przewidywania

Aby uzyskać klucze używane w tym przykładzie, odwiedź stronę [strony sieci web Custom Vision](https://customvision.ai) i wybierz __ikonę koła zębatego__ w prawym górnym rogu. W __kont__ sekcji, skopiuj wartości z __klucz szkolenia__ i __klucz prognozowania__ pola.

![Obraz przedstawiający klucze interfejsu użytkownika](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Instalowanie zestawu SDK usługi Custom Vision

Aby Zainstaluj usługę Custom Vision Service SDK, użyj następującego polecenia:

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>Pobierz przykład obrazów

W tym przykładzie użyto obrazów z `Samples/Images` katalogu [ https://github.com/Microsoft/Cognitive-CustomVision-Windows ](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images) projektu. Klonuj lub Pobierz i Wyodrębnij projektu do swojego środowiska projektowego.

## <a name="create-a-custom-vision-service-project"></a>Tworzenie projektu usługi Custom Vision Service

Aby utworzyć nowy projekt usługi Custom Vision Service, Utwórz nowy plik o nazwie `sample.py`. Użyj poniższego kodu, jako zawartość pliku:

> [!IMPORTANT]
> Ustaw `training_key` wartość klucza szkolenia pobranym wcześniej.
>
> Ustaw `prediction_key` do przewidywania wartości klucza pobranym wcześniej.

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

Aby dodać znaczniki do swojego projektu, Dodaj następujący kod na końcu `sample.py` pliku:

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>Przekazywanie obrazów do projektu

Aby dodać przykładowe obrazy do projektu, Wstaw następujący kod do tworzenia tagu. Ten kod przekazuje obraz z odpowiednim tagu:

> [!IMPORTANT]
>
> Zmień ścieżkę, aby obrazy oparte na którego projekt Cognitive — CustomVision — Windows został pobrany wcześniej.

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

To w opracowywaniu klasyfikatora, Dodaj następujący kod na końcu `sample.py` pliku:

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

## <a name="get-and-use-the-default-prediction-endpoint"></a>I korzystaj z domyślnego punktu końcowego prognoz

Wysyłanie obrazu do endpoint prognoz i pobieranie prognozowania, Dodaj następujący kod na końcu `sample.py` pliku:

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

Uruchom rozwiązanie. Przewidywane wyniki są wyświetlane w konsoli.

```
python sample.py
```

Dane wyjściowe aplikacji będą podobne do następującego tekstu:

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