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
ms.openlocfilehash: fdee336298212f2536c2408e49f40e25e2c24161
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227692"
---
# <a name="create-your-first-function-from-the-command-line"></a>Tworzenie pierwszej funkcji z poziomu wiersza polecenia

Ten temat przewodnika Szybki start przeprowadzi Cię przez tworzenie pierwszej funkcji z poziomu wiersza polecenia lub terminalu. Za pomocą interfejsu wiersza polecenia platformy Azure zostanie utworzona aplikacja funkcji, która jest [bezserwerową](https://azure.microsoft.com/solutions/serverless/) infrastrukturą hostującą funkcję. Projekt kodu funkcji jest generowany na podstawie szablonu przy użyciu narzędzi [Azure Functions Core Tools](functions-run-local.md), które są również używane do wdrażania projektu aplikacji funkcji na platformie Azure.

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

Po wyświetleniu monitu wybierz środowisko uruchomieniowe procesu roboczego z następujących opcji języka:

+ `dotnet`: tworzy projekt biblioteki klas platformy .NET (csproj).
+ `node`: tworzy projekt w języku JavaScript.

Gdy polecenie zostanie wykonane, zostaną zwrócone dane wyjściowe podobne do następujących:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

Użyj następującego polecenia, aby przejść do nowego folderu projektu `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

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

### <a name="configure-the-function-app-nodejs"></a>Konfigurowanie aplikacji funkcji (środowisko Node.js)

Podczas tworzenia aplikacji funkcji w języku JavaScript ważne jest wybranie odpowiedniej wersji środowiska Node.js. Wersja 2.x środowiska uruchomieniowego funkcji wymaga środowiska Node.js w wersji 8.x. Ustawienie aplikacji `WEBSITE_NODE_DEFAULT_VERSION` kontroluje wersję środowiska Node.js, która jest używana przez aplikację funkcji na platformie Azure. Użyj polecenia [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set), aby ustawić wersję środowiska Node.js na wartość `8.11.1`.

W poniższym poleceniu interfejsu wiersza polecenia platformy Azure wartość „<app_name>” to nazwa Twojej aplikacji funkcji.

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings WEBSITE_NODE_DEFAULT_VERSION=8.11.1
```

Sprawdź nowe ustawienie w danych wyjściowych.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

