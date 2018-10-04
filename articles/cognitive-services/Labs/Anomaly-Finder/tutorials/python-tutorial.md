---
title: Wykrywanie anomalii aplikacji języka Python — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
description: Zapoznaj się z notesu Python, który używa interfejsu API wykrywania anomalii w usługach Microsoft Cognitive Services. Wyślij oryginalnego punkty danych do interfejsu API i uzyskać oczekiwaną wartość i anomalii punktów.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 87cd9e976d231291ad13acecf188cfd668d692b6
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248231"
---
# <a name="anomaly-detection-python-application"></a>Aplikację w języku Python wykrywania anomalii

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Samouczek pokazuje, jak używać interfejsu API wykrywania anomalii w języku Python oraz sposób wizualizować wyniki za pomocą popularnych bibliotek. Używanie aplikacji Jupyter do uruchamiania tego samouczka i podjęcie próby własnych danych, z kluczem subskrypcji. Aby dowiedzieć się, jak rozpocząć pracę z interaktywnego notesów programu Jupyter, zapoznaj się [dokumentacji Jupyter](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subskrybowanie do wykrywania anomalii i Uzyskaj klucz subskrypcji 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>Pobierz przykładowy kod

1. Przejdź do [samouczek Notes w usłudze Github](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Kliknij zielony przycisk Klonuj lub Pobierz samouczek. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Otwieranie notesu samouczków w programie Jupyter

1. Otwórz wiersz polecenia i przejdź do folderu python-sample.
2. Uruchom polecenie notesu Jupyter z wiersza polecenia, w którym zostanie uruchomiona Jupyter.
3. W oknie programu Jupyter, kliknij pozycję <em>Example.ipynb interfejsu API wykrywania anomalii</em> aby otworzyć notes samouczka.   

## <a name="running-the-tutorial"></a>Wykonywania instrukcji samouczka

Aby użyć tego notesu, konieczne będzie klucz subskrypcji dla interfejsu API wykrywania anomalii. Odwiedź stronę subskrypcji, aby się zarejestrować. Na stronie "zwrotu Sign in" Użyj swojego konta Microsoft, aby zalogować się i będzie mógł subskrybować i Uzyskaj klucze. Po zakończeniu procesu rejestracji, Wklej swój klucz do sekcji zmiennych Notes (przedstawionym poniżej). Podstawowy lub pomocniczy klucz działa. Upewnij się, klucza, należy ująć w cudzysłów, aby stał się ciągiem.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
