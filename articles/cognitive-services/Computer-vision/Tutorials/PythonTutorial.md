---
title: 'Samouczek: interfejs API przetwarzania obrazów dla języka Python'
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API przetwarzania obrazów za pomocą języka Python przy użyciu notesów Jupyter. Wyniki wizualizuj przy użyciu popularnych bibliotek.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: 59e88ecb253bc6da803ddf34a4a02229b99e259d
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981715"
---
# <a name="tutorial-computer-vision-api-python"></a>Samouczek: interfejs API przetwarzania obrazów dla języka Python

W tym samouczku pokazano, jak używać interfejsu API przetwarzania obrazów w języku Python oraz jak wizualizować wyniki za pomocą niektórych popularnych bibliotek. Użyj programu Jupyter do uruchomienia tego samouczka. Aby dowiedzieć się, jak zacząć korzystać z interakcyjnych notesów Jupyter, zapoznaj się z [dokumentacją programu Jupyter](http://jupyter.readthedocs.io/en/latest/index.html). 

### <a name="opening-the-tutorial-notebook-in-jupyter"></a>Otwieranie notesu samouczka w programie Jupyter 

1. Przejdź do [notesu samouczka w witrynie GitHub](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Kliknij zielony przycisk, aby sklonować lub pobrać samouczek. 
3. Otwórz wiersz polecenia i przejdź do folderu _Cognitive-Vision-Python-master\Jupyter Notebook_. 
4. Uruchom polecenie **jupyter notebook** z wiersza polecenia. Spowoduje to uruchomienie programu Jupyter.
5. W oknie programu Jupyter kliknij pozycję _Computer Vision API Example.ipynb_, aby otworzyć notes samouczka. 

### <a name="running-the-tutorial"></a>Uruchamianie samouczka

Aby użyć tego notesu, potrzebny będzie klucz subskrypcji dla interfejsu API przetwarzania obrazów. Odwiedź [stronę subskrypcji](https://azure.microsoft.com/try/cognitive-services/), aby się zarejestrować. Na stronie „Logowanie” użyj swojego konta Microsoft, aby się zalogować. Następnie aktywuj subskrypcję i uzyskaj bezpłatne klucze. Po zakończeniu procesu rejestracji wklej klucz w sekcji zmiennych notesu (przedstawionej poniżej). Możesz użyć klucza podstawowego lub pomocniczego. Pamiętaj o ujęciu klucza w cudzysłów, tak aby stał się ciągiem.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
