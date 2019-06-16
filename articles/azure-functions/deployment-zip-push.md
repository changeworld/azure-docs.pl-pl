---
title: ZIP wdrażanie powiadomień push dla usługi Azure Functions | Dokumentacja firmy Microsoft
description: Możliwości wdrożenia pliku zip usługa wdrażania Kudu należy używać do publikowania usługi Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 08/12/2018
ms.author: glenga
ms.openlocfilehash: 2762e5c4f2b67415a0e42e80a34ae5b34c57adc9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62111203"
---
# <a name="zip-deployment-for-azure-functions"></a>Wdrażanie pliku ZIP dla usługi Azure Functions

W tym artykule opisano sposób wdrażania plików projektu aplikacji funkcji na platformie Azure z pliku zip (skompresowane). Dowiesz się, jak to zrobić w przypadku wdrożenia wypychania, zarówno za pomocą wiersza polecenia platformy Azure, jak i za pomocą interfejsów API REST. [Podstawowe narzędzia usługi Azure Functions](functions-run-local.md) również używa tych interfejsów API wdrożenia podczas publikowania projektu lokalnych na platformę Azure.

Usługa Azure Functions oferuje pełny zakres ciągłe opcje wdrażania i integracji, które są dostarczane przez usługę Azure App Service. Aby uzyskać więcej informacji, zobacz [ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md).

Aby przyspieszyć rozwój, użytkownik może okazać się łatwiejsze do wdrożenia funkcji pliki projektu aplikacji bezpośrednio z pliku zip. Plik zip wdrażania interfejsu API pobiera zawartość pliku .zip i wyodrębnia zawartość do `wwwroot` folderu aplikacji funkcji. To wdrożenie plików zip używa tej samej usługi Kudu tego uprawnienia ciągłej integracji na podstawie wdrożeń, w tym:

+ Usuwanie plików, które zostały utworzone podczas wcześniejszych wdrożeń.
+ Dostosowanie wdrożenia, w tym uruchamianie skryptów wdrażania.
+ Dzienniki wdrożenia.
+ Trwa synchronizowanie wyzwalacze funkcji w [planu zużycie](functions-scale.md) aplikacji funkcji.

Aby uzyskać więcej informacji, zobacz [odwołanie do wdrożenia zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Wymagania dotyczące pliku zip wdrażania

Plik zip, używanej do wdrożenia wypychania musi zawierać wszystkie pliki potrzebne do uruchomienia funkcji.

>[!IMPORTANT]
> Użycie opcji wdrożenia .zip, wszystkie pliki z istniejącego wdrożenia, które nie zostaną znalezione w pliku .zip są usuwane z aplikacji funkcji.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Aplikacja funkcji obejmuje wszystkie pliki i foldery w `wwwroot` katalogu. Wdrażanie plików zip obejmują zawartość `wwwroot` katalogu, ale nie sam katalog. Podczas wdrażania projektu biblioteki klas C#, musi zawierać pliki skompilowanej biblioteki i zależności w `bin` podfolderu w pakiecie .zip.

## <a name="download-your-function-app-files"></a>Pobieranie plików aplikacji — funkcja

Gdy tworzysz na komputerze lokalnym, jest łatwo jest tworzyć plik .zip zawierający folder projektu aplikacji funkcji na komputerze deweloperskim.

Jednak może utworzono funkcji za pomocą edytora w witrynie Azure portal. Możesz pobrać istniejący projekt aplikacji funkcji w jednej z następujących sposobów:

+ **W witrynie Azure portal:**

  1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie przejdź do aplikacji funkcji.

  2. Na **Przegląd** zaznacz **Pobierz zawartość aplikacji**. Wybierz swoje opcje pobierania, a następnie wybierz **Pobierz**.

      ![Pobieranie projektu aplikacji funkcji](./media/deployment-zip-push/download-project.png)

     Plik zip pobranego jest w poprawnym formacie, aby ponownie opublikować aplikację funkcji przy użyciu wdrożenia wypychania .zip. Pobierz portalu można również dodać pliki potrzebne do otwierania aplikacji funkcji bezpośrednio w programie Visual Studio.

+ **Przy użyciu interfejsów API REST:**

    Pobieranie plików z przy użyciu następujących wdrażania pobieranie interfejsu API usługi `<function_app>` projektu: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    W tym `/site/wwwroot/` zapewnia, że plik zip zawiera tylko pliki projektu aplikacji funkcji, a nie całej witryny. Jeśli użytkownik są nie już zalogowany na platformie Azure, poprosimy Cię o tym.  

Możesz również pobrać plik zip z repozytorium GitHub. Po pobraniu repozytorium GitHub w postaci pliku .zip, GitHub dodaje poziomem dodatkowych folderów gałęzi. Oznacza to, folder dodatkowego poziomu nie można wdrożyć pliku .zip bezpośrednio jako pobrany z usługi GitHub. Jeśli korzystasz z repozytorium GitHub do obsługi aplikacji funkcji, należy użyć [ciągłej integracji](functions-continuous-deployment.md) do wdrożenia aplikacji.  

## <a name="cli"></a>Wdrażanie przy użyciu wiersza polecenia platformy Azure

Można użyć wiersza polecenia platformy Azure, aby wyzwolić wdrożenie wypychania. Wypychane wdrażanie pliku zip do aplikacji funkcji przy użyciu [az functionapp deployment źródła config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) polecenia. Aby użyć tego polecenia, należy użyć wiersza polecenia platformy Azure w wersji 2.0.21 lub nowszej. Aby zobaczyć bieżącą wersję wiersza polecenia platformy Azure, którego używasz, należy użyć `az --version` polecenia.

W poniższym poleceniu zastąp `<zip_file_path>` symbolu zastępczego na ścieżkę do lokalizacji pliku zip. Ponadto Zastąp `<app_name>` na unikatową nazwę aplikacji funkcji. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```

To polecenie wdraża pliki projektu z pliku zip pobranego do aplikacji funkcji na platformie Azure. Następnie ponownie uruchamia aplikację. Aby wyświetlić listę wdrożenia dla tej aplikacji funkcji, należy użyć interfejsów API REST.

Podczas korzystania z wiersza polecenia platformy Azure na komputerze lokalnym `<zip_file_path>` jest ścieżka do pliku ZIP na tym komputerze. Można również uruchomić wiersza polecenia platformy Azure w [usługi Azure Cloud Shell](../cloud-shell/overview.md). Korzystając z usługi Cloud Shell, należy najpierw przekazać plik zip wdrożenia do konta usługi Azure Files, która jest skojarzona z usługi Cloud Shell. W takim przypadku `<zip_file_path>` jest lokalizację magazynu, który korzysta z konta usługi Cloud Shell. Aby uzyskać więcej informacji, zobacz [utrwalanie plików w usłudze Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Uruchamianie funkcji z pakietu wdrożenia

Możesz również uruchamiać swoje funkcje bezpośrednio z pliku pakietu wdrażania. Ta metoda pominie krok wdrażania kopiowania plików z pakietu do `wwwroot` katalogu aplikacji funkcji. Zamiast tego pliku pakietu jest instalowane przez środowisko uruchomieniowe usługi Functions i zawartość `wwwroot` katalogu stają się tylko do odczytu.  

Wdrożenie zip współpracuje z tej funkcji, które można włączyć, konfigurując dla ustawienia aplikacji funkcji `WEBSITE_RUN_FROM_PACKAGE` wartości `1`. Aby uzyskać więcej informacji, zobacz [uruchamiać swoje funkcje na podstawie pliku pakietu wdrażania](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
