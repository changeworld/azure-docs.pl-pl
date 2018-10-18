---
title: 'Szybki start: aktualizowanie bazy wiedzy — REST, Node.js — usługa QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki start przeprowadzi Cię przez proces programistycznego aktualizowania istniejącej bazy wiedzy usługi QnA Maker.  Ten kod JSON umożliwia zaktualizowanie bazy wiedzy przez dodanie nowych źródeł danych, zmianę źródeł danych lub usunięcie źródeł danych.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: diberry
ms.openlocfilehash: 3bbc55b3bb064b2cf4b140a395e99209b71a5ce1
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816236"
---
# <a name="quickstart-update-a-qna-maker-knowledge-base-in-nodejs"></a>Szybki start: aktualizowanie bazy wiedzy usługi QnA Maker w środowisku Node.js

Ten przewodnik Szybki start przeprowadzi Cię przez proces programistycznego aktualizowania istniejącej bazy wiedzy usługi QnA Maker.  Ten kod JSON umożliwia zaktualizowanie bazy wiedzy przez dodanie nowych źródeł danych, zmianę źródeł danych lub usunięcie źródeł danych.

Użycie tego interfejsu API odpowiada edytowaniu i użyciu przycisku **Zapisz i przeszkol** w portalu usługi QnA Maker.

Ten przewodnik Szybki start wywołuje interfejsy API usługi QnA Maker:
* [Aktualizacja](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) — model bazy wiedzy jest zdefiniowany w formacie JSON wysyłanym w treści żądania interfejsu API. 
* [Pobieranie szczegółów operacji](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js 6+](https://nodejs.org/en/download/)
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz, wybierz pozycję **Klucze** w obszarze **Zarządzanie zasobami** na pulpicie nawigacyjnym. 
* Identyfikator bazy wiedzy usługi QnA Maker dostępny w adresie URL w parametrze ciągu zapytania kbid, jak pokazano poniżej.

    ![Identyfikator bazy wiedzy usługi QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

Jeśli nie masz jeszcze bazy wiedzy, możesz utworzyć przykładową bazę na potrzeby tego podręcznika Szybki start: [Tworzenie nowej bazy wiedzy](create-new-kb-nodejs.md).

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>Tworzenie pliku Node.js bazy wiedzy

Utwórz plik o nazwie `update-knowledge-base.js`.

## <a name="add-the-required-dependencies"></a>Dodawanie wymaganych zależności

Na początku pliku `update-knowledge-base.js` dodaj następujące wiersze, aby dodać niezbędne zależności do projektu:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-required-constants"></a>Dodawanie wymaganych stałych
Po poprzednich wymaganych zależnościach dodaj wymagane stałe umożliwiające dostęp do usługi QnA Maker. Zastąp wartość zmiennej `subscriptionKey` własnym kluczem usługi QnA Maker. 

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=10-17 "Add required constants")]

## <a name="add-knowledge-base-id"></a>Dodawanie identyfikatora bazy wiedzy

Po poprzednich stałych dodaj identyfikator bazy wiedzy i dodaj go do ścieżki:

[!code-nodejs[Add knowledge base ID](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=19-23 "Add knowledge base ID")]

## <a name="add-the-kb-update-model-definition"></a>Dodawanie definicji modelu aktualizacji bazy wiedzy

Po dodaniu stałych dodaj poniższą definicję aktualizacji bazy wiedzy. Definicja aktualizacji zawiera trzy sekcje:

* add
* update
* delete

W jednym żądaniu wysyłanym do interfejsu API można użyć wszystkich tych sekcji. 

[!code-nodejs[Add the KB update definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=25-53 "Add the KB update definition")]


## <a name="add-supporting-functions"></a>Dodawanie funkcji pomocniczych

Następnie dodaj poniższe funkcje pomocnicze.

1. Dodaj następującą funkcję, aby wyświetlić dane JSON w czytelnym formacie:

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=55-58 "Add supporting functions, step 1")]

2. Dodaj następujące funkcje do zarządzania odpowiedzią HTTP, aby uzyskać stan operacji tworzenia:

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=60-82 "Add supporting functions, step 2")]

## <a name="add-patch-request-to-update-kb"></a>Dodawanie żądania PATCH w celu zaktualizowania bazy wiedzy

Dodaj następujące funkcje, aby utworzyć żądanie HTTP PATCH aktualizujące bazę wiedzy. `Ocp-Apim-Subscription-Key` to klucz usługi QnA Maker używany do uwierzytelniania.

[!code-nodejs[Add PATCH request to update KB](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=84-111 "Add PATCH request to update KB")]

To wywołanie interfejsu API zwraca odpowiedź w formacie JSON, która zawiera identyfikator operacji. Identyfikator operacji jest konieczny do przesłania żądania dotyczącego stanu, jeśli operacja nie została zakończona.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-10-02T01:23:00Z",
  "lastActionTimestamp": "2018-10-02T01:23:00Z",
  "userId": "335c3841df0b42cdb00f53a49d51a89c",
  "operationId": "e7be3897-88ff-44e5-a06c-01df0e05b78c"
}
```

## <a name="add-get-request-to-determine-operation-status"></a>Dodawanie żądania GET w celu ustalenia stanu operacji

Sprawdź stan operacji.
    
[!code-nodejs[Add GET request to determine operation status](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=113-137 "Add GET request to determine operation status")]

To wywołanie interfejsu API zwraca odpowiedź w formacie JSON, która zawiera stan operacji: 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

Powtarzaj wywołanie do momentu uzyskania stanu powodzenia lub niepowodzenia: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-updatekb-method"></a>Dodawanie metody update_kb

Poniższa metoda aktualizuje bazę wiedzy i powtarza sprawdzanie stanu. Tworzenie bazy wiedzy może nieco potrwać, dlatego musisz powtarzać wywołania sprawdzenia stanu do czasu uzyskania stanu powodzenia lub niepowodzenia.

[!code-nodejs[Add update_kb method](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=139-169 "Add update_kb method")]

## <a name="run-the-program"></a>Uruchamianie programu

Wprowadź następujące polecenie w wierszu polecenia, aby uruchomić program. Program wyśle żądanie do interfejsu API usługi QnA Maker, aby zaktualizować bazę wiedzy, a następnie będzie sondować wyniki co 30 sekund. Każda odpowiedź jest wypisywana w oknie konsoli.

```bash
node update-knowledge-base.js
```

Zaktualizowaną bazę wiedzy można wyświetlić w portalu usługi QnA Maker, na stronie [My knowledge bases (Moje bazy wiedzy)](https://www.qnamaker.ai/Home/MyServices). 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)