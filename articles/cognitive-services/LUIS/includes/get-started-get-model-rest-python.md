---
title: 'Pobierz model z wywołaniem REST w C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/14/2020
ms.author: diberry
ms.openlocfilehash: 4d8da7d2bc51c4fc4ebc8d71f230f24f20b3aa24
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368467"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Zrozumienie języka platformy Azure — tworzenie zasobu 32 klucz znaków i tworzenie adresu URL punktu końcowego. Utwórz za pomocą [witryny Azure portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) lub [interfejsu wiersza polecenia platformy Azure](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Zaimportuj aplikację [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) z repozytorium GitHub ze zrozumieniem funkcji cognitive-services-language.
* Identyfikator aplikacji usługi LUIS dla zaimportowanej aplikacji TravelAgent. Identyfikator aplikacji jest wyświetlany na pulpicie nawigacyjnym aplikacji.
* Identyfikator wersji w aplikacji, w której odbierane są wypowiedzi. Domyślny identyfikator to „0.1”.
* Środowisko [Python 3.6](https://www.python.org/downloads/) lub nowsze.
* [Kod programu Visual Studio](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Plik JSON z przykładowymi wypowiedziami

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Programowo zmienianie modelu

1. Utwórz nowy plik o nazwie `model.py`. Dodaj następujący kod:

    [!code-python[Add example utterances to Language Understanding in python](~/samples-luis/documentation-samples/quickstarts/change-model/python/3.x/add-utterances-3-6.py)]

1. Zastąp `YOUR-` wartości zaczynające się od własnych wartości.

    |Informacje|Przeznaczenie|
    |--|--|
    |`YOUR-KEY`|Twój 32-znakowy klucz do tworzenia znaków.|
    |`YOUR-ENDPOINT`| Punkt końcowy adresu URL tworzenia. Na przykład `replace-with-your-resource-name.api.cognitive.microsoft.com`. Podczas tworzenia zasobu można ustawić nazwę zasobu.|
    |`YOUR-APP-ID`| Identyfikator aplikacji usługi LUIS. |

    Przypisane klucze i zasoby są widoczne w portalu usługi LUIS w sekcji Zarządzanie na stronie **Zasoby platformy Azure.** Identyfikator aplikacji jest dostępny w tej samej sekcji Zarządzanie na stronie **Ustawienia aplikacji.**

1. W wierszu polecenia w tym samym katalogu, w którym utworzono plik, wprowadź następujące polecenie, aby uruchomić plik:

    ```console
    python model.py
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu tego przewodnika Szybki start usuń plik z systemu plików.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najważniejsze wskazówki dotyczące aplikacji](../luis-concept-best-practices.md)
