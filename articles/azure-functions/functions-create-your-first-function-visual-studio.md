---
title: 'Szybki start: tworzenie pierwszej funkcji na platformie Azure przy użyciu programu Visual Studio'
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć i opublikować funkcję platformy Azure wyzwalaną przez protokół HTTP przy użyciu programu Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 13a03ca6820229fc90467236e0c23f236664d73f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80056693"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Szybki start: tworzenie pierwszej funkcji na platformie Azure przy użyciu programu Visual Studio

Usługa Azure Functions umożliwia uruchamianie kodu w środowisku bezserwerowym bez konieczności tworzenia maszyny Wirtualnej lub publikowania aplikacji sieci web.

W tym przewodniku Szybki start dowiesz się, jak używać programu Visual Studio 2019 do lokalnego tworzenia i testowania aplikacji funkcji C# wyzwalanych przez "hello world", które następnie publikujesz na platformie Azure. 

![Odpowiedź hosta localhost funkcji wyświetlona w przeglądarce](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Ten przewodnik Szybki start został zaprojektowany dla programu Visual Studio 2019. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, najpierw zainstaluj [program Visual Studio 2019](https://azure.microsoft.com/downloads/). Upewnij się, że podczas instalacji wybierzesz obciążenie **deweloperów platformy Azure.** Jeśli chcesz utworzyć projekt usługi Azure Functions przy użyciu programu Visual Studio 2017 zamiast tego, należy najpierw zainstalować [najnowsze narzędzia usługi Azure Functions](functions-develop-vs.md#check-your-tools-version).

![Instalowanie programu Visual Studio przy za pomocą obciążenia dewelopera platformy Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Tworzenie projektu aplikacji funkcji

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio tworzy projekt i klasę, która zawiera standardowy kod dla typu funkcji wyzwalacza HTTP. Atrybut `FunctionName` metody ustawia nazwę funkcji, która domyślnie `Function1`jest . Atrybut `HttpTrigger` określa, że funkcja jest wyzwalana przez żądanie HTTP. Standardowy kod wysyła odpowiedź HTTP zawierającą wartość z treści żądania lub ciągu zapytania.

Rozwiń możliwości funkcji za pomocą powiązań wejściowych i wyjściowych, stosując odpowiednie atrybuty do metody. Aby uzyskać więcej informacji, zobacz sekcję [Triggers and bindings](functions-dotnet-class-library.md#triggers-and-bindings) (Wyzwalacze i powiązania) [dokumentacji usługi Azure Functions dla deweloperów w C#](functions-dotnet-class-library.md).

Po utworzeniu projektu funkcji i funkcji wyzwalanej przez protokół HTTP można je przetestować na komputerze lokalnym.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Visual Studio integruje się z usługą Azure Functions Core Tools, dzięki czemu można przetestować swoje funkcje lokalnie przy użyciu pełnego środowiska uruchomieniowego usługi Azure Functions.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.

## <a name="publish-the-project-to-azure"></a>Publikowanie projektu na platformie Azure

Aby można było opublikować projekt, musisz mieć aplikację funkcji w subskrypcji platformy Azure. Publikowanie programu Visual Studio tworzy aplikację funkcji dla Ciebie przy pierwszym opublikowaniu projektu.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testowanie funkcji na platformie Azure

1. Skopiuj podstawowy adres URL aplikacji funkcyjnej ze strony **Publikuj** profil. Zastąp `localhost:port` część adresu URL użytego do lokalnego przetestowania funkcji nowym podstawowym adresem URL. Dołącz ciąg `?name=<YOUR_NAME>` zapytania do tego adresu URL i uruchom żądanie.

    Adres URL, który wywołuje funkcję wyzwalania HTTP jest w następującym formacie:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. Wklej nowy adres URL żądania HTTP na pasku adresu przeglądarki. Na poniższej ilustracji przedstawiono odpowiedź w przeglądarce na zdalne żądanie GET zwrócone przez funkcję:

    ![Odpowiedź funkcji wyświetlona w przeglądarce](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto programu Visual Studio do utworzenia i opublikowania aplikacji funkcji Języka C# na platformie Azure przy użyciu prostej funkcji wyzwalane HTTP. 

Przejdź do następnego artykułu, aby dowiedzieć się, jak dodać powiązanie kolejki usługi Azure Storage do funkcji:
> [!div class="nextstepaction"]
> [Dodawanie powiązania kolejki usługi Azure Storage do funkcji](functions-add-output-binding-storage-queue-vs.md)

