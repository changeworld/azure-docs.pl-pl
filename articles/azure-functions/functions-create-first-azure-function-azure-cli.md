---
title: Tworzenie pierwszej funkcji z poziomu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Dowiedz się, jak utworzyć pierwszą funkcję platformy Azure do wykonywania bezserwerowego z poziomu interfejsu wiersza polecenia platformy Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 01/24/2018
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 2088844693748f090a67ad56f9b5fba4514d1282
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38488361"
---
# <a name="create-your-first-function-using-the-azure-cli"></a>Tworzenie pierwszej funkcji z poziomu interfejsu wiersza polecenia platformy Azure

W tym temacie szybkiego startu omówiono tworzenie pierwszej funkcji przy użyciu usługi Azure Functions. Za pomocą interfejsu wiersza polecenia platformy Azure zostanie utworzona aplikacja funkcji, która jest [bezserwerową](https://azure.microsoft.com/overview/serverless-computing/) infrastrukturą hostującą funkcję. Sam kod funkcji jest wdrażany z repozytorium przykładów GitHub.    

Poniższe kroki możesz wykonać przy użyciu komputera z systemem Mac, Windows lub Linux. 

## <a name="prerequisites"></a>Wymagania wstępne 

Przed uruchomieniem tego przykładu należy dysponować następującymi elementami:

+ Aktywne konto usługi [GitHub](https://github.com). 
+ Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 


[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji potrzebna jest aplikacja funkcji. Aplikacja funkcji zapewnia środowisko do bezserwerowego wykonywania kodu funkcji. Umożliwia ona grupowanie funkcji w ramach jednostki logicznej, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi. Utwórz aplikację funkcji przy użyciu polecenia [az functionapp create](/cli/azure/functionapp#az_functionapp_create). 

W poniższym poleceniu w miejsce symbolu zastępczego `<app_name>` wstaw unikatową nazwę aplikacji funkcji, a w miejsce symbolu zastępczego `<storage_name>` wstaw nazwę konta magazynu. Nazwa `<app_name>` jest używana jako domyślna domena DNS aplikacji funkcji, więc nazwa ta musi być unikatowa wśród wszystkich aplikacji na platformie Azure. Parametr _deployment-source-url_ to przykładowe repozytorium GitHub, które zawiera funkcję „Hello world” wyzwalaną za pośrednictwem protokołu HTTP.

```azurecli-interactive
az functionapp create --deployment-source-url https://github.com/Azure-Samples/functions-quickstart  \
--resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```
Ustawienie parametru _consumption-plan-location_ oznacza, że funkcja aplikacji jest hostowana w planie hostingu Zużycie. W tym planie zasoby są dodawane dynamicznie zgodnie z wymogami Twoich funkcji i płacisz tylko wtedy, kiedy funkcje są uruchomione. Aby uzyskać więcej informacji, zobacz [Wybieranie odpowiedniego planu hostingu](functions-scale.md). 

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

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
