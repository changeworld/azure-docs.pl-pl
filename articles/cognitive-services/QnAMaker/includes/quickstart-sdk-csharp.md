---
title: 'Szybki start: biblioteka klienta QnA Maker dla platformy .NET'
description: Ten przewodnik Szybki start pokazuje, jak rozpocząć pracę z biblioteką klienta programu QnA Maker dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.  Usługa QnA Maker umożliwia zasilanie usługi pytań i odpowiedzi za pomocą częściowo ustrukturyzowanej zawartości, takiej jak dokumenty z często zadawanymi pytaniami, adresy URL i podręczniki produktów.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 2911c74226c3b682b75e8d10b0b4b7617a48ec64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946341"
---
Użyj biblioteki klienta programu QnA Maker dla platformy .NET, aby:

* Tworzenie bazy wiedzy
* Zarządzanie bazą wiedzy
* Publikowanie bazy wiedzy
* Generowanie odpowiedzi z bazy wiedzy

[Dokumentacja dokumentacja](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [dokumentacji Pakiet źródłowy (NuGet)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [C# Przykłady](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Bieżąca wersja programu [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-qna-maker-azure-resource"></a>Tworzenie zasobu platformy Azure programu QnA Maker

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla programu QnA Maker przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym.

Po otrzymaniu klucza i punktu końcowego dla zasobu [utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza o nazwie `QNAMAKER_SUBSCRIPTION_KEY`. Nazwa zasobu jest używana jako część adresu URL punktu końcowego.

### <a name="create-a-new-c-application"></a>Tworzenie nowej aplikacji języka C#

Utwórz nową aplikację .NET Core w preferowanym edytorze lub ide.

W oknie konsoli (takim jak cmd, PowerShell `dotnet new` lub Bash) użyj polecenia, `qna-maker-quickstart`aby utworzyć nową aplikację konsoli o nazwie . To polecenie tworzy prosty projekt "Hello World" C# z jednym plikiem źródłowym: *Program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można utworzyć za pomocą:

```dotnetcli
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać żadnych ostrzeżeń ani błędów.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-sdk"></a>Instalacja zestawu SDK

W katalogu aplikacji zainstaluj bibliotekę klienta programu QnA Maker dla platformy .NET za pomocą następującego polecenia:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Jeśli używasz środowiska IDE programu Visual Studio, biblioteka klienta jest dostępna jako pakiet NuGet do pobrania.


## <a name="object-model"></a>Model obiektu

QnA Maker klient jest [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) obiektu, który uwierzytelnia się na platformie Azure przy użyciu microsoft.rest.ServiceClientCredentials, który zawiera klucz.

Po utworzeniu klienta użyj właściwości [Baza wiedzy,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) aby utworzyć bazę wiedzy, zarządzać nią i publikować.

Zarządzaj swoją bazą wiedzy, wysyłając obiekt JSON. W przypadku operacji natychmiastowych metoda zwykle zwraca obiekt JSON wskazujący stan. W przypadku długotrwałych operacji odpowiedzią jest identyfikator operacji. Zadzwoń do [klienta. Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) metoda z identyfikatorem operacji w celu określenia [stanu żądania](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet).


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta programu QnA Maker dla platformy .NET:

* [Tworzenie bazy wiedzy](#create-a-knowledge-base)
* [Aktualizowanie bazy wiedzy](#update-a-knowledge-base)
* [Pobieranie bazy wiedzy](#download-a-knowledge-base)
* [Publikowanie bazy wiedzy](#publish-a-knowledge-base)
* [Usuwanie bazy wiedzy](#delete-a-knowledge-base)
* [Uzyskaj stan operacji](#get-status-of-an-operation)
* [Generowanie odpowiedzi z bazy wiedzy](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>Dodawanie zależności

W katalogu projektu otwórz plik **Program.cs** w preferowanym edytorze lub w programie IDE. Zastąp `using` istniejący `using` kod następującymi dyrektywami:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Uwierzytelnianie klienta w celu tworzenia bazy wiedzy

W metodzie **głównej** utwórz zmienną dla klucza platformy Azure `QNAMAKER_SUBSCRIPTION_KEY`zasobu pobraną ze zmiennej środowiskowej o nazwie . Jeśli utworzono zmienną środowiskową po uruchomieniu aplikacji, edytor, IDE lub powłoki uruchomionej będzie musiał zostać zamknięty i ponownie załadowany, aby uzyskać dostęp do zmiennej. Metody zostaną utworzone później.

Następnie utwórz [obiekt ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) za pomocą klucza i użyj go z punktem końcowym, aby utworzyć obiekt [QnAMakerClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet)

|Zmienna środowiskowa|zmienna|Przykład|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|Klucz jest ciągiem znaków 32 i jest dostępny w witrynie Azure portal, w zasobie QnA Maker, na stronie Szybki start. To nie jest taka sama jak klucz punktu końcowego przewidywania.|
|`QNAMAKER_HOST`|`Endpoint`| Punkt końcowy tworzenia w formacie `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`, zawiera **nazwę zasobu**. Nie jest to ten sam adres URL, który jest używany do wykonywania zapytań o punkt końcowy przewidywania.|
||||

```csharp
var subscriptionKey = Environment.GetEnvironmentVariable("QNAMAKER_SUBSCRIPTION_KEY");
var client = new QnAMakerClient(new ApiKeyServiceClientCredentials(subscriptionKey)) { Endpoint = "https://<your-custom-domain>.api.cognitive.microsoft.com" };
```

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Uwierzytelnianie środowiska wykonawczego do generowania odpowiedzi

W metodzie **głównej** utwórz zmienną dla uwierzytelniania zasobu pobraną ze zmiennych środowiskowych o nazwie `QNAMAKER_ENDPOINT_HOSTNAME` i `QNAMAKER_ENDPOINT_KEY`. Podczas publikowania bazy wiedzy te wartości są zwracane. Po opublikowaniu te ustawienia można znaleźć na stronie **Ustawienia** portalu QnA Maker.

Utwórz [QnAMakerRuntimeClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) aby zbadać bazę wiedzy, aby wygenerować odpowiedź lub trenować z aktywnego uczenia się.

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey)]

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

Baza wiedzy przechowuje pary pytań i odpowiedzi dla obiektu [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) z trzech źródeł:

* W przypadku **zawartości redakcyjnej**użyj obiektu [QnADTO.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet)
* W przypadku **plików**użyj obiektu [FileDTO.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet)
* W przypadku **adresów URL**użyj listy ciągów.

Wywołanie [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) metoda następnie przekazać zwrócony identyfikator operacji do [MonitorOperation](#get-status-of-an-operation) metody sondowania stanu.

Końcowy wiersz następującego kodu zwraca identyfikator bazy wiedzy z odpowiedzi z MonitorOoperation.

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

Upewnij się, [`MonitorOperation`](#get-status-of-an-operation) że funkcja dołączyć się w powyższym kodzie, aby pomyślnie utworzyć bazę wiedzy.

## <a name="update-a-knowledge-base"></a>Aktualizowanie bazy wiedzy

Bazę wiedzy można zaktualizować, przekazując identyfikator bazy wiedzy i [updatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) zawierający [dodawanie,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet) [aktualizowanie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)i [usuwanie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) obiektów DTO do metody [UpdateAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) Użyj [MonitorOperation](#get-status-of-an-operation) metody, aby ustalić, czy aktualizacja powiodła się.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

Upewnij się, [`MonitorOperation`](#get-status-of-an-operation) że funkcja, o której mowa w powyższym kodzie, w celu pomyślnej aktualizacji bazy wiedzy.

## <a name="download-a-knowledge-base"></a>Pobieranie bazy wiedzy

Użyj [downloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) metoda pobrać bazę danych jako listę [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). _Nie_ jest to równoważne eksportowi portalu QnA Maker ze strony **Ustawienia,** ponieważ wynikiem tej metody nie jest plik TSV.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

Opublikuj bazę wiedzy przy użyciu [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) metody. Spowoduje to uwzględnione bieżące zapisane i przeszkolony model, do którego odwołuje się identyfikator bazy wiedzy, i publikuje go w punkcie końcowym.

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generowanie odpowiedzi z bazy wiedzy

Generowanie odpowiedzi z opublikowanej bazy wiedzy przy użyciu [języka RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). [GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) metoda. Ta metoda akceptuje identyfikator bazy wiedzy i [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Dostęp do dodatkowych właściwości QueryDTO, takich [jak Top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) i [kontekst](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) do użycia w bot czatu.

[!code-csharp[Generate an answer from a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=GenerateAnswer&highlight=2)]


## <a name="delete-a-knowledge-base"></a>Usuwanie bazy wiedzy

Usuń bazę wiedzy przy użyciu [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) metody z parametrem identyfikator bazy wiedzy.

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Uzyskaj stan operacji

Niektóre metody, takie jak tworzenie i aktualizowanie, może zająć wystarczająco dużo czasu, że zamiast czekać na zakończenie procesu, zwracana jest [operacja.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) Użyj [identyfikatora operacji](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) z operacji do sondowania (z logiką ponawiania próby), aby określić stan oryginalnej metody.

_Pętla_ i _Task.Delay_ w następującym bloku kodu są używane do symulowania logiki ponawiania. Należy je zastąpić własną logiką ponawiania.

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za `dotnet run` pomocą polecenia z katalogu aplikacji.

Wszystkie fragmenty kodu w tym artykule są [dostępne](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) i mogą być uruchamiane jako pojedynczy plik.

```dotnetcli
dotnet run
```

[Kod źródłowy tego przewodnika Szybki start](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs) jest dostępny w przykładowym repozytorium Programu GitHub dla programu QnA Maker C#.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)