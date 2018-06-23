---
title: Wykrywanie anomalii aplikacji Python - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Poznaj notesu Python, który używa interfejsu API wykrywania anomalii w kognitywnych usług firmy Microsoft. Wyślij oryginalnego punkty danych do interfejsu API i uzyskać oczekiwaną wartością i punkty anomalii.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: d35f41ddab21aa155376ad52ff4084298dab8fc5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348576"
---
# <a name="anomaly-detection-python-application"></a>Aplikacji Python wykrywania anomalii

Samouczek przedstawia sposób użycia interfejsu API wykrywania anomalii w języku Python i wizualizować wyniki za pomocą popularnych bibliotek. Przy użyciu oprogramowania Jupyter do uruchamiania tego samouczka i danych użytkownika w trakcie kluczem subskrypcji. Aby dowiedzieć się, jak rozpocząć pracę z notesów Jupyter interaktywnego, zajrzyj do [dokumentacji Jupyter](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subskrybowanie wykrywania anomalii i uzyskiwanie klucza subskrypcji 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>Pobierz przykładowy kod

1. Przejdź do [notesu samouczek w witrynie Github](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Kliknij zielony przycisk do klonowania lub pobrać samouczka. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Otwierania samouczek notesu Jupyter

1. Otwórz wiersz polecenia i przejdź do folderu python próbki.
2. Uruchom polecenie notesu Jupyter z wiersza polecenia, w którym zostanie uruchomiona Jupyter.
3. W oknie Jupyter kliknij <em>Example.ipynb interfejsu API wykrywania anomalii</em> otworzyć samouczek notesu.   

## <a name="running-the-tutorial"></a>Z tego samouczka

Aby użyć tego notesu, konieczne będzie klucza subskrypcji dla interfejsu API wykrywania anomalii. Odwiedź stronę subskrypcji, aby utworzyć konto. Na stronie "Logowanie" Użyj konta Microsoft do logowania i będzie można zasubskrybować w celu uzyskania kluczy. Po zakończeniu procesu rejestracji, Wklej klucz w sekcji zmiennych notesu (przedstawionym poniżej). Podstawowy lub pomocniczy klucz działa. Upewnij się, klucza, należy ująć w cudzysłów, aby było to ciąg.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
