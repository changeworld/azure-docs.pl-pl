---
title: Tworzenie aplikacji funkcji w systemie Linux w ramach planu usługi Azure App Service
description: Dowiedz się, jak utworzyć aplikację funkcji działającej w systemie Linux w ramach planu usługi App Service przy użyciu wiersza polecenia platformy Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: d9800ff3fc82636c5cae12167738667ec84326ee
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856620"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan-preview"></a>Tworzenie aplikacji funkcji w systemie Linux w ramach planu usługi Azure App Service (wersja zapoznawcza)

Usługa Azure Functions umożliwia obsługę funkcji w systemie Linux w domyślnym kontenerze usługi Azure App Service. W tym artykule opisano przy użyciu wiersza polecenia platformy Azure do tworzenia aplikacji funkcji obsługiwane systemu Linux na platformie Azure, która działa w [planu usługi App Service](functions-scale.md#app-service-plan). Możesz również [skorzystać z własnego kontenera niestandardowego](functions-create-function-linux-custom-image.md). Hosting systemu Linux jest obecnie w wersji zapoznawczej.

W ramach planu usługi App Service odpowiedzialność za skalowanie aplikacji funkcji. Aby móc korzystać z możliwości bez użycia serwera usługi Azure functions, może również obsługiwać funkcje w systemie Linux w [planu zużycie](functions-scale.md#consumption-plan).

Poniższe kroki możesz wykonać przy użyciu komputera z systemem Mac, Windows lub Linux.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

+ Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, na potrzeby tego tematu jest wymagany interfejs wiersza polecenia platformy Azure w wersji 2.0.21 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Tworzenie planu usługi App Service dla systemu Linux

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Tworzenie aplikacji funkcji w systemie Linux

Do obsługi wykonywania funkcji w systemie Linux potrzebna jest aplikacja funkcji. Aplikacja funkcji zapewnia środowisko do wykonywania kodu funkcji. Umożliwia ona grupowanie funkcji w ramach jednostki logicznej, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi. Utwórz aplikację funkcji przy użyciu polecenia [az functionapp create](/cli/azure/functionapp#az-functionapp-create) w ramach planu usługi App Service dla systemu Linux.

W poniższym poleceniu w miejsce symbolu zastępczego `<app_name>` wstaw unikatową nazwę aplikacji funkcji, a w miejsce symbolu zastępczego `<storage_name>` wstaw nazwę konta magazynu. Nazwa `<app_name>` jest używana jako domyślna domena DNS aplikacji funkcji, więc nazwa ta musi być unikatowa wśród wszystkich aplikacji na platformie Azure. Należy również ustawić `<language>` środowisko uruchomieniowe dla aplikacji funkcji z `dotnet` (C#), `node` (JavaScript) lub `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --plan myAppServicePlan \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

Po utworzeniu i wdrożeniu aplikacji funkcji interfejs wiersza polecenia platformy Azure wyświetli informacje podobne do następujących:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
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

Ponieważ `myAppServicePlan` jest planem systemu Linux, wbudowany obraz platformy Docker służy do tworzenia kontenera, w którym działa aplikacja funkcji w systemie Linux.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources-simple.md)]

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak utworzyć aplikację funkcji systemu Linux hostowanych na platformie Azure. Możesz teraz [wdrażanie projektu funkcji](https://docs.microsoft.com/cli/azure/functionapp/deployment/source?view=azure-cli-latest) do tej aplikacji funkcji. Możesz użyć narzędzia Core usługi Azure Functions do [Tworzenie projektu funkcji](functions-run-local.md#create-a-local-functions-project) na komputerze lokalnym i wdrożyć ją na nową aplikację funkcji systemu Linux.  

> [!div class="nextstepaction"] 
> [Kodowanie i testowanie usługi Azure Functions lokalnie](functions-run-local.md)
