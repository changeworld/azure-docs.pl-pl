---
title: Tworzenie pierwszej funkcji w systemie Linux z poziomu interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza) | Microsoft Docs
description: Dowiedz się, jak utworzyć pierwszą funkcję platformy Azure działającą w domyślnym obrazie systemu Linux z poziomu interfejsu wiersza polecenia platformy Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: f4285b3e02176f6f734e6de4d02d3c9c26e5524c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429717"
---
# <a name="create-your-first-function-running-on-linux-using-the-azure-cli-preview"></a>Tworzenie pierwszej funkcji działającej w systemie Linux z poziomu interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza)

Usługa Azure Functions umożliwia obsługę funkcji w systemie Linux w domyślnym kontenerze usługi Azure App Service. Możesz również [skorzystać z własnego kontenera niestandardowego](functions-create-function-linux-custom-image.md). Ta funkcja jest obecnie w wersji zapoznawczej i wymaga [środowiska uruchomieniowego usługi Functions 2.0](functions-versions.md), które jest również dostępne w wersji zapoznawczej.

W tym temacie szybkiego startu przedstawiono sposób użycia usługi Azure Functions z poziomu interfejsu wiersza polecenia platformy Azure w celu utworzenia pierwszej aplikacji funkcji w systemie Linux hostowanym w domyślnym kontenerze usługi App Service. Sam kod funkcji jest wdrażany w obrazie z repozytorium przykładów GitHub.    

Poniższe kroki można wykonać na komputerze Mac, w systemie Windows lub w systemie Linux. 

## <a name="prerequisites"></a>Wymagania wstępne 

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

+ Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, na potrzeby tego tematu jest wymagany interfejs wiersza polecenia platformy Azure w wersji 2.0.21 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Tworzenie planu usługi App Service dla systemu Linux

Hosting systemu Linux dla usługi Functions jest obecnie obsługiwany tylko w ramach planu usługi App Service. Hosting w ramach planu Zużycie nie jest jeszcze obsługiwany. Aby dowiedzieć się więcej o hostingu, zobacz [Porównanie planów hostingu usługi Azure Functions](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Tworzenie aplikacji funkcji w systemie Linux

Do obsługi wykonywania funkcji w systemie Linux potrzebna jest aplikacja funkcji. Aplikacja funkcji zapewnia środowisko do wykonywania kodu funkcji. Umożliwia ona grupowanie funkcji w ramach jednostki logicznej, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi. Utwórz aplikację funkcji przy użyciu polecenia [az functionapp create](/cli/azure/functionapp#az-functionapp-create) w ramach planu usługi App Service dla systemu Linux. 

W poniższym poleceniu w miejsce symbolu zastępczego `<app_name>` wstaw unikatową nazwę aplikacji funkcji, a w miejsce symbolu zastępczego `<storage_name>` wstaw nazwę konta magazynu. Nazwa `<app_name>` jest używana jako domyślna domena DNS aplikacji funkcji, więc nazwa ta musi być unikatowa wśród wszystkich aplikacji na platformie Azure. Parametr _deployment-source-url_ to przykładowe repozytorium GitHub, które zawiera funkcję „Hello world” wyzwalaną za pośrednictwem protokołu HTTP.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-source-url https://github.com/Azure-Samples/functions-quickstart-linux
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

>[!NOTE]  
>Repozytorium przykładów aktualnie obejmuje dwa pliki skryptów, [deploy.sh](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/deploy.sh) i [.deployment](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/.deployment). Plik .deployment informuje proces wdrażania, że jako [skrypt wdrożenia niestandardowego](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) ma być używany plik deploy.sh. W bieżącej wersji zapoznawczej skrypty są wymagane do wdrażania aplikacji funkcji w obrazie systemu Linux.  

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
