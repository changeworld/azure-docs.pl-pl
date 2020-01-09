---
title: 'Szybki Start: QnA Makera Biblioteka kliencka dla środowiska Node. js'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start pokazano, jak rozpocząć pracę z biblioteką kliencką QnA Maker dla środowiska Node. js. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.  Usługa QnA Maker umożliwia zasilanie usługi pytań i odpowiedzi za pomocą częściowo ustrukturyzowanej zawartości, takiej jak dokumenty z często zadawanymi pytaniami, adresy URL i podręczniki produktów.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: a605b5145e393352e8cd6fe18ac97ea749caf4ca
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447592"
---
# <a name="quickstart-qna-maker-client-library-for-nodejs"></a>Szybki Start: QnA Makera Biblioteka kliencka dla środowiska Node. js

Rozpocznij pracę z biblioteką klienta QnA Maker dla środowiska Node. js. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.  Usługa QnA Maker umożliwia zasilanie usługi pytań i odpowiedzi za pomocą częściowo ustrukturyzowanej zawartości, takiej jak dokumenty z często zadawanymi pytaniami, adresy URL i podręczniki produktów.

Użyj QnA Maker biblioteki klienta dla środowiska Node. js, aby:

* Utwórz bazę wiedzy
* Zarządzanie bazą wiedzy
* Publikowanie bazy wiedzy

[Dokumentacja referencyjna](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [pakietu (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [przykładów środowiska Node. js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja środowiska [Node. js](https://nodejs.org).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-qna-maker-azure-resource"></a>Tworzenie zasobu QnA Maker platformy Azure

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla QnA Maker przy użyciu [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym.

Po uzyskaniu klucza z zasobu [Utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla zasobu o nazwie `QNAMAKER_SUBSCRIPTION_KEY` i `QNAMAKER_HOST`. Użyj wartości klucza i punktu końcowego znajdujących się w **kluczach** zasobu i stronach **przeglądu** w Azure Portal.

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir myapp && cd myapp
```

Uruchom `npm init -y` polecenie, aby utworzyć aplikację Node przy użyciu pliku `package.json`.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Zainstaluj `ms-rest-azure` i `azure-cognitiveservices-qnamaker` pakiety NPM:

```console
npm install azure-cognitiveservices--qnamaker ms-rest-azure --save
```

Plik `package.json` aplikacji jest aktualizowany z zależnościami.


## <a name="object-model"></a>Model obiektów

Klient QnA Maker jest obiektem [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) , który jest uwierzytelniany na platformie Azure przy użyciu elementu serviceclientcredentials, który zawiera klucz.

Po utworzeniu klienta Użyj właściwości [bazy wiedzy](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) , aby utworzyć i opublikować bazę wiedzy oraz zarządzać nią.

Zarządzaj bazą wiedzy, wysyłając obiekt JSON. W przypadku operacji natychmiastowych Metoda zwykle zwraca obiekt JSON wskazujący stan. W przypadku długotrwałych operacji odpowiedź jest IDENTYFIKATORem operacji. Wywoływanie [klienta. Operations. Details](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) — Metoda z identyfikatorem operacji w celu określenia [stanu żądania](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest).


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta QnA Maker dla środowiska Node. js:

* [Tworzenie bazy wiedzy](#create-a-knowledge-base)
* [Aktualizowanie bazy wiedzy](#update-a-knowledge-base)
* [Publikowanie bazy wiedzy](#publish-a-knowledge-base)
* [Usuwanie bazy wiedzy](#delete-a-knowledge-base)
* [Pobierz stan operacji](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Dodawanie zależności

Utwórz plik o nazwie `index.js`. Dodaj bibliotekę QnA Maker i bibliotekę REST platformy Azure do pliku.

[!code-javascript[Require statements](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=dependencies)]

Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę, na których jest uruchomiona, aby uzyskać dostęp do zmiennej.

|Zmienna środowiskowa|Zmienna Node. js|Przykład|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|Klucz jest ciągiem znaków 32 i jest dostępny w Azure Portal na QnA Maker zasobu na stronie Szybki Start. Ta wartość nie jest taka sama jak klucz punktu końcowego przewidywania.|
|`QNAMAKER_HOST`|`endpoint`| Twój punkt końcowy tworzenia w formacie `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`zawiera **nazwę zasobu**. Nie jest to ten sam adres URL służący do wykonywania zapytań dotyczących punktu końcowego przewidywania.|
||||

[!code-javascript[Azure resource variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=resourcekeys)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Następnie Utwórz obiekt serviceclientcredentials z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) . Użyj obiektu klienta, aby uzyskać obiekt [bazy wiedzy](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) .


[!code-javascript[Authorization to resource key](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Utwórz bazę wiedzy

Baza wiedzy zapisuje pary pytań i odpowiedzi dla obiektu [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) z trzech źródeł:

* W przypadku **zawartości redakcyjnej**Użyj obiektu [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) .
* Dla **plików**Użyj obiektu [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) .
* W przypadku **adresów URL**Użyj listy ciągów.

Wywołaj metodę [Create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) , a następnie Przekaż identyfikator zwróconej operacji do metody [Operations. Details](#get-status-of-an-operation) w celu sondowania stanu.

[!code-javascript[Create a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=createkb&highlight=15)]

Upewnij się, że funkcja include [`wait_for_operation`](#get-status-of-an-operation) , do której odwołuje się powyższy kod, w celu pomyślnego utworzenia bazy wiedzy.

## <a name="update-a-knowledge-base"></a>Aktualizowanie bazy wiedzy

Bazę wiedzy można zaktualizować, przekazując informacje o IDENTYFIKATORze bazy wiedzy i [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) zawierającym obiekty [Add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [Update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)i [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO do metody [Update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) . Użyj metody [Operation. GetDetail](#get-status-of-an-operation) , aby określić, czy aktualizacja zakończyła się pomyślnie.

[!code-javascript[Update a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=updatekb&highlight=19)]

Upewnij się, że funkcja include [`wait_for_operation`](#get-status-of-an-operation) , do której odwołuje się powyższy kod, w celu pomyślnej aktualizacji bazy wiedzy.

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

Opublikuj bazę wiedzy przy użyciu metody [Publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) . Obejmuje to bieżący zapisany i szkolony model, do którego odwołuje się identyfikator bazy wiedzy, i publikuje go w punkcie końcowym.

[!code-javascript[Publish a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=publishkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Usuwanie bazy wiedzy

Usuń bazę wiedzy przy użyciu metody [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) z parametrem identyfikatora bazy wiedzy.

[!code-javascript[Delete a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=deletekbs&highlight=2)]

## <a name="get-status-of-an-operation"></a>Pobierz stan operacji

Niektóre metody, takie jak tworzenie i aktualizowanie, mogą trwać wystarczająco długo, aby nie czekać na zakończenie procesu, zostanie zwrócona [operacja](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) . Użyj [identyfikatora operacji](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) z operacji do sondowania (z logiką ponownych prób), aby określić stan oryginalnej metody.

Wywołanie _setTimeout_ w poniższym bloku kodu służy do symulowania kodu asynchronicznego. Zastąp to logiką ponawiania.

[!code-javascript[Monitor an operation](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=monitorOperation&highlight=2,17)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą polecenia `node index.js` z katalogu aplikacji.


Wszystkie fragmenty kodu w tym artykule są [dostępne](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js) i można je uruchamiać jako pojedynczy plik.

```console
node index.js
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Samouczek: Tworzenie i odpowiadanie na KB](../tutorials/create-publish-query-in-portal.md)

* [Co to jest interfejs API usługi QnA Maker?](../Overview/overview.md)
* [Edytowanie wiedzy](../how-to/edit-knowledge-base.md)
* [Pobierz analizę użycia](../how-to/get-analytics-knowledge-base.md)