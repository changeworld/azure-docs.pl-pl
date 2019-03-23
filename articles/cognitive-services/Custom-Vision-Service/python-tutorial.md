---
title: 'Szybki start: Tworzenie projektu klasyfikacji obrazów przy użyciu zestawu Custom Vision SDK dla języka Python'
titlesuffix: Azure Cognitive Services
description: Utwórz projekt, dodaj tagi, przekaż obrazy, wyszkol projekt i wykonaj przewidywanie przy użyciu zestawu SDK dla języka Python.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: 43c9e155a16ab5d1cc907ecd849ef109f2507782
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351698"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>Szybki start: Tworzenie projektu klasyfikacji obrazów przy użyciu zestawu Custom Vision SDK języka Python

Ten artykuł zawiera informacje i przykładowy kod, dzięki którym można łatwiej rozpocząć tworzenie modeli klasyfikacji obrazów za pomocą zestawu Custom Vision SDK i języka Python. Po jej utworzeniu możesz dodać tagi, przesłać obrazy, wyszkolić projekt, uzyskać adres URL punktu końcowego domyślnego przewidywania projektu i użyć punktu końcowego do programowego przetestowania obrazu. Użyj tego przykładu jako szablonu do utworzenia własnej aplikacji języka Python. Jeśli chcesz przejść przez proces tworzenia i używania modelu klasyfikacji _bez_ kodu, zobacz zamiast tego [wskazówki dotyczące przeglądarki](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Wymagania wstępne

- [Środowisko Python w wersji 2.7 lub nowszej albo 3.5 lub nowszej](https://www.python.org/downloads/)
- Narzędzie [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="install-the-custom-vision-sdk"></a>Instalowanie zestawu Custom Vision SDK

Aby zainstalować zestaw Custom Vision Service SDK dla języka Python, uruchom następujące polecenie w programie PowerShell:

```PowerShell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]


## <a name="add-the-code"></a>Dodawanie kodu

Utwórz nowy plik o nazwie *sample.py* w preferowanym katalogu projektu.

### <a name="create-the-custom-vision-service-project"></a>Tworzenie projektu Custom Vision Service

Dodaj następujący kod do skryptu, aby utworzyć nowy projekt Custom Vision Service. Wstaw klucze subskrypcji w odpowiednich definicjach.

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry

ENDPOINT = "https://southcentralus.api.cognitive.microsoft.com"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>Tworzenie tagów w projekcie

Aby utworzyć tagi klasyfikacji dla projektu, dodaj następujący kod na końcu pliku *sample.py*:

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>Przekazywanie i tagowanie obrazów

Aby dodać przykładowe obrazy do projektu, po utworzeniu tagów wstaw następujący kod. Ten kod przekazuje każdy obraz z odpowiednim tagiem. Należy wprowadzić bazową ścieżkę adresu URL obrazów, zależnie od tego, gdzie został pobrany projekt z przykładami dotyczącymi zestawu SDK dla języka Python dla usług Cognitive Services.

> [!NOTE]
> Należy zmienić ścieżkę do obrazów, zależnie od tego, gdzie został wcześniej pobrany projekt z przykładami dotyczącymi zestawu SDK dla języka Python dla usług Cognitive Services.

```Python
base_image_url = "<path to project>"

print("Adding images...")

image_entry = lambda image_path, tag_id: ImageFileCreateEntry(
    name=image_path.split("/")[-1], contents=image_path, tag_ids=[tag_id]
)

image_list = [
    image_entry(
        base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num),
        hemlock_tag.id,
    )
    for image_num in range(1, 10)
] + [
    image_entry(
        base_image_url
        + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num),
        cherry_tag.id,
    )
    for image_num in range(1, 10)
]

trainer.create_images_from_files(project.id, images=image_list)
```

### <a name="train-the-classifier"></a>Szkolenie klasyfikatora

Ten kod tworzy pierwszą iterację w projekcie i oznacza ją jako domyślną. Iteracja domyślna odzwierciedla wersję modelu, która będzie odpowiadać na żądania przewidywania. Należy ją zaktualizować przy każdym ponownym szkoleniu modelu.

```Python
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

### <a name="get-and-use-the-default-prediction-endpoint"></a>Uzyskanie domyślnego punktu końcowego do przewidywania i użycie go

Aby wysłać obraz do punktu końcowego przewidywania i uzyskać przewidywanie, dodaj na końcu pliku następujący kod:

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction

predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom plik *sample.py*.

```PowerShell
python sample.py
```

Dane wyjściowe aplikacji powinny być podobne do poniższego tekstu:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Możesz następnie sprawdzić, czy obraz testowy (znajdujący się w folderze **<bazowy_adres_url_obrazów>/Images/Test/**) został odpowiednio otagowany. Możesz też wrócić do [witryny internetowej Custom Vision](https://customvision.ai) i wyświetlić bieżący stan nowo utworzonego projektu.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Kolejne kroki

Teraz wiesz już, jak wykonać każdy krok procesu klasyfikacji obrazów przy użyciu kodu. W tym przykładzie jest wykonywana jedna iteracja szkolenia, ale często trzeba szkolić i testować model wiele razy, aby zwiększyć jego dokładność.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)
