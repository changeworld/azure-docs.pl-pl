---
title: Jak korzystać z usługi Azure Functions wersji środowiska uruchomieniowego
description: Środowisko Azure Functions obsługuje wiele wersji środowiska uruchomieniowego. Dowiedz się, jak określić wersję środowiska uruchomieniowego aplikacji funkcji obsługiwane na platformie Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 6fc84642050f4b7acfa2e3c5b4518135d6a97171
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29843395"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Jak korzystać z usługi Azure Functions wersji środowiska uruchomieniowego

Aplikacja funkcji jest uruchamiane w określonej wersji środowiska uruchomieniowego usługi Azure Functions. Istnieją dwa główne wersje: [1.x i 2.x](functions-versions.md). W tym artykule opisano sposób konfigurowania aplikacji funkcji na platformie Azure do uruchomienia na wersję, którą wybierzesz. Aby uzyskać informacje o sposobie konfigurowania lokalne Środowisko deweloperskie dla określonej wersji, zobacz [kodu oraz testów usługi Azure Functions lokalnie](functions-run-local.md).

>[!IMPORTANT]   
> Środowisko uruchomieniowe Functions Azure 2.0 jest w wersji zapoznawczej i obecnie nie wszystkie funkcje usługi Azure Functions są obsługiwane. Aby uzyskać więcej informacji, zobacz [omówienie wersji środowiska uruchomieniowego usługi Azure Functions](functions-versions.md).

## <a name="automatic-and-manual-version-updates"></a>Aktualizacje automatycznej i ręcznej wersji

Funkcje umożliwia docelowych przy użyciu określonej wersji środowiska uruchomieniowego `FUNCTIONS_EXTENSION_VERSION` ustawienia aplikacji w aplikacji funkcji. Funkcja aplikacji jest przechowywana na określonej wersji głównej, dopóki nie wybierzesz jawnie można przenieść do nowej wersji.

Jeśli określisz tylko wersję główną ("~ 1" dla 1.x) lub "beta" dla 2.x aplikacji funkcji jest automatycznie aktualizowane do nowej wersji pomocniczych środowiska uruchomieniowego udostępniane. Nowe wersje pomocnicze nie znajdą się istotne zmiany. Jeśli określono wersję pomocniczą (na przykład "1.0.11360"), dopóki jawnie zmienić aplikacji funkcji jest przechowywana w tej wersji. 

Gdy publicznie dostępna jest nowa wersja, monitu w aplikacji portal daje możliwość Przenieś w górę do tej wersji. Po przeniesieniu do nowej wersji, zawsze można użyć `FUNCTIONS_EXTENSION_VERSION` ustawienie aplikacji, aby powrócić do poprzedniej wersji.

Zmiana wersji środowiska uruchomieniowego powoduje, że ponowne uruchomienie aplikacji funkcji.

Wartości można ustawić w `FUNCTIONS_EXTENSION_VERSION` aplikacji ustawienie, aby włączyć Aktualizacje automatyczne są obecnie "~ 1" dla środowiska uruchomieniowego 1.x i wersji "beta" dla 2.x.

## <a name="view-the-current-runtime-version"></a>Wyświetl bieżącą wersję środowiska uruchomieniowego

Poniższa procedura umożliwia wyświetlanie wersji środowiska uruchomieniowego aktualnie używany przez aplikację funkcji. 

1. W [portalu Azure](https://portal.azure.com), przejdź do aplikacji funkcji i w obszarze **skonfigurować funkcje**, wybierz **funkcji ustawienia aplikacji**. 

    ![Wybierz ustawienia aplikacji — funkcja](./media/functions-versions/add-update-app-setting.png)

2. W **funkcji ustawienia aplikacji** zlokalizuj **wersja środowiska uruchomieniowego**. Należy pamiętać, wersja określonego środowiska uruchomieniowego i żądanej wersji głównej. W poniższym przykładzie funkcje\_rozszerzenia\_wersji aplikacji został ustawiony na `~1`.
 
   ![Wybierz ustawienia aplikacji — funkcja](./media/functions-versions/function-app-view-version.png)

## <a name="target-a-version-using-the-portal"></a>Docelowa wersja przy użyciu portalu

Jeśli potrzebujesz docelową wersję niż bieżąca wersja główna lub w wersji 2.0, należy skonfigurować `FUNCTIONS_EXTENSION_VERSION` ustawienia aplikacji.

1. W [portalu Azure](https://portal.azure.com), przejdź do aplikacji funkcji i w obszarze **skonfigurować funkcje**, wybierz **ustawienia aplikacji**.

    ![Wybierz ustawienia aplikacji — funkcja](./media/functions-versions/add-update-app-setting1a.png)

2. W **ustawienia aplikacji** karcie, Znajdź `FUNCTIONS_EXTENSION_VERSION` ustawienie i zmień wartość na prawidłową wersję środowiska uruchomieniowego 1.x lub `beta` w wersji 2.0. Tylda z wersji głównej oznacza używać najnowszej wersji głównej wersji (na przykład "~ 1"). 

    ![Ustawienia aplikacji — funkcja](./media/functions-versions/add-update-app-setting2.png)

3. Kliknij przycisk **zapisać** można zapisać ustawienia aplikacji. 

## <a name="target-a-version-using-azure-cli"></a>Docelowa wersja przy użyciu wiersza polecenia platformy Azure

 Można również ustawić `FUNCTIONS_EXTENSION_VERSION` z wiersza polecenia platformy Azure. Przy użyciu wiersza polecenia platformy Azure, zaktualizuj ustawienia aplikacji w aplikacji funkcji z [az functionapp config appsettings zestaw](/cli/azure/functionapp/config/appsettings#set) polecenia.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
W tym kodzie Zamień `<function_app>` z nazwą funkcji aplikacji. Zastąp również `<my_resource_group>` z nazwą grupy zasobów dla aplikacji funkcja. Zastąp `<version>` z prawidłową wersją środowiska uruchomieniowego 1.x lub `beta` w wersji 2.0. 

Możesz uruchomić to polecenie z [powłoki chmury Azure](../cloud-shell/overview.md) , wybierając **wypróbuj** w poprzednim przykładzie kodu. Można również użyć [lokalnie interfejsu wiersza polecenia Azure](/cli/azure/install-azure-cli) do wykonywania tego polecenia po wykonaniu [logowania az](/cli/azure/reference-index#az_login) do logowania.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Do wykonywania 2.0 w środowisku projektowania lokalnego](functions-run-local.md)

> [!div class="nextstepaction"]
> [Zobacz informacje o wersji dla wersji środowiska uruchomieniowego](https://github.com/Azure/azure-webjobs-sdk-script/releases)