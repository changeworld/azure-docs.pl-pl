---
title: Pobieranie intencji, Python
titleSuffix: Language Understanding - Azure Cognitive Services
description: W tym przewodniku Szybki start przekażesz wypowiedzi do punktu końcowego aplikacji LUIS i uzyskasz intencje oraz jednostki.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 559b934194fa9aa160f4e7c5fe65610063181223
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276108"
---
# <a name="quickstart-get-intent-using-python"></a>Szybki start: pobieranie intencji przy użyciu języka Python
W tym przewodniku Szybki start przekażesz wypowiedzi do punktu końcowego aplikacji LUIS i uzyskasz intencje oraz jednostki.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko [Python 3.6](https://www.python.org/downloads/) lub nowsze.
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Pobieranie klucza usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Pobieranie intencji za pomocą przeglądarki

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent--programmatically"></a>Pobieranie intencji w sposób programistyczny

Aby uzyskać dostęp do tego samego wyniku, który został wyświetlony w oknie przeglądarki w poprzednim kroku, można użyć języka Python.

1. Skopiuj jeden z następujących fragmentów kodu do pliku o nazwie `quickstart-call-endpoint.py`:

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Zastąp wartość pola `Ocp-Apim-Subscription-Key` swoim kluczem punktu końcowego usługi LUIS.

3. Zainstaluj zależności przy użyciu polecenia `pip install requests`.

4. Uruchom skrypt przy użyciu polecenia `python ./quickstart-call-endpoint.py`. Wyświetli ona ten sam wynik JSON, który został wyświetlony wcześniej w oknie przeglądarki.

## <a name="luis-keys"></a>Klucze usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Usuń plik języka Python. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie wypowiedzi](luis-get-started-python-add-utterance.md)
