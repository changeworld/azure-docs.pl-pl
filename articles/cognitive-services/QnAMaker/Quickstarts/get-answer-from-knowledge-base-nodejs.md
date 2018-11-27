---
title: 'Szybki start: uzyskiwanie odpowiedzi z bazy wiedzy — środowisko REST, Node.js — QnA Maker'
titlesuffix: Azure Cognitive Services
description: W tym samouczku Szybki start opartym na protokole REST i środowisku Node.js opisano sposób programowego uzyskiwania odpowiedzi z bazy wiedzy.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: diberry
ms.openlocfilehash: 9a3db996429bbbd0883643e2b7c6622dcff5480f
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977857"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>Uzyskiwanie odpowiedzi na pytanie z bazy wiedzy przy użyciu środowiska Node.js

Ten przewodnik Szybki start przeprowadzi Cię przez programowe uzyskiwanie odpowiedzi z opublikowanej bazy wiedzy usługi QnA Maker. Usługa QnA Maker automatycznie wyodrębnia pytania i odpowiedzi z częściowo ustrukturyzowanej zawartości, na przykład często zadawanych pytań, ze [źródeł danych](../Concepts/data-sources-supported.md). Pytanie w formacie JSON jest wysyłane w treści żądania interfejsu API. 

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby uzyskać klucz, wybierz pozycję **Klucze** w obszarze **Zarządzanie zasobami** na pulpicie nawigacyjnym platformy Azure dla zasobu usługi QnA Maker. 
* Ustawienia na stronie **Publikowanie**. Jeśli nie masz opublikowanej bazy wiedzy, utwórz pustą bazę wiedzy, zaimportuj bazę wiedzy na stronie **Ustawienia**, a następnie opublikuj. Możesz pobrać [tę podstawową bazę wiedzy](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv) i używać jej. 

    Ustawienia strony publikowania obejmują wartość trasy POST, wartość hosta i wartość elementu EndpointKey. 

    ![Ustawienia publikowania](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Kod używany w tym przewodniku Szybki start znajduje się w repozytorium [https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/get-answer). 

## <a name="create-a-nodejs-file"></a>Tworzenie pliku środowiska Node.js

Otwórz program VSCode i utwórz nowy plik o nazwie `get-answer.js`. 

## <a name="add-the-required-dependencies"></a>Dodawanie wymaganych zależności

Na początku pliku `get-answer.js` dodaj wymagane zależności do projektu:

[!code-nodejs[Add the required dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=1-4 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Dodawanie wymaganych stałych

Następnie dodaj stałe wymagane w celu uzyskiwania dostępu do usługi QnA Maker. Te wartości znajdują się na stronie **Publikowanie** po opublikowaniu bazy wiedzy. 

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=6-22 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Dodawanie żądania POST wysyłającego zapytanie i uzyskującego odpowiedź

Poniższy kod umożliwia wysłanie żądania HTTPS do interfejsu API usługi QnA Maker w celu wysłania zapytania do bazy wiedzy oraz odebrania odpowiedzi:

[!code-nodejs[Add a POST request to send question to knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=24-49 "Add a POST request to send question to knowledge base")]

Wartość nagłówka `Authorization` zawiera ciąg `EndpointKey `. 

## <a name="install-the-dependencies"></a>Instalowanie zależności

Zainstaluj zależności przy użyciu wiersza polecenia:

```bash
npm install request request-promise
```

## <a name="run-the-program"></a>Uruchamianie programu

Uruchom program z poziomu wiersza polecenia. Automatycznie wyśle on żądanie do interfejsu API usługi QnA Maker, a następnie wyświetli informacje w oknie konsoli.

Uruchom plik:

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)