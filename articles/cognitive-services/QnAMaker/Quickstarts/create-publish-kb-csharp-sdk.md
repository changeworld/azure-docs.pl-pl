---
title: 'Szybki start: Biblioteka klienta usługi QnA Maker dla platformy .NET'
titlesuffix: Azure Cognitive Services
description: Rozpocznij pracę z biblioteki klienta usługi QnA Maker dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbuj przykładowy kod dla podstawowych zadań.  Usługa QnA Maker umożliwia zasilanie usługi pytań i odpowiedzi za pomocą częściowo ustrukturyzowanej zawartości, takiej jak dokumenty z często zadawanymi pytaniami, adresy URL i podręczniki produktów.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: f38cbfa0efd3b9be9ca091942dca7ffcd91b6019
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828120"
---
# <a name="quickstart-qna-maker-client-library-for-net"></a>Szybki start: Biblioteka klienta usługi QnA Maker dla platformy .NET

Rozpocznij pracę z biblioteki klienta usługi QnA Maker dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbuj przykładowy kod dla podstawowych zadań.  Usługa QnA Maker umożliwia zasilanie usługi pytań i odpowiedzi za pomocą częściowo ustrukturyzowanej zawartości, takiej jak dokumenty z często zadawanymi pytaniami, adresy URL i podręczniki produktów. 

Za pomocą biblioteki klienta usługi QnA Maker dla platformy .NET do:

* Tworzenie bazy wiedzy 
* Zarządzanie bazy wiedzy
* Publikowanie bazy wiedzy

[Dokumentację referencyjną](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [pakietów (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/)  |  [ C# przykłady](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja Azure — [utworzyć jedno za darmo](https://azure.microsoft.com/free/)
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-qna-maker-azure-resource"></a>Utwórz zasób usługi QnA Maker Azure

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Tworzenie zasobu za pomocą usługi QnA Maker [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. 

Po otrzymaniu klucza od zasobu, [Utwórz zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza o nazwie `QNAMAKER_SUBSCRIPTION_KEY`.

### <a name="create-a-new-c-application"></a>Utwórz nową C# aplikacji

Utwórz nową aplikację platformy .NET Core w preferowanego edytora lub w środowisku IDE. 

W oknie konsoli (np. cmd, PowerShell lub Bash), za pomocą polecenia dotnet `new` polecenie, aby utworzyć nową aplikację konsoli o nazwie `qna-maker-quickstart`. To polecenie umożliwia utworzenie prostego "Hello World" C# projektu z jednym pliku źródłowym: `Program.cs`. 

```console
dotnet new console -n qna-maker-quickstart
```

Zmień katalog na folder nowo utworzoną aplikację. Można tworzyć aplikację za pomocą:

```console
dotnet build
```

Dane wyjściowe kompilacji powinien zawierać żadnych ostrzeżeń ani błędów. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```


### <a name="install-the-sdk"></a>Instalacja zestawu SDK

W ramach katalogu aplikacji należy zainstalować biblioteki klienta usługi QnA Maker dla platformy .NET za pomocą następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Jeśli używasz programu Visual Studio IDE z biblioteki klienta jest dostępna jako pakiet NuGet do pobrania.


## <a name="object-model"></a>Model obiektów

Klient usługi QnA Maker jest [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) obiektu, który przeprowadza uwierzytelnianie na platformie Azure przy użyciu Microsoft.Rest.ServiceClientCredentials, który zawiera klucz.

Po utworzeniu klienta użyj [wiedzy](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) właściwości tworzenia, zarządzania i opublikuj bazę wiedzy. 

Zarządzaj bazy wiedzy, wysyłając obiekt JSON. Natychmiastowej operacji metody zwykle zwraca obiekt JSON, wskazujący stan. W przypadku długotrwałych operacji odpowiedź jest identyfikator operacji. Wywołaj [klienta. Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) metody za pomocą Identyfikatora operacji, aby określić [stan żądania](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet). 

 
## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienckiej usługi QnA Maker dla platformy .NET:

* [Tworzenie bazy wiedzy](#create-a-knowledge-base)
* [Aktualizowanie bazy wiedzy](#update-a-knowledge-base)
* [Pobierz bazy wiedzy](#download-a-knowledge-base)
* [Publikowanie bazy wiedzy](#publish-a-knowledge-base)
* [Usuń z bazy wiedzy](#delete-a-knowledge-base)
* [Pobierz stan operacji](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Dodaj zależności

W katalogu projektu otwórz **Program.cs** plik w preferowanym edytorze lub w IDE. Zastąp istniejące `using` kod następującym kodem `using` dyrektywy:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W **głównego** metody, Utwórz zmienną dla zasobu usługi Azure klucza ściągane ze zmienną środowiskową o nazwie `QNAMAKER_SUBSCRIPTION_KEY`. Jeśli zmienna środowiskowa jest utworzony po uruchomieniu aplikacji, edytor, środowiska IDE lub powłoki, na których jest on uruchomiony musisz zamknąć i ponownie załadować uzyskiwania dostępu do zmiennej. Te metody zostaną utworzone później.

Następnie należy utworzyć [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) obiektu za pomocą klucza usługi i korzystać z punktem końcowym usługi, aby utworzyć [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) obiektu.

Jeśli klucz nie znajduje się w `westus` region, jak ten przykład kodu pokazuje, zmień lokalizację **punktu końcowego** zmiennej. Ta lokalizacja znajduje się na **Przegląd** strony zasobu usługi QnA Maker w witrynie Azure portal.

[!code-csharp[Authorization to resource key](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Authorization)]

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

Baza wiedzy przechowuje pary pytań i odpowiedzi dla [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) obiekt z trzech źródeł:

* Aby uzyskać **zawartości redakcyjnej**, użyj [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) obiektu.
* Aby uzyskać **pliki**, użyj [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) obiektu. 
* Aby uzyskać **adresy URL**, użyj listy ciągów.

Wywołaj [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) metody należy przekazać identyfikator operacji zwrócone do [MonitorOperation](#get-status-of-an-operation) metodę, aby sondować stan. 

Ostatni wiersz poniższy kod zwraca identyfikator bazy wiedzy z odpowiedzi z MonitorOoperation. 

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

## <a name="update-a-knowledge-base"></a>Aktualizowanie bazy wiedzy

Możesz zaktualizować wiedzy, przekazując identyfikator bazy wiedzy i [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) zawierający [Dodaj](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [aktualizacji](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet), i [Usuń](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet)DTO obiekty do [metod UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) metody. Użyj [MonitorOperation](#get-status-of-an-operation) metodę, aby określić, jeśli aktualizacja powiodła się.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

## <a name="download-a-knowledge-base"></a>Pobierz bazy wiedzy

Użyj [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) metody do pobierania bazy danych jako listę [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Jest to _nie_ odpowiednikiem eksportu portalu narzędzia QnA Maker z **ustawienia** strony, ponieważ wynikiem tej metody nie jest plik TSV.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

Publikowanie bazy wiedzy knowledge base, za pomocą [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) metody. Pobiera bieżący zapisane uczonego modelu i przywoływane przez identyfikator bazy wiedzy i która zostanie opublikowana w punkcie końcowym. 

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Usuwanie bazy wiedzy

Usuń przy użyciu wiedzy [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) metody z parametrem identyfikator bazy wiedzy knowledge base. 

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Pobierz stan operacji

Niektóre metody, takie jak tworzenie i aktualizowanie, może potrwać wystarczająco dużo czasu, zamiast czekać, aż do zakończenia, procesu [operacji](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) jest zwracana. Użyj [identyfikator operacji](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) operacji do sondowania (z logikę ponawiania próby) do określenia stanu oryginalną metodę. 

_Pętli_ i _Task.Delay_ w poniższy blok kodu służą do symulowania logikę ponawiania próby. Te należy zastąpić je klasą Logika ponawiania prób. 

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą polecenia dotnet `run` polecenie z katalogu aplikacji.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupa zasobów. Usunięcie grupy zasobów powoduje również usunięcie wszystkich innych zasobów skojarzonych z nim.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Samouczek: Tworzenie i Odpowiedz bazę wiedzy](../tutorials/create-publish-query-in-portal.md)

* [Co to jest interfejs API producenta pytań i odpowiedzi?](../Overview/overview.md)
* [Edytowanie wiedzy](../how-to/edit-knowledge-base.md)
* [Rozpoczynanie analizy użycia](../how-to/get-analytics-knowledge-base.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs).