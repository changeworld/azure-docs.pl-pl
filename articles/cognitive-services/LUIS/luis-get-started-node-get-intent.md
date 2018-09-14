---
title: Analizowanie tekstu w języku naturalnym w usłudze Language Understanding (LUIS) przy użyciu środowiska Node.js — Cognitive Services — Azure Cognitive Services | Microsoft Docs
description: W tym przewodniku Szybki start użyjesz dostępnej publicznie aplikacji LUIS, aby określić intencję użytkownika w tekście konwersacji. Przy użyciu środowiska Node.js wyślesz intencję użytkownika jako tekst do punktu końcowego przewidywania HTTP aplikacji publicznej. W punkcie końcowym usługa LUIS zastosuje model aplikacji publicznej, aby przeanalizować tekst w języku naturalnym pod kątem znaczenia, określając ogólną intencję i wyodrębniając dane dotyczące domeny podmiotu aplikacji.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: d7067041ae8e413675de3c95ca4ba0c1b987f47a
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "43771699"
---
# <a name="quickstart-analyze-text-using-nodejs"></a>Szybki start: analiza tekstu przy użyciu środowiska Node.js

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* Język programowania [Node.js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)
* Identyfikator aplikacji publicznej: df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> Kompletne rozwiązanie Node.js jest dostępne w [repozytorium GitHub **LUIS-Samples**](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/analyze-text/node).

## <a name="get-luis-key"></a>Pobieranie klucza usługi LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analiza tekstu przy użyciu przeglądarki

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-nodejs"></a>Analiza tekstu przy użyciu środowiska Node.js

Aby uzyskać dostęp do tego samego wyniku, który został wyświetlony w oknie przeglądarki w poprzednim kroku, możesz użyć języka Node.js.

1. Skopiuj następujący fragment kodu:

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. Utwórz plik `.env` z następującym tekstem lub ustaw te zmienne w środowisku systemu:

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. Ustaw zmienną środowiskową `LUIS_ENDPOINT_KEY` na klucz.

4. Zainstaluj zależności, uruchamiając następujące polecenie w wierszu poleceń: `npm install`.

5. Uruchom kod za pomocą polecenia `npm start`. Wyświetli on ten sam wynik, który został wyświetlony wcześniej w oknie przeglądarki.

## <a name="luis-keys"></a>Klucze usługi LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usuń plik Node.js.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Dodawanie wypowiedzi](luis-get-started-node-add-utterance.md)