---
title: Tworzenie pierwszej funkcji języka Python na platformie Azure
description: Dowiedz się, jak utworzyć pierwszą funkcję języka Python na platformie Azure przy użyciu narzędzi Azure Functions Core Tools i interfejsu wiersza polecenia platformy Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 08/29/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: af684a4fcc3a70326c1a57cb10a39204b4fd12dc
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438754"
---
# <a name="create-your-first-python-function-in-azure-preview"></a>Tworzenie pierwszej funkcji języka Python na platformie Azure (wersja zapoznawcza)

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

W tym artykule Szybki start przedstawiono sposób użycia interfejsu wiersza polecenia platformy Azure w celu utworzenia pierwszej [bezserwerowej](https://azure.com/serverless) aplikacji funkcji języka Python działającej w systemie Linux. Kod funkcji jest tworzony lokalnie, a następnie wdrażany na platformie Azure za pomocą narzędzi [Azure Functions Core Tools](functions-run-local.md). Aby dowiedzieć się więcej na temat uruchamiania aplikacji funkcji w wersji zapoznawczej w systemie Linux, zobacz [ten artykuł dotyczący usługi Functions w systemie Linux](https://aka.ms/funclinux).

Poniższe kroki można wykonać na komputerze Mac, w systemie Windows lub w systemie Linux.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skompilować i przetestować aplikację w środowisku lokalnym, musisz wykonać następujące czynności:

+ Zainstaluj język [Python 3.6](https://www.python.org/downloads/).

+ Zainstaluj [narzędzia Azure Functions Core Tools](functions-run-local.md#v2) w wersji 2.2.70 lub nowszej (wymagany zestaw SDK platformy .NET Core 2.x).

Aby opublikować i uruchomić aplikację na platformie Azure:

+ Zainstaluj [interfejs wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli) w wersji 2.x lub nowszej.

+ Potrzebna jest aktywna subskrypcja platformy Azure.
  [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Tworzenie i aktywowanie środowiska wirtualnego

Utworzenie projektu usługi Functions wymaga korzystania ze środowiska wirtualnego Python 3.6. Uruchom następujące polecenia, aby utworzyć i aktywować środowisko wirtualne o nazwie `.env`.

```bash
# In Bash
python3.6 -m venv .env
source .env/bin/activate

# In PowerShell
py -3.6 -m venv .env
.env\scripts\activate
```

## <a name="create-a-local-functions-project"></a>Tworzenie lokalnego projektu usługi Functions

Możesz teraz utworzyć lokalny projekt usługi Functions. Ten katalog jest odpowiednikiem aplikacji funkcji na platformie Azure. Może on zawierać wiele funkcji, które mają taką samą konfigurację lokalną i konfigurację hostingu.

W oknie terminalu lub w wierszu polecenia uruchom następujące polecenie:

```bash
func init MyFunctionProj
```

Wybierz opcję **python** jako oczekiwane środowisko uruchomieniowe.

```output
Select a worker runtime:
1. dotnet
2. node
3. python
```

Pojawią się dane wyjściowe — mniej więcej takie.

```output
Installing wheel package
Installing azure-functions package
Installing azure-functions-worker package
Running pip freeze
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing /MyFunctionProj/.vscode/extensions.json
```

Zostanie utworzony nowy folder o nazwie _MyFunctionProj_. Aby kontynuować, przejdź do tego folderu.

```bash
cd MyFunctionProj
```

## <a name="create-a-function"></a>Tworzenie funkcji

Aby utworzyć funkcję, uruchom następujące polecenie:

```bash
func new
```

Wybierz element `HTTP Trigger` jako szablon i podaj **nazwę funkcji** elementu `HttpTrigger`.

```output
Select a template:
1. Blob trigger
2. Cosmos DB trigger
3. Event Grid trigger
4. Event Hub trigger
5. HTTP trigger
6. Queue trigger
7. Service Bus Queue trigger
8. Service Bus Topic trigger
9. Timer trigger

Choose option: 5
Function name: HttpTrigger
```

Pojawią się dane wyjściowe — mniej więcej takie.

```output
Writing /MyFunctionProj/HttpTrigger/sample.dat
Writing /MyFunctionProj/HttpTrigger/__init__.py
Writing /MyFunctionProj/HttpTrigger/function.json
The function "HttpTrigger" was created successfully from the "HTTP trigger" template.
```

Zostanie utworzony podfolder o nazwie _HttpTrigger_. Zawiera on plik `__init__.py` — podstawowy plik skryptu — i plik `function.json`, w którym znajduje się opis wyzwalacza i powiązań używanych przez funkcję. Więcej informacji na temat modelu programowania można znaleźć w [przewodniku po usłudze Azure Functions dla deweloperów języka Python](functions-reference-python.md).

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Uruchom hosta usługi Functions w środowisku lokalnym za pomocą następującego polecenia.

```bash
func host start
```

W danych wyjściowych uruchomionego hosta usługi Functions pojawi się adres URL funkcji wyzwalanej przez protokół HTTP. Zwróć uwagę, że dane wyjściowe zostały skrócone, aby zachować przejrzystość.

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %
...
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.
...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger
```

Skopiuj adres URL funkcji z danych wyjściowych i wklej go na pasku adresu przeglądarki. Dołącz ciąg zapytania `?name=<yourname>` do tego adresu URL i wykonaj żądanie.

    http://localhost:7071/api/HttpTrigger?name=<yourname>

Poniższy zrzut ekranu przedstawia odpowiedź funkcji po jej wyzwoleniu w przeglądarce:

![test](./media/functions-create-first-function-python/function-test-local-browser.png)

Teraz możesz zacząć tworzyć aplikację funkcji oraz inne wymagane zasoby w celu opublikowania ich na platformie Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Tworzenie aplikacji funkcji systemu Linux na platformie Azure

Aplikacja funkcji zapewnia środowisko do wykonywania kodu funkcji. Umożliwia ona grupowanie funkcji w ramach jednostki logicznej, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi. Utwórz **aplikację funkcji języka Python działającą w systemie Linux** przy użyciu polecenia [az functionapp create](/cli/azure/functionapp).

Uruchom poniższe polecenie, używając unikatowej nazwy aplikacji funkcji zamiast symbolu zastępczego `<app_name>` i nazwy konta magazynu zamiast symbolu zastępczego `<storage_name>`. `<app_name>` jest również domyślną domeną DNS aplikacji funkcji. Ta nazwa musi być unikatowa dla wszystkich aplikacji na platformie Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <app_name> --storage-account  <storage_name>
```

> [!NOTE]
> Jeśli masz istniejącą grupę zasobów o nazwie `myResourceGroup` obsługującą dowolne aplikacje usługi App Service w systemie innym niż Linux, musisz użyć innej grupy zasobów. Nie można jednocześnie hostować aplikacji systemów Windows i Linux w tej samej grupie zasobów.  

Po utworzeniu aplikacji funkcji zostanie wyświetlony następujący komunikat:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Teraz możesz opublikować lokalny projekt funkcji w aplikacji funkcji na platformie Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Wdrażanie projektu aplikacji funkcji na platformie Azure

Przy użyciu narzędzi Azure Functions Core Tools uruchom następujące polecenie. Symbol zastępczy `<app_name>` zastąp nazwą aplikacji z poprzedniego kroku.

```bash
func azure functionapp publish <app_name>
```

Pojawią się mniej więcej takie dane wyjściowe. Zostały one skrócone, aby zachować przejrzystość.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat tworzenia wystąpień usługi Azure Functions przy użyciu języka Python.

> [!div class="nextstepaction"]
> [Przewodnik po usłudze Azure Functions dla deweloperów języka Python](functions-reference-python.md)
> [Wyzwalacze i powiązania usługi Azure Functions](functions-triggers-bindings.md)
