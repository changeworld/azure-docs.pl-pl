---
title: Jak odwoływać się wersje środowiska uruchomieniowego usługi Azure Functions
description: Usługa Azure Functions obsługuje wiele wersji środowiska uruchomieniowego. Dowiedz się, jak określić wersję środowiska uruchomieniowego aplikacji funkcji hostowanych na platformie Azure.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: glenga
ms.openlocfilehash: 2047f11272c6154d4443e889eff24401c2f73afa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067692"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Jak odwoływać się wersje środowiska uruchomieniowego usługi Azure Functions

Aplikacja funkcji jest uruchamiana od określonej wersji środowiska uruchomieniowego usługi Azure Functions. Istnieją dwie wersje główne: [w wersji 1.x i 2.x](functions-versions.md). Domyślnie funkcja aplikacji, które zostały utworzone w wersji 2.x środowiska uruchomieniowego. W tym artykule opisano sposób konfigurowania aplikacji funkcji na platformie Azure do uruchamiania na wybranej wersji. Aby uzyskać informacje o sposobie konfigurowania lokalnego środowiska deweloperskiego dla określonej wersji, zobacz [kodu i testowanie usługi Azure Functions lokalnie](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Aktualizacje automatyczne i ręczne wersji

Usługa Azure Functions umożliwia docelowej określonej wersji środowiska uruchomieniowego przy użyciu `FUNCTIONS_EXTENSION_VERSION` ustawienia aplikacji w aplikacji funkcji. Aplikacja funkcji jest przechowywana na określonej wersji głównych, dopóki nie wybierzesz jawnie można przenieść do nowej wersji.

Jeśli określisz tylko wersję główną ("~ 2" dla 2.x lub "~ 1" dla 1.x), aplikacja funkcji zostanie automatycznie zaktualizowany do nowej pomocniczej wersji środowiska uruchomieniowego, gdy staną się dostępne. Nowe wersje pomocnicze nie wprowadza zmiany powodujące niezgodność. Jeśli określisz wersji pomocniczej (na przykład "2.0.12345"), aplikacja funkcji jest przypięta do tej określonej wersji, dopóki nie zmienisz jawnie.

> [!NOTE]
> Jeśli przypiąć do określonej wersji usługi Azure Functions, a następnie spróbuj Opublikuj na platformie Azure przy użyciu programu Visual Studio, okna dialogowego wyświetli monit o aktualizację do najnowszej wersji, lub Anuluj publikowanie. Aby tego uniknąć, należy dodać `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` właściwości w swojej `.csproj` pliku.

Gdy publicznie dostępna jest nowa wersja, wiersz w portalu daje możliwość Przenieś w górę do tej wersji. Po przeniesieniu do nowej wersji, zawsze można użyć `FUNCTIONS_EXTENSION_VERSION` ustawienie aplikacji, aby powrócić do poprzedniej wersji.

Zmiana wersji środowiska uruchomieniowego powoduje, że aplikacja funkcji ponownie uruchomić.

Wartości można ustawić w `FUNCTIONS_EXTENSION_VERSION` ustawienie, aby włączyć automatyczne aktualizacje aplikacji są obecnie "~ 1" środowisko uruchomieniowe 1.x i "~ 2" dla 2.x.

## <a name="view-and-update-the-current-runtime-version"></a>Wyświetlanie i aktualizowanie bieżącą wersję środowiska uruchomieniowego

Można zmienić wersji środowiska uruchomieniowego używane przez aplikację funkcji. Ze względu na potencjał istotne zmiany należy tylko zmieniać wersji środowiska uruchomieniowego, przed utworzeniem jakichkolwiek funkcji w aplikacji funkcji. Chociaż wersja środowiska uruchomieniowego jest określana przez `FUNCTIONS_EXTENSION_VERSION` ustawienia, należy wprowadzić tę zmianę w witrynie Azure portal, a nie zmieniając ustawienie bezpośrednio. Jest to spowodowane portalu weryfikuje zmiany i sprawia, że inne powiązane zmiany zgodnie z potrzebami.

### <a name="from-the-azure-portal"></a>W witrynie Azure portal

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> W witrynie Azure portal, nie można zmienić wersji środowiska uruchomieniowego dla aplikacji funkcji, która już zawiera funkcje.

### <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Możesz również wyświetlić i ustawić `FUNCTIONS_EXTENSION_VERSION` z wiersza polecenia platformy Azure.

>[!NOTE]
>Ponieważ pozostałe ustawienia mogą mieć wpływ na wersji środowiska uruchomieniowego, należy zmienić wersję w portalu. Portal automatycznie sprawia, że innych wymaganych aktualizacji, takich jak stos wersji i środowisko uruchomieniowe Node.js, gdy zmienią się wersje środowiska uruchomieniowego.  

Przy użyciu wiersza polecenia platformy Azure, Wyświetl bieżącą wersję środowiska uruchomieniowego za pomocą [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) polecenia.

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

W tym kodzie Zastąp `<function_app>` nazwę aplikacji funkcji. Zastąp również `<my_resource_group>` nazwą grupy zasobów dla aplikacji funkcji. 

Zostanie wyświetlony `FUNCTIONS_EXTENSION_VERSION` następujące dane wyjściowe, które zostały obcięte w celu uściślenia:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

Możesz zaktualizować `FUNCTIONS_EXTENSION_VERSION` ustawienie w aplikacji funkcji przy użyciu [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) polecenia.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Zastąp `<function_app>` nazwę aplikacji funkcji. Zastąp również `<my_resource_group>` nazwą grupy zasobów dla aplikacji funkcji. Ponadto Zastąp `<version>` przy użyciu prawidłowej wersji 1.x środowiska uruchomieniowego lub `~2` wersji 2.x.

Możesz uruchomić to polecenie z [usługi Azure Cloud Shell](../cloud-shell/overview.md) , wybierając **wypróbuj** w poprzednim przykładzie kodu. Można również użyć [lokalnie wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) do wykonywania tego polecenia po wykonaniu [az login](/cli/azure/reference-index#az-login) do logowania.



## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przeznaczone dla środowiska uruchomieniowego 2.0 w swojego lokalnego środowiska deweloperskiego](functions-run-local.md)

> [!div class="nextstepaction"]
> [Znajdziesz w informacjach o wersji środowiska uruchomieniowego](https://github.com/Azure/azure-webjobs-sdk-script/releases)
