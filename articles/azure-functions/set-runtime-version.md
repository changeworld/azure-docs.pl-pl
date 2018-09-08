---
title: Jak odwoływać się wersje środowiska uruchomieniowego usługi Azure Functions
description: Usługa Azure Functions obsługuje wiele wersji środowiska uruchomieniowego. Dowiedz się, jak określić wersję środowiska uruchomieniowego aplikacji funkcji hostowanych na platformie Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: ced4b6846d291bfbb718c3346ea588ca9e961d07
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093706"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Jak odwoływać się wersje środowiska uruchomieniowego usługi Azure Functions

Aplikacja funkcji jest uruchamiana od określonej wersji środowiska uruchomieniowego usługi Azure Functions. Istnieją dwie wersje główne: [wersji 1.x i 2.x](functions-versions.md). W tym artykule opisano sposób konfigurowania aplikacji funkcji na platformie Azure do uruchamiania na wybranej wersji. Aby uzyskać informacje o sposobie konfigurowania lokalnego środowiska deweloperskiego dla określonej wersji, zobacz [kodu i testowanie usługi Azure Functions lokalnie](functions-run-local.md).

>[!IMPORTANT]   
> Środowisko uruchomieniowe funkcji platformy Azure 2.0 jest w wersji zapoznawczej i obecnie nie wszystkie funkcje usługi Azure Functions są obsługiwane. Aby uzyskać więcej informacji, zobacz [Przegląd wersje środowiska uruchomieniowego usługi Azure Functions](functions-versions.md).

## <a name="automatic-and-manual-version-updates"></a>Aktualizacje automatyczne i ręczne wersji

Functions umożliwia docelowej określonej wersji środowiska uruchomieniowego przy użyciu `FUNCTIONS_EXTENSION_VERSION` ustawienia aplikacji w aplikacji funkcji. Aplikacja funkcji jest przechowywana na określonej wersji głównych, dopóki nie wybierzesz jawnie można przenieść do nowej wersji.

Jeśli określisz tylko wersję główną ("~ 1" dla 1.x) lub "beta", aby uzyskać 2.x, aplikacja funkcji zostanie automatycznie zaktualizowany do nowej pomocniczej wersji środowiska uruchomieniowego, gdy staną się dostępne. Nowe wersje pomocnicze nie wprowadza zmiany powodujące niezgodność. Jeśli określisz wersji pomocniczej (na przykład "1.0.11360"), aplikacja funkcji jest przechowywana na tej wersji, dopóki nie zmienisz jawnie. 

Gdy publicznie dostępna jest nowa wersja, wiersz w portalu daje możliwość Przenieś w górę do tej wersji. Po przeniesieniu do nowej wersji, zawsze można użyć `FUNCTIONS_EXTENSION_VERSION` ustawienie aplikacji, aby powrócić do poprzedniej wersji.

Zmiana wersji środowiska uruchomieniowego powoduje, że aplikacja funkcji ponownie uruchomić.

Wartości można ustawić w `FUNCTIONS_EXTENSION_VERSION` ustawienie, aby włączyć automatyczne aktualizacje aplikacji są obecnie "~ 1" środowisko uruchomieniowe 1.x i w wersji "beta" dla 2.x.

## <a name="view-the-current-runtime-version"></a>Wyświetl bieżącą wersję środowiska uruchomieniowego

Poniższa procedura umożliwia wyświetlanie wersji środowiska uruchomieniowego, obecnie używana przez aplikację funkcji. 

1. W [witryny Azure portal](https://portal.azure.com), przejdź do aplikacji funkcji i w obszarze **skonfigurowane funkcje**, wybierz **ustawień aplikacji funkcji**. 

    ![Wybierz pozycję Ustawienia aplikacji funkcji](./media/functions-versions/add-update-app-setting.png)

2. W **ustawień aplikacji funkcji** kartę, odszukaj **wersji środowiska uruchomieniowego**. Należy pamiętać, określonej wersji środowiska uruchomieniowego i żądanej wersji głównej. W poniższym przykładzie, funkcje\_rozszerzenia\_wersja aplikacji jest ustawiana `~1`.
 
   ![Wybierz pozycję Ustawienia aplikacji funkcji](./media/functions-versions/function-app-view-version.png)

## <a name="target-a-version-using-the-portal"></a>Docelowa wersja, przy użyciu portalu

Gdy zachodzi potrzeba docelowej wersji innej niż bieżąca wersja główna lub w wersji 2.0, należy ustawić `FUNCTIONS_EXTENSION_VERSION` ustawienia aplikacji.

1. W [witryny Azure portal](https://portal.azure.com), przejdź do aplikacji funkcji i w obszarze **skonfigurowane funkcje**, wybierz **ustawienia aplikacji**.

    ![Wybierz pozycję Ustawienia aplikacji funkcji](./media/functions-versions/add-update-app-setting1a.png)

2. W **ustawienia aplikacji** kartę, Znajdź `FUNCTIONS_EXTENSION_VERSION` ustawienie i zmień wartość na prawidłową wersję 1.x środowiska uruchomieniowego lub `beta` dla wersji 2.0. Tylda za pomocą wersji głównej oznacza, użyj najnowszej wersji tej wersji głównej (na przykład, "~ 1"). 

    ![Ustawienia aplikacji — funkcja](./media/functions-versions/add-update-app-setting2.png)

3. Kliknij przycisk **Zapisz** można zapisać ustawienia aktualizacji aplikacji. 

## <a name="target-a-version-using-azure-cli"></a>Docelowa wersja, przy użyciu wiersza polecenia platformy Azure

 Można również ustawić `FUNCTIONS_EXTENSION_VERSION` z wiersza polecenia platformy Azure. Przy użyciu wiersza polecenia platformy Azure, zaktualizować ustawienia aplikacji w aplikacji funkcji przy użyciu [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) polecenia.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
W tym kodzie Zastąp `<function_app>` nazwę aplikacji funkcji. Zastąp również `<my_resource_group>` nazwą grupy zasobów dla aplikacji funkcji. Zastąp `<version>` przy użyciu prawidłowej wersji 1.x środowiska uruchomieniowego lub `beta` dla wersji 2.0. 

Możesz uruchomić to polecenie z [usługi Azure Cloud Shell](../cloud-shell/overview.md) , wybierając **wypróbuj** w poprzednim przykładzie kodu. Można również użyć [lokalnie wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) do wykonywania tego polecenia po wykonaniu [az login](/cli/azure/reference-index#az-login) do logowania.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przeznaczone dla środowiska uruchomieniowego 2.0 w swojego lokalnego środowiska deweloperskiego](functions-run-local.md)

> [!div class="nextstepaction"]
> [Znajdziesz w informacjach o wersji środowiska uruchomieniowego](https://github.com/Azure/azure-webjobs-sdk-script/releases)
