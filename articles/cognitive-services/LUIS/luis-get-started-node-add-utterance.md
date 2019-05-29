---
title: Zmienianie i trenowanie aplikacji, Node.js
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start Node.js dodasz przykładowe wypowiedzi do aplikacji Home Automation i przeprowadzisz uczenie aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: diberry
ms.openlocfilehash: 13ce1706a2184075155b2bbc4dca5753be391ceb
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357121"
---
# <a name="quickstart-change-model-using-nodejs"></a>Szybki start: zmiana modelu przy użyciu środowiska Node.js

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Najnowsza wersja środowiska [**Node.js**](https://nodejs.org/en/download/) z programem NPM.
* Zależności programu NPM na potrzeby tego artykułu: [**request**](https://www.npmjs.com/package/request), [**request-promise**](https://www.npmjs.com/package/request-promise), [**fs-extra**](https://www.npmjs.com/package/fs-extra).  
* [Program Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Plik JSON z przykładowymi wypowiedziami

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Tworzenie kodu przewodnika Szybki start 

Dodaj zależności NPM do pliku o nazwie `add-utterances.js`.

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=8-11 "NPM Dependencies")]

Dodaj stałe usługi LUIS do pliku. Skopiuj poniższy kod i zmień na swój klucz tworzenia, identyfikator aplikacji i identyfikator wersji.

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=13-22 "LUIS key and IDs")]

Dodaj nazwę i lokalizację pliku przekazywania zawierającego wypowiedzi. 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=24-26 "Add upload file")]

Dodaj metodę i obiekt dla funkcji `addUtterance`.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=28-67 "Add configuration information for adding utterance")]

Dodaj metodę i obiekt dla funkcji `train`.

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=69-101 "Add configuration information for training LUIS")]

Dodaj funkcję `sendUtteranceToApi`, aby wysyłać i odbierać połączenia HTTP. 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=103-119 "Send the HTTP request")]

Dodaj kod **main**, który wybiera akcję.

   [!code-javascript[Main function](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=121-143 "Main function")]

### <a name="install-dependencies"></a>Instalowanie zależności

Utwórz plik `package.json` z następującym tekstem:

   [!code-json[Package.json](~/samples-luis/documentation-samples/quickstarts/change-model/node/package.json "Package.json")]

W wierszu polecenia z poziomu katalogu zawierającego plik package.json zainstaluj zależności za pomocą menedżera NPM: `npm install`.

## <a name="run-code"></a>Uruchamianie kodu

Uruchom aplikację z poziomu wiersza polecenia za pomocą języka Node.js.

Wywołanie polecenia `npm start` spowoduje dodanie wypowiedzi, przeprowadzenie uczenia i uzyskanie stanu uczenia.

```console
> npm start 
```

W tym wierszu polecenia wyświetlane są wyniki wywołania interfejsu API dodawania wypowiedzi. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu przewodnika Szybki start usuń wszystkie pliki utworzone w tym przewodniku Szybki start. 

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"] 
> [Programowe tworzenie aplikacji LUIS](luis-tutorial-node-import-utterances-csv.md)
