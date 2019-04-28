---
title: 'Samouczek: wykrywanie anomalii, Python'
titlesuffix: Azure Cognitive Services
description: Zapoznaj się z notesem języka Python, który używa interfejsu API wykrywania anomalii. Wyślij oryginalne punkty danych do interfejsu API i uzyskaj oczekiwaną wartość oraz punkty anomalii.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 5a2fb54658599e0500944aaae9225f314277f9da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60838389"
---
# <a name="tutorial-anomaly-detection-with-python-application"></a>Samouczek: wykrywanie anomalii przy użyciu aplikacji języka Python

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

W samouczku pokazano, jak używać interfejsu API wykrywania anomalii w języku Python oraz jak wizualizować wyniki za pomocą popularnych bibliotek. Uruchomisz samouczek przy użyciu programu Jupyter oraz spróbujesz użyć własnych danych z kluczem subskrypcji. Aby dowiedzieć się, jak zacząć korzystać z interakcyjnych notesów Jupyter, zapoznaj się z [dokumentacją programu Jupyter](https://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subskrybowanie wykrywania anomalii i uzyskiwanie klucza subskrypcji 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>Pobieranie przykładowego kodu

1. Przejdź do [notesu samouczka w witrynie GitHub](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Kliknij zielony przycisk, aby sklonować lub pobrać samouczek. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Otwieranie notesu samouczka w programie Jupyter

1. Otwórz wiersz polecenia i przejdź do folderu python-sample.
2. Uruchom notes Jupyter w wierszu polecenia, co spowoduje uruchomienie programu Jupyter.
3. W oknie programu Jupyter kliknij pozycję <em>Anomaly Detection API Example.ipynb</em>, aby otworzyć notes samouczka.   

## <a name="running-the-tutorial"></a>Uruchamianie samouczka

Do użycia tego notesu jest potrzebny klucz subskrypcji dla interfejsu API wykrywania anomalii. Odwiedź stronę subskrypcji, aby się zarejestrować. Na stronie „Rejestrowanie” zaloguj się przy użyciu swojego konta Microsoft, aby móc skorzystać z subskrypcji i uzyskać klucze. Po zakończeniu procesu rejestracji wklej klucz w sekcji zmiennych notesu (przedstawionej poniżej). Działają oba klucze, podstawowy i pomocniczy. Pamiętaj o ujęciu klucza w cudzysłów, tak aby stał się ciągiem.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
