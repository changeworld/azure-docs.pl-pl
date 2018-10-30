---
title: 'Samouczek: tworzenie projektu klasyfikacji obrazów przy użyciu zestawu Custom Vision SDK dla języka Python'
titlesuffix: Azure Cognitive Services
description: Utwórz projekt, dodaj tagi, prześlij obrazy, wyszkol projekt i wykonaj przewidywanie przy użyciu domyślnego punktu końcowego.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: 96125ba1c54f742bb9ddf32a1588173217be0766
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49953116"
---
# <a name="tutorial-create-an-image-classification-project-with-the-custom-vision-sdk-for-python"></a>Samouczek: tworzenie projektu klasyfikacji obrazów przy użyciu zestawu Custom Vision SDK dla języka Python

Dowiedz się, jak utworzyć projekt klasyfikacji obrazów za pomocą usługi Custom Vision Service i prostego skryptu języka Python. Po jego utworzeniu możesz dodać tagi, przesłać obrazy, wyszkolić projekt, uzyskać adres URL domyślnego punktu końcowego przewidywania projektu i użyć go do programowego przetestowania obrazu. Ta przykładowa aplikacja open source może posłużyć jako szablon do utworzenia własnej aplikacji przy użyciu interfejsu API Custom Vision.



## <a name="prerequisites"></a>Wymagania wstępne

- Python 2.7+ lub Python 3.5 +.
- Narzędzie pip.

## <a name="get-the-training-and-prediction-keys"></a>Uzyskanie kluczy szkoleniowego i predykcyjnego

Aby uzyskać klucze używane w tym przykładzie, przejdź na [stronę Custom Vision](https://customvision.ai) i wybierz __ikonę koła zębatego__ w prawym górnym rogu. W sekcji __Accounts__ (Konta) skopiuj wartości z pól __Training Key__ (Klucz szkoleniowy) i __Prediction Key__ (Klucz predykcyjny).

![Obraz interfejsu użytkownika do uzyskiwania kluczy](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Zainstalowanie pakietu SDK Custom Vision Service

Aby zainstalować usługę Custom Vision Service SDK, użyj następującego polecenia:

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>Pobranie przykładowych obrazów

W tym przykładzie użyto obrazów z katalogu `Samples/Images` projektu [ https://github.com/Microsoft/Cognitive-CustomVision-Windows ](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images). Sklonuj lub pobierz i wyodrębnij projekt do swojego środowiska projektowego.

## <a name="create-a-custom-vision-service-project"></a>Utworzenie projektu Custom Vision Service

Aby utworzyć nowy projekt usługi Custom Vision Service, utwórz nowy plik o nazwie `sample.py`. Użyj poniższego kodu jako zawartości pliku:

> [!IMPORTANT]
> Ustaw `training_key` na wartość uzyskanego wcześniej klucza szkoleniowego.
>
> Ustaw `prediction_key` na wartość uzyskanego wcześniej klucza do predykcyjnego.

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

## <a name="add-tags-to-your-project"></a>Dodanie tagów do projektu

Aby dodać tagi do projektu, wstaw następujący kod na końcu pliku `sample.py`:

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>Przesłanie obrazów do projektu

Aby dodać przykładowe obrazy do projektu, po utworzeniu tagów wstaw następujący kod. Ten kod przesyła obraz z odpowiednim tagiem:

> [!IMPORTANT]
>
> Zmień ścieżkę do obrazów odpowiednio do lokalizacji, do której wcześniej pobrano projekt Cognitive-CustomVision-Windows.

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

Aby wyszkolić klasyfikator, dodaj na końcu pliku `sample.py` poniższy kod:

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

## <a name="get-and-use-the-default-prediction-endpoint"></a>Uzyskanie domyślnego punktu końcowego do przewidywania i użycie go

Aby uzyskać obraz do punktu końcowego przewidywania i uzyskać przewidywanie, dodaj na końcu pliku `sample.py` następujący kod:

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

## <a name="run-the-example"></a>Uruchomienie przykładu

Uruchom rozwiązanie. W konsoli zostaną wyświetlone wyniki przewidywania.

```
python sample.py
```

Dane wyjściowe aplikacji będą podobne do poniższego przykładu:

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