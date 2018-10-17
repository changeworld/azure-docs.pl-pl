---
title: Tworzenie pierwszej funkcji z poziomu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć pierwszą funkcję platformy Azure do wykonywania bezserwerowego przy użyciu interfejsu wiersza polecenia platformy Azure i narzędzi Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 09/10/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: f65ff3e38e3768dccf04e9eaa4345ffcea4daa9e
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451124"
---
# <a name="create-your-first-function-from-the-command-line"></a>Tworzenie pierwszej funkcji z poziomu wiersza polecenia

Ten temat przewodnika Szybki start przeprowadzi Cię przez tworzenie pierwszej funkcji z poziomu wiersza polecenia lub terminalu. Za pomocą interfejsu wiersza polecenia platformy Azure zostanie utworzona aplikacja funkcji, która jest [bezserwerową](https://azure.microsoft.com/overview/serverless-computing/) infrastrukturą hostującą funkcję. Projekt kodu funkcji jest generowany na podstawie szablonu przy użyciu narzędzi [Azure Functions Core Tools](functions-run-local.md), które są również używane do wdrażania projektu aplikacji funkcji na platformie Azure.

Poniższe kroki możesz wykonać przy użyciu komputera z systemem Mac, Windows lub Linux.

## <a name="prerequisites"></a>Wymagania wstępne

Przed uruchomieniem tego przykładu należy dysponować następującymi elementami:

+ Zainstaluj narzędzia [Azure Core Tools w wersji 2.x](functions-run-local.md#v2).

+ Zainstaluj [interfejs wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Możesz również użyć usługi [Azure Cloud Shell](https://shell.azure.com/bash).

+ Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Tworzenie lokalnego projektu aplikacji funkcji

Uruchom następujące polecenie w wierszu polecenia, aby utworzyć projekt aplikacji funkcji w folderze `MyFunctionProj` bieżącego katalogu lokalnego. Ponadto w folderze `MyFunctionProj` jest tworzone repozytorium GitHub.

```bash
func init MyFunctionProj
```

Po wyświetleniu monitu użyj klawiszy strzałek, aby wybrać środowisko uruchomieniowe procesu roboczego z następujących opcji języka:

+ `dotnet`: tworzy projekt biblioteki klas platformy .NET (csproj).
+ `node`: tworzy projekt w języku JavaScript.

Gdy polecenie zostanie wykonane, zostaną zwrócone dane wyjściowe podobne do następujących:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

## <a name="create-a-function"></a>Tworzenie funkcji

Następujące polecenie powoduje przejście do nowego projektu i utworzenie funkcji wyzwalanej przez protokół HTTP o nazwie `MyHtpTrigger`.

```bash
cd MyFunctionProj
func new --name MyHttpTrigger --template "HttpTrigger"
```

Gdy polecenie zostanie wykonane, zostaną zwrócone dane wyjściowe podobne do następujących. Jest to funkcja JavaScript:

```output
Writing C:\functions\MyFunctionProj\MyHttpTrigger\index.js
Writing C:\functions\MyFunctionProj\MyHttpTrigger\sample.dat
Writing C:\functions\MyFunctionProj\MyHttpTrigger\function.json
```

## <a name="edit-the-function"></a>Edytowanie funkcji

Domyślnie szablon tworzy funkcję, która wymaga klucza funkcji do wysyłania żądań. Aby ułatwić testowanie funkcji na platformie Azure, należy zaktualizować funkcję, aby zezwolić na dostęp anonimowy. Sposób wprowadzania tej zmiany zależy od języka projektu funkcji.

### <a name="c"></a>C\#

Otwórz plik kodu MyHttpTrigger.cs, który jest Twoją nową funkcją, i zmień atrybut **AuthorizationLevel** w definicji funkcji na wartość `anonymous`, a następnie zapisz zmiany.

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

Otwórz plik function.json nowej funkcji, otwórz go w edytorze tekstów, zmień właściwość **authLevel** w sekcji **bindings.httpTrigger** na wartość `anonymous` i zapisz zmiany.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

Teraz możesz wywoływać funkcję na platformie Azure bez konieczności podawania klucza funkcji. Klucz funkcji nigdy nie jest wymagany podczas pracy lokalnej.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Następujące polecenie uruchamia aplikację funkcji. Aplikacja jest uruchamiana przy użyciu tego samego środowiska uruchomieniowego usługi Azure Functions, które znajduje się na platformie Azure.

```bash
func host start --build
```

Opcja `--build` jest wymagana do kompilowania projektów w języku C#. Nie jest ona potrzebna w przypadku projektu w języku JavaScript.

Podczas uruchamiania hosta funkcji zwróci on dane wyjściowe podobne do następujących (zostały one skrócone, aby zwiększyć czytelność):

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

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Skopiuj adres URL funkcji `HTTPTrigger` z danych wyjściowych środowiska uruchomieniowego i wklej go w pasku adresu swojej przeglądarki. Dołącz ciąg zapytania `?name=<yourname>` do tego adresu URL i wykonaj żądanie. Poniżej pokazano wyświetloną w przeglądarce odpowiedź na żądanie GET zwróconą przez funkcję lokalną:

![Testowanie lokalne w przeglądarce](./media/functions-create-first-azure-function-azure-cli/functions-test-local-browser.png)

Teraz, gdy funkcja została uruchomiona lokalnie, możesz utworzyć aplikację funkcji i inne wymagane zasoby na platformie Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji potrzebna jest aplikacja funkcji. Aplikacja funkcji zapewnia środowisko do bezserwerowego wykonywania kodu funkcji. Umożliwia ona grupowanie funkcji w ramach jednostki logicznej, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi. Utwórz aplikację funkcji przy użyciu polecenia [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

W poniższym poleceniu w miejsce symbolu zastępczego `<app_name>` wstaw unikatową nazwę aplikacji funkcji, a w miejsce symbolu zastępczego `<storage_name>` wstaw nazwę konta magazynu. Nazwa `<app_name>` jest używana jako domyślna domena DNS aplikacji funkcji, więc nazwa ta musi być unikatowa wśród wszystkich aplikacji na platformie Azure. Parametr _deployment-source-url_ to przykładowe repozytorium GitHub, które zawiera funkcję „Hello world” wyzwalaną za pośrednictwem protokołu HTTP.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```

Ustawienie parametru _consumption-plan-location_ oznacza, że funkcja aplikacji jest hostowana w planie hostingu Zużycie. W tym planie bezserwerowym zasoby są dodawane dynamicznie zgodnie z wymaganiami Twoich funkcji i płacisz tylko wtedy, kiedy funkcje są uruchomione. Aby uzyskać więcej informacji, zobacz [Wybieranie odpowiedniego planu hostingu](functions-scale.md).

Po utworzeniu aplikacji funkcji interfejs wiersza polecenia platformy Azure wyświetli informacje podobne do następujących:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

## <a name="configure-the-function-app"></a>Konfigurowanie aplikacji funkcji

Narzędzia Core Tools w wersji 2.x tworzą projekty za pomocą szablonów środowiska uruchomieniowego 2.x usługi Azure Functions. W związku z tym należy się upewnić, że na platformie Azure jest używane środowisko uruchomieniowe w wersji 2.x. Określenie dla ustawienia aplikacji `FUNCTIONS_WORKER_RUNTIME` wartości `~2` powoduje powiązanie aplikacji funkcji z najnowszą wersją 2.x. Skonfiguruj ustawienia aplikacji za pomocą polecenia [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set).

W poniższym poleceniu interfejsu wiersza polecenia platformy Azure wartość „<app_name>” to nazwa Twojej aplikacji funkcji.

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~2
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
