---
title: wykonywanie operacji na obrazach — Python
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API przetwarzania obrazów za pomocą języka Python przy użyciu notesów Jupyter. Wyniki wizualizuj przy użyciu popularnych bibliotek.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: b18f41db772c1c214bdf48c9bb765b1ce7e6d2c2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604156"
---
# <a name="computer-vision-api-jupyter-notebook"></a>Notes Jupyter interfejsu API przetwarzania komputera

Ten przewodnik pokazuje, jak używać interfejsu API przetwarzania obrazów w języku Python oraz sposób wizualizować wyniki za pomocą popularnych bibliotek. Do uruchomienia tego samouczka użyjesz programu Jupyter. Aby dowiedzieć się, jak zacząć korzystać z interakcyjnych notesów Jupyter, zapoznaj się z [dokumentacją programu Jupyter](https://jupyter.readthedocs.io/en/latest/index.html).

## <a name="prerequisites"></a>Wymagania wstępne

- [Środowisko Python w wersji 2.7 lub nowszej albo 3.5 lub nowszej](https://www.python.org/downloads/)
- Narzędzie [pip](https://pip.pypa.io/en/stable/installing/)
- Zainstalowana aplikacja [Jupyter Notebook](https://jupyter.org/install)

## <a name="open-the-notebook-in-jupyter"></a>Otworzyć notes w programie Jupyter 

1. Przejdź do repozytorium GitHub [Cognitive Vision Python](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Kliknij zielony przycisk, aby sklonować lub pobrać repozytorium. 
3. Otwórz wiersz polecenia i przejdź do folderu **Cognitive-Vision-Python\Jupyter Notebook**.
1. Upewnij się, że masz wszystkie wymagane biblioteki, uruchamiając polecenie `pip install requests opencv-python numpy matplotlib` w wierszu polecenia.
1. Uruchom program Jupyter, uruchamiając polecenie `jupyter notebook` w wierszu polecenia.
1. W oknie programu Jupyter kliknij pozycję _Computer Vision API Example.ipynb_, aby otworzyć notes samouczka.

## <a name="run-the-notebook"></a>Uruchamianie notesu

Aby użyć tego notesu, potrzebny będzie klucz subskrypcji dla interfejsu API przetwarzania obrazów. Odwiedź [stronę subskrypcji](https://azure.microsoft.com/try/cognitive-services/), aby się zarejestrować. Na stronie **Logowanie** zaloguj się przy użyciu swojego konta Microsoft. Następnie aktywuj subskrypcję i uzyskaj bezpłatne klucze. Po zakończeniu procesu rejestracji wklej klucz w sekcji `Variables` notesu (przedstawionej poniżej). Działają oba klucze, podstawowy i pomocniczy. Pamiętaj o ujęciu klucza w cudzysłów, tak aby stał się ciągiem.

Należy również upewnić się, że wartość w polu `_region` jest zgodna z regionem odpowiadającym subskrypcji.

```python
# Variables
_region = 'westcentralus'  # Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(
    _region)
_key = None  # Here you have to paste your primary key
_maxNumRetries = 10
```

Po uruchomieniu tego samouczka będzie można dodać obrazy do analizy, zarówno z adresu URL, jak i z magazynu lokalnego. Skrypt wyświetli obrazy i informacje o analizie w notesie.
