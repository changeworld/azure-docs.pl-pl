---
title: Tworzenie pierwszej funkcji w systemie Linux na platformie Azure
description: Dowiedz się, jak utworzyć pierwszą funkcję hostowaną w systemie Linux na platformie Azure przy użyciu narzędzi Azure Functions Core Tools i interfejsu wiersza polecenia platformy Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: javascript
manager: jeconnoc
ms.openlocfilehash: 6597e0058176eaa819170a494e4908ab44456360
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850537"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>Tworzenie pierwszej funkcji hostowanej w systemie Linux za pomocą narzędzi Core Tools i interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza)

Usługa Azure Functions umożliwia wykonywanie kodu w środowisku [bezserwerowym](https://azure.microsoft.com/overview/serverless-computing/) systemu Linux bez konieczności uprzedniego tworzenia maszyny wirtualnej lub publikowania aplikacji internetowej. Funkcja hostowania w systemie Linux jest obecnie w wersji zapoznawczej i wymaga [środowiska uruchomieniowego usługi Functions 2.0](functions-versions.md). Aby dowiedzieć się więcej na temat uruchamiania aplikacji funkcji w wersji zapoznawczej w systemie Linux, zobacz [ten artykuł dotyczący usługi Functions w systemie Linux](https://aka.ms/funclinux).

W tym artykule Szybki start przedstawiono sposób użycia interfejsu wiersza polecenia platformy Azure w celu utworzenia pierwszej aplikacji funkcji działającej w systemie Linux. Kod funkcji jest tworzony lokalnie, a następnie wdrażany na platformie Azure za pomocą narzędzi [Azure Functions Core Tools](functions-run-local.md).

Poniższe kroki można wykonać na komputerze Mac, w systemie Windows lub w systemie Linux. W tym artykule opisano sposób tworzenia funkcji w języku JavaScript lub C#. Aby dowiedzieć się, jak tworzyć funkcje języka Python, zobacz [Create your first Python function using Core Tools and the Azure CLI (preview) (Tworzenie pierwszej funkcji języka Python za pomocą narzędzi Core Tools i interfejsu wiersza polecenia platformy Azure — wersja zapoznawcza)](functions-create-first-function-python.md).

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
+ `python`: tworzy projekt w języku Python. Aby uzyskać informacje na temat języka Python, zobacz [przewodnik Szybki start dla języka Python](functions-create-first-function-python.md).

Gdy polecenie zostanie wykonane, zostaną zwrócone dane wyjściowe podobne do następujących:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Tworzenie planu usługi App Service dla systemu Linux

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Tworzenie aplikacji funkcji systemu Linux na platformie Azure

Do obsługi wykonywania funkcji w systemie Linux potrzebna jest aplikacja funkcji. Aplikacja funkcji zapewnia bezserwerowe środowisko do wykonywania kodu funkcji. Umożliwia ona grupowanie funkcji w ramach jednostki logicznej, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi. Utwórz aplikację funkcji działającą w systemie Linux przy użyciu polecenia [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

W poniższym poleceniu zamiast symbolu zastępczego `<app_name>` użyj unikatowej nazwy aplikacji funkcji, a zamiast symbolu zastępczego `<storage_name>` użyj nazwy konta magazynu. `<app_name>` jest również domyślną domeną DNS aplikacji funkcji. Ta nazwa musi być unikatowa dla wszystkich aplikacji na platformie Azure. Należy również ustawić środowisko uruchomieniowe `<language>` dla aplikacji funkcji za pomocą pozycji `dotnet` (C#), `node` (JavaScript) lub `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

> [!NOTE]
> Jeśli masz istniejącą grupę zasobów o nazwie `myResourceGroup` obsługującą dowolne aplikacje usługi App Service w systemie innym niż Linux, musisz użyć innej grupy zasobów. Nie można jednocześnie hostować aplikacji systemów Windows i Linux w tej samej grupie zasobów.  

Po utworzeniu aplikacji funkcji zostanie wyświetlony następujący komunikat:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Teraz możesz opublikować swój projekt w nowej aplikacji funkcji na platformie Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak hostować aplikację funkcji w domyślnym kontenerze usługi Azure App Service. Swoje funkcje możesz też hostować w systemie Linux we własnym kontenerze niestandardowym.

> [!div class="nextstepaction"]
> [Tworzenie funkcji w systemie Linux przy użyciu niestandardowego obrazu](functions-create-function-linux-custom-image.md)
