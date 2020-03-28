---
title: 'Szybki start: biblioteka klienta programu QnA Maker dla pliku Node.js'
description: Ten przewodnik Szybki start pokazuje, jak rozpocząć pracę z biblioteką klienta programu QnA Maker dla pliku Node.js.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 05e6d2c77a351c22c73f0bdb54daeaf0e03f4a9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76021323"
---
Użyj biblioteki klienta programu QnA Maker dla pliku Node.js, aby:

* Tworzenie bazy wiedzy
* Aktualizowanie bazy wiedzy
* Publikowanie bazy wiedzy
* Pobierz opublikowany klucz punktu końcowego
* Poczekaj na długotrwałe zadanie
* Usuwanie bazy wiedzy

[Dokumentacja](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [dokumentacja dokumentacji Pakiet źródłowy (NPM)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [Package (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js Samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Bieżąca wersja [pliku Node.js](https://nodejs.org).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-qna-maker-azure-resource"></a>Tworzenie zasobu platformy Azure programu QnA Maker

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla programu QnA Maker przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym.

Po uzyskaniu klucza i punktu końcowego z zasobu, pobierz wartości z witryny Azure portal, dla nowego zasobu, na stronie Szybki start.

[Tworzenie zmiennych](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)środowiskowych `QNAMAKER_AUTHORING_KEY` `QNAMAKER_ENDPOINT`, nazwanych i . Plik [.env.sample](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/.env.sample) można skopiować do `.env` zmiennych środowiskowych w tym pliku i użyć go.

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog aplikacji i przejdź do niego.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Uruchom `npm init -y` polecenie, aby utworzyć aplikację `package.json` węzła z plikiem.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Zainstaluj wymagane i opcjonalne pakiety NPM:

```console
npm install @azure/cognitiveservices-qnamaker @azure/ms-rest-js dotenv
```

`package.json` Plik aplikacji jest aktualizowany o zależności. Jest `dotenv` opcjonalny i służy do ustawiania zmiennych środowiskowych w pliku tekstowym. Nie należy `.env` sprawdzać w formancie źródła.


## <a name="object-model"></a>Model obiektu

Klient QnA Maker jest obiektem [QnAMakerClient,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) który uwierzytelnia się na platformie Azure przy użyciu serviceclientcredentials, który zawiera klucz.

Po utworzeniu klienta należy użyć właściwości [Baza wiedzy](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) tworzenie, zarządzanie i publikowanie bazy wiedzy.

Zarządzaj swoją bazą wiedzy, wysyłając obiekt JSON. W przypadku operacji natychmiastowych metoda zwykle zwraca obiekt JSON wskazujący stan. W przypadku długotrwałych operacji odpowiedzią jest identyfikator operacji. Zadzwoń do [klienta. Operations.getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) metoda z identyfikatorem operacji w celu określenia [stanu żądania](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest).


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta programu QnA Maker dla pliku Node.js:

* [Tworzenie bazy wiedzy](#create-a-knowledge-base)
* [Aktualizowanie bazy wiedzy](#update-a-knowledge-base)
* [Publikowanie bazy wiedzy](#publish-a-knowledge-base)
* [Usuwanie bazy wiedzy](#delete-a-knowledge-base)
* [Uzyskaj opublikowany punkt końcowy](#get-published-endpoint)
* [Uzyskaj stan operacji](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Dodawanie zależności

Utwórz plik o nazwie `index.js`. Dodaj bibliotekę QnA Maker i zależności do pliku.

[!code-javascript[Require statements](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=dependencies)]

Tworzenie zmiennych dla punktu końcowego i klucza platformy Azure zasobu. Jeśli po uruchomieniu aplikacji utworzono zmienną środowiskową, należy zamknąć i ponownie otworzyć edytor, IDE lub powłokę uruchamianą ją, aby uzyskać dostęp do zmiennej.

|Zmienna środowiskowa|Zmienna node.js|Przykład|
|--|--|--|
|`QNAMAKER_AUTHORING_KEY`|`authoring_key`|Klucz jest ciągiem znaków 32 i jest dostępny w witrynie Azure portal, w zasobie QnA Maker, na stronie Szybki start. To nie jest taka sama jak klucz punktu końcowego przewidywania.|
|`QNAMAKER_ENDPOINT`|`endpoint`| Punkt końcowy tworzenia w formacie `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`, zawiera **nazwę zasobu**. Nie jest to ten sam adres URL, który jest używany do wykonywania zapytań o punkt końcowy przewidywania.|
||||

[!code-javascript[Azure resource variables](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=resourceSettings)]

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

Następnie utwórz obiekt ApiKeyCredentials za pomocą klucza i użyj go z punktem końcowym, aby utworzyć obiekt [QnAMakerClient.](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) Użyj obiektu klienta, aby uzyskać obiekt [klienta bazy wiedzy.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest)


[!code-javascript[Authorization to resource key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

Baza wiedzy przechowuje pary pytań i odpowiedzi dla obiektu [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) z trzech źródeł:

* W przypadku **zawartości redakcyjnej**użyj obiektu [QnADTO.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest)
* W przypadku **plików**użyj obiektu [FileDTO.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest)
* W przypadku **adresów URL**użyj listy ciągów.

Wywołanie metody [tworzenia](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) z informacjami bazy wiedzy. Informacje o bazie wiedzy są w zasadzie obiektem JSON.

Gdy metoda tworzenia zwraca, przekazać zwrócony identyfikator operacji do [metody wait_for_operation](#get-status-of-an-operation) do sondowania stanu. Metoda wait_for_operation zwraca po zakończeniu operacji. Przesiemadź `resourceLocation` wartość nagłówka zwróconej operacji, aby uzyskać nowy identyfikator bazy wiedzy.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=createKnowledgeBase&highlight=15,30)]

Upewnij się, [`wait_for_operation`](#get-status-of-an-operation) że funkcja dołączyć się w powyższym kodzie, aby pomyślnie utworzyć bazę wiedzy.

## <a name="update-a-knowledge-base"></a>Aktualizowanie bazy wiedzy

Bazę wiedzy można zaktualizować, przekazując identyfikator bazy wiedzy i [updateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) zawierającą [dodawanie,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add) [aktualizowanie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)i [usuwanie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) obiektów DTO do metody [aktualizacji.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) DTO są również w zasadzie JSON obiektów. Użyj [metody wait_for_operation,](#get-status-of-an-operation) aby ustalić, czy aktualizacja powiodła się.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=updateKnowledgeBase&highlight=28)]

Upewnij się, [`wait_for_operation`](#get-status-of-an-operation) że funkcja, o której mowa w powyższym kodzie, w celu pomyślnej aktualizacji bazy wiedzy.

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

Opublikuj bazę wiedzy przy użyciu metody [publikowania.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) Spowoduje to uwzględnione bieżące zapisane i przeszkolony model, do którego odwołuje się identyfikator bazy wiedzy, i publikuje go w punkcie końcowym. Sprawdź kod odpowiedzi HTTP, aby sprawdzić poprawność publikowania powiodło się.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=publishKnowledgeBase&highlight=3)]


## <a name="get-published-endpoint"></a>Uzyskaj opublikowany punkt końcowy

Po opublikowaniu bazy wiedzy należy uzyskać dostęp do opublikowanej bazy wiedzy za pośrednictwem środowiska wykonawczego przewidywania kwerendyWauchw interfejsie API. W tym celu potrzebny jest klucz punktu końcowego środowiska wykonawczego. Jest to inny niż klucz tworzenia.

[!code-javascript[Get endpoint keys](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=getEndpointKeys&highlight=3)]

Dwa klucze punktu końcowego są zwracane z wywołania. Tylko jeden jest niezbędny do uzyskania dostępu do punktu końcowego środowiska wykonawczego.

## <a name="delete-a-knowledge-base"></a>Usuwanie bazy wiedzy

Usuń bazę wiedzy przy użyciu metody [usuwania](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) z parametrem identyfikatora bazy wiedzy.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=deleteKnowledgeBase&highlight=3)]

## <a name="get-status-of-an-operation"></a>Uzyskaj stan operacji

Niektóre metody, takie jak tworzenie i aktualizowanie, może zająć wystarczająco dużo czasu, że zamiast czekać na zakończenie procesu, zwracana jest [operacja.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) Użyj [identyfikatora operacji](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) z operacji do sondowania (z logiką ponawiania próby), aby określić stan oryginalnej metody.

_DelayTimer wywołanie_ w następującym bloku kodu jest używany do symulacji logiki ponawiania. Zamień to na własną logikę ponawiania.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=monitorOperation&highlight=8,13)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację `node index.js` za pomocą polecenia z katalogu aplikacji.

Wszystkie fragmenty kodu w tym artykule są [dostępne](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js) i mogą być uruchamiane jako pojedynczy plik.

```console
node index.js
```

Program drukuje stan konsoli:

```console
qnamaker_quickstart@1.0.0 start C:\samples\cognitive-services-quickstart-code\javascript\QnAMaker\sdk> node index.js

Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Create operation 200, KB ID 99df758d-f23f-4931-ab83-e738fe978e69
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Update operation state 200 - HTTP status 200
Publish request succeeded - HTTP status 204
GetEndpointKeys request succeeded - HTTP status 200 - primary key 8482830b-681e-400e-b8a3-4016278aba64
QnA Maker FAQ stored in English language with 1 sources, last updated 2020-01-12T16:54:40Z
New KB name stored in English language with 1 sources, last updated 2020-01-12T17:32:16Z
New KB name stored in English language with 1 sources, last updated 2020-01-13T00:27:46Z
Delete operation state succeeded - HTTP status 204
done
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)