---
title: Pobierz zamierzenia z wywołaniem REST w programie Node. js
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: a6dfe21cd92c5bf5580d7b121f33f68fb4e135fa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838525"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Język programowania w języku [Node. js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)
* Identyfikator aplikacji publicznej: df67dcdb-c37d-46AF-88e1-8b97951ca1c2


> [!NOTE] 
> Kompletne rozwiązanie Node. js jest dostępne w [repozytorium **Azure-Samples** ](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/analyze-text/node)w witrynie GitHub.

## <a name="get-luis-key"></a>Pobierz klucz LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Programowe pobieranie zamiarów

Aby uzyskać dostęp do tych samych wyników w oknie przeglądarki w poprzednim kroku, można użyć środowiska Node. js.

1. Skopiuj następujący fragment kodu:

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. Utwórz plik `.env` z następującym tekstem lub ustaw te zmienne w środowisku systemowym:

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. Ustaw zmienną środowiskową `LUIS_ENDPOINT_KEY` na klucz.

4. Zainstaluj zależności, uruchamiając następujące polecenie w wierszu polecenia: `npm install`.

5. Uruchom kod z `npm start`. Wyświetla te same wartości, które zostały wyświetlone wcześniej w oknie przeglądarki.


## <a name="luis-keys"></a>Klucze LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start Zamknij projekt programu Visual Studio i Usuń katalog projektu z systemu plików. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie wyrażenia długości i uczenie przy użyciu środowiska Node. js](../luis-get-started-node-add-utterance.md)