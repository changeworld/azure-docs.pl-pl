---
title: Samouczek Python interfejsu API przetwarzania obrazów komputera | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą interfejsu API przetwarzania obrazów komputera Python za pomocą notesów Jupyter w kognitywnych usług firmy Microsoft. Wizualizacja wyników przy użyciu popularnych bibliotek.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: a093c2d066e70a8daf1fe1cd33ccf794ecb196af
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347536"
---
# <a name="computer-vision-api-python-tutorial"></a>Samouczek Python interfejsu API wizji komputera

Ten samouczek przedstawia sposób za pomocą interfejsu API przetwarzania obrazów komputera w języku Python i wizualizować wyniki za pomocą niektórych popularnych bibliotek. Użyj Jupyter do uruchamiania w samouczku. Aby dowiedzieć się, jak rozpocząć pracę z notesów Jupyter interaktywne, dotyczą: [Jupyter Documementation](http://jupyter.readthedocs.io/en/latest/index.html). 

### <a name="opening-the-tutorial-notebook-in-jupyter"></a>Otwierania samouczek notesu Jupyter 

1. Przejdź do [notesu samouczek w witrynie GitHub](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Kliknij zielony przycisk do klonowania lub pobrać samouczka. 
3. Otwórz wiersz polecenia i przejdź do folderu _notesu Cognitive-wizji-Python-master\Jupyter_. 
4. Uruchom polecenie **notesu jupyter** z wiersza polecenia. Spowoduje to uruchomienie Jupyter.
5. W oknie Jupyter kliknij _Example.ipynb interfejsu API przetwarzania obrazów komputera_ otworzyć samouczek notesu 

### <a name="running-the-tutorial"></a>Z tego samouczka

Aby użyć tego notesu, konieczne będzie klucza subskrypcji dla interfejsu API przetwarzania obrazów komputera. Odwiedź stronę [stronę subskrypcji](https://azure.microsoft.com/try/cognitive-services/) zasubskrybowanie. Na stronie "Logowanie" Użyj konta Microsoft do logowania i będzie można umożliwia subskrybowanie i uzyskać bezpłatne klucze. Po zakończeniu procesu rejestracji, Wklej klucz w sekcji zmiennych notesu (przedstawionym poniżej). Podstawowy lub pomocniczy klucz działa. Upewnij się, klucza, należy ująć w cudzysłów, aby było to ciąg.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
