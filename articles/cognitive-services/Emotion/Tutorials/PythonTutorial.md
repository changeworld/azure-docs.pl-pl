---
title: Samouczek Python interfejsu API rozpoznawania emocji — warstwa | Dokumentacja firmy Microsoft
description: Umożliwia korzystanie z języka Python kognitywnych API rozpoznawania emocji — warstwa usług notesu Jupyter. Wizualizacja wyników przy użyciu popularnych bibliotek.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 70c8ca48c651601f3d7cbb3717c32bfe112176fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347549"
---
# <a name="emotion-api-using-python-tutorial"></a>Emocji interfejsu API przy użyciu języka Python — samouczek

> [!IMPORTANT]
> Interfejs API podglądu kończy się 30 października 2017 r. Testowanie nowego [wideo indeksatora interfejsu API w wersji zapoznawczej](https://azure.microsoft.com/services/cognitive-services/video-indexer/) można łatwo wyodrębnić szczegółowych informacji z wideo i celu ułatwienia pracy funkcję odnajdowania zawartości, takich jak wyniki wyszukiwania został określony poprzez wykrycie słowa rozmowy, kroje znaków i emocji. [Dowiedz się więcej](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Aby ułatwić szybkie wprowadzenie do interfejsu API rozpoznawania emocji — warstwa notesu Jupyter linki znajdują się poniżej przedstawiono sposób za pomocą interfejsu API w języku Python i wizualizować wyniki za pomocą niektórych popularnych bibliotek. 

[Łącze do notesu w serwisie GitHub](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>Za pomocą notesu Jupyter

Aby użyć notesu interaktywne, należy ją sklonować i uruchom go w oprogramowaniu Jupyter. Aby dowiedzieć się, jak rozpocząć pracę z notesów Jupyter interaktywne, postępuj zgodnie z instrukcjami w http://jupyter.readthedocs.org/en/latest/install.html. 

Aby użyć tego notesu, konieczne będzie klucza subskrypcji dla interfejsu API rozpoznawania emocji — warstwa. Odwiedź stronę [stronę subskrypcji](https://azure.microsoft.com/try/cognitive-services/) zasubskrybowanie. Na stronie "Logowanie" Użyj konta Microsoft do logowania i będzie można umożliwia subskrybowanie i uzyskać bezpłatne klucze. Po zakończeniu procesu rejestracji, Wklej klucz w sekcji zmiennych, pokazano poniżej. Podstawowy lub pomocniczy klucz działa.

```
Python Example 

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
