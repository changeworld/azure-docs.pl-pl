---
title: Jak kierować reklamy na wersje środowiska uruchomieniowego usługi Azure Functions
description: Usługa Azure Functions obsługuje wiele wersji środowiska wykonawczego. Dowiedz się, jak określić wersję środowiska wykonawczego aplikacji funkcji hostowanych na platformie Azure.
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 5a71338b1b9735d7e7494dc2667bd7addf5d4a53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77151959"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Jak kierować reklamy na wersje środowiska uruchomieniowego usługi Azure Functions

Aplikacja funkcji działa w określonej wersji środowiska wykonawczego usługi Azure Functions. Istnieją trzy główne wersje: [1.x, 2.x i 3.x](functions-versions.md). Domyślnie aplikacje funkcji są tworzone w wersji 2.x środowiska wykonawczego. W tym artykule wyjaśniono, jak skonfigurować aplikację funkcji na platformie Azure, aby działała w wersji, którą wybierzesz. Aby uzyskać informacje dotyczące konfigurowania lokalnego środowiska programistycznego dla określonej wersji, zobacz [Kod i testowanie usług Azure Functions lokalnie.](functions-run-local.md)

## <a name="automatic-and-manual-version-updates"></a>Automatyczne i ręczne aktualizacje wersji

Usługa Azure Functions umożliwia kierowanie określonej wersji `FUNCTIONS_EXTENSION_VERSION` środowiska wykonawczego przy użyciu ustawienia aplikacji w aplikacji funkcji. Aplikacja funkcji jest przechowywana w określonej wersji głównej, dopóki jawnie nie zdecydujesz się przejść do nowej wersji.

Jeśli określisz tylko wersję główną, aplikacja funkcji jest automatycznie aktualizowana do nowych wersji pomocniczych środowiska wykonawczego, gdy staną się dostępne. Nowe wersje pomocnicze nie powinny wprowadzać przełomowych zmian. Jeśli określisz wersję pomocniczą (na przykład "2.0.12345"), aplikacja funkcji jest przypięta do tej określonej wersji, dopóki nie zostanie jawnie zmieniona.

> [!NOTE]
> Jeśli przypniesz do określonej wersji usługi Azure Functions, a następnie spróbujesz opublikować na platformie Azure za pomocą programu Visual Studio, pojawi się okno dialogowe z monitem o zaktualizowanie do najnowszej wersji lub anulowanie publikowania. Aby tego uniknąć, `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` dodaj `.csproj` właściwość w pliku.

Gdy nowa wersja jest publicznie dostępna, monit w portalu daje możliwość przejścia do tej wersji. Po przejściu do nowej wersji, `FUNCTIONS_EXTENSION_VERSION` zawsze można użyć ustawienia aplikacji, aby przejść z powrotem do poprzedniej wersji.

W poniższej `FUNCTIONS_EXTENSION_VERSION` tabeli przedstawiono wartości dla każdej wersji głównej, aby włączyć aktualizacje automatyczne:

| Wersja główna | `FUNCTIONS_EXTENSION_VERSION`Wartość |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Zmiana wersji środowiska wykonawczego powoduje ponowne uruchomienie aplikacji funkcji.

## <a name="view-and-update-the-current-runtime-version"></a>Wyświetlanie i aktualizowanie bieżącej wersji środowiska wykonawczego

Można zmienić wersję środowiska wykonawczego używaną przez aplikację funkcji. Ze względu na potencjał zmiany podziału, można zmienić tylko wersję środowiska wykonawczego przed utworzeniem żadnych funkcji w aplikacji funkcji. 

> [!IMPORTANT]
> Mimo że wersja środowiska wykonawczego `FUNCTIONS_EXTENSION_VERSION` jest określana przez ustawienie, należy wprowadzić tę zmianę w witrynie Azure portal, a nie zmieniając ustawienie bezpośrednio. Dzieje się tak, ponieważ portal sprawdza poprawność zmian i wprowadza inne powiązane zmiany w razie potrzeby.

### <a name="from-the-azure-portal"></a>Z witryny Azure Portal

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Za pomocą witryny Azure portal, nie można zmienić wersję środowiska wykonawczego dla aplikacji funkcji, która już zawiera funkcje.

### <a name="from-the-azure-cli"></a><a name="view-and-update-the-runtime-version-using-azure-cli"></a>Z interfejsu wiersza polecenia platformy Azure

Można również wyświetlić `FUNCTIONS_EXTENSION_VERSION` i ustawić z interfejsu wiersza polecenia platformy Azure.

>[!NOTE]
>Ponieważ na inne ustawienia może mieć wpływ wersja środowiska wykonawczego, należy zmienić wersję w portalu. Portal automatycznie wprowadza inne potrzebne aktualizacje, takie jak wersja Node.js i stos środowiska wykonawczego, po zmianie wersji środowiska uruchomieniowego.  

Korzystając z interfejsu wiersza polecenia platformy Azure, wyświetl bieżącą wersję środowiska wykonawczego za pomocą polecenia [az functionapp config appsettings set.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

W tym kodzie zastąp `<function_app>` nazwą aplikacji funkcji. Należy `<my_resource_group>` również zastąpić nazwą grupy zasobów dla aplikacji funkcji. 

Zobaczysz następujące `FUNCTIONS_EXTENSION_VERSION` dane wyjściowe, które zostały obcięty dla jasności:

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

Ustawienie w `FUNCTIONS_EXTENSION_VERSION` aplikacji funkcji można zaktualizować za pomocą polecenia [az functionapp config appsettings set.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Zamień `<function_app>` na nazwę aplikacji funkcyjnej. Należy `<my_resource_group>` również zastąpić nazwą grupy zasobów dla aplikacji funkcji. Należy również `<version>` zastąpić prawidłową wersją środowiska wykonawczego `~2` 1.x lub wersją 2.x.

To polecenie można uruchomić z [usługi Azure Cloud Shell,](../cloud-shell/overview.md) wybierając **pozycję Wypróbuj go** w poprzednim przykładzie kodu. Można również użyć [interfejsu wiersza polecenia platformy Azure lokalnie,](/cli/azure/install-azure-cli) aby wykonać to polecenie po wykonaniu [logowania az,](/cli/azure/reference-index#az-login) aby się zalogować.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Kierowanie środowiska wykonawczego 2.0 w lokalnym środowisku programistycznym](functions-run-local.md)

> [!div class="nextstepaction"]
> [Zobacz Informacje o wersji dla wersji środowiska wykonawczego](https://github.com/Azure/azure-webjobs-sdk-script/releases)
