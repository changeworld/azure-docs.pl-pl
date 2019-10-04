---
title: Pobierz zamierzenia z wywołaniem REST w języku Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e86d1e16e7c61f851a75ad97d2744b0daa009617
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838509"
---
## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3,6](https://www.python.org/downloads/) lub nowszy.
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Pobierz klucz LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent--programmatically"></a>Programowe pobieranie zamiarów

Możesz użyć języka Python, aby uzyskać dostęp do tych samych wyników w oknie przeglądarki w poprzednim kroku.

1. Skopiuj jeden z następujących fragmentów kodu do pliku o nazwie `quickstart-call-endpoint.py`:

    #### <a name="python-27tabp2"></a>[Python 2,7](#tab/P2)

    [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]    

    #### <a name="python-36tabp3"></a>[Python 3,6](#tab/P3)

    [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

    * * *

1. Zastąp wartość pola `Ocp-Apim-Subscription-Key` kluczem punktu końcowego LUIS.

1. Zainstaluj zależności z `pip install requests`.

1. Uruchom skrypt z `python ./quickstart-call-endpoint.py`. Wyświetla ten sam kod JSON, który został wyświetlony wcześniej w oknie przeglądarki.

## <a name="luis-keys"></a>Klucze LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start Zamknij projekt programu Visual Studio i Usuń katalog projektu z systemu plików. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie wyrażenia długości i uczenie się przy użyciu języka Python](../luis-get-started-python-add-utterance.md)