---
title: Wdrożenie zip push dla usług Azure Functions
description: Użyj obiektów wdrażania plików zip usługi wdrażania Kudu, aby opublikować usługi Azure Functions.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: 6bda0859ca4741fe74f572b204e40130c56c46fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769675"
---
# <a name="zip-deployment-for-azure-functions"></a>Wdrażanie pliku zip dla usługi Azure Functions

W tym artykule opisano sposób wdrażania plików projektu aplikacji funkcji na platformie Azure z pliku .zip (skompresowanego). Dowiesz się, jak wykonać wdrożenie wypychanego, zarówno przy użyciu interfejsu wiersza polecenia platformy Azure, jak i przy użyciu interfejsów API REST. [Podstawowe narzędzia azure functions również](functions-run-local.md) używa tych interfejsów API wdrażania podczas publikowania projektu lokalnego na platformie Azure.

Usługa Azure Functions ma pełny zakres opcji ciągłego wdrażania i integracji, które są dostarczane przez usługę Azure App Service. Aby uzyskać więcej informacji, zobacz [Ciągłe wdrażanie usług Azure Functions](functions-continuous-deployment.md).

Aby przyspieszyć rozwój, może okazać się łatwiejsze do wdrożenia plików projektu aplikacji funkcji bezpośrednio z pliku zip. Interfejs API wdrażania .zip pobiera zawartość pliku zip i wyodrębnia zawartość do folderu `wwwroot` aplikacji funkcji. To wdrożenie pliku zip używa tej samej usługi Kudu, która obsługuje ciągłe wdrożenia oparte na integracji, w tym:

+ Usuwanie plików, które pozostały z wcześniejszych wdrożeń.
+ Dostosowywanie wdrażania, w tym uruchamianie skryptów wdrażania.
+ Dzienniki wdrażania.
+ Wyzwalacze funkcji synchronizacji w aplikacji funkcji [planu zużycia.](functions-scale.md)

Aby uzyskać więcej informacji, zobacz [odwołanie do wdrożenia zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Wdrażanie wymagań dotyczących plików zip

Plik zip używany do wdrażania wypychania musi zawierać wszystkie pliki potrzebne do uruchomienia funkcji.

>[!IMPORTANT]
> Podczas korzystania z wdrożenia .zip wszystkie pliki z istniejącego wdrożenia, które nie znajdują się w pliku zip, są usuwane z aplikacji funkcji.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Aplikacja funkcyjna zawiera wszystkie pliki i `wwwroot` foldery w katalogu. Wdrożenie pliku zip zawiera zawartość `wwwroot` katalogu, ale nie sam katalog. Podczas wdrażania projektu biblioteki klas języka C# należy dołączyć skompilowane pliki biblioteki i zależności w `bin` podfolderze w pakiecie zip.

## <a name="download-your-function-app-files"></a>Pobieranie plików aplikacji funkcji

Podczas tworzenia na komputerze lokalnym można łatwo utworzyć plik zip folderu projektu aplikacji funkcji na komputerze deweloperskim.

Jednak być może utworzono funkcje przy użyciu edytora w witrynie Azure portal. Istniejący projekt aplikacji funkcji można pobrać w jeden z następujących sposobów:

+ **Z witryny Azure portal:**

  1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie przejdź do aplikacji funkcji.

  2. Na karcie **Przegląd** wybierz pozycję **Pobierz zawartość aplikacji**. Wybierz opcje pobierania, a następnie wybierz pozycję **Pobierz**.

      ![Pobierz projekt aplikacji funkcji](./media/deployment-zip-push/download-project.png)

     Pobrany plik zip jest w odpowiednim formacie, który ma zostać ponownie opublikowany w aplikacji funkcji przy użyciu wdrożenia wypychania .zip. Pobieranie portalu można również dodać pliki potrzebne do otwarcia aplikacji funkcji bezpośrednio w programie Visual Studio.

+ **Korzystanie z interfejsów API REST:**

    Użyj następującego interfejsu API GET wdrożenia, aby pobrać pliki z `<function_app>` projektu: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    W `/site/wwwroot/` tym upewnia się, że plik zip zawiera tylko pliki projektu aplikacji funkcji, a nie całą witrynę. Jeśli nie jesteś jeszcze zalogowany na platformie Azure, zostaniesz poproszony o to.  

Możesz również pobrać plik zip z repozytorium GitHub. Po pobraniu repozytorium GitHub jako pliku zip, GitHub dodaje dodatkowy poziom folderu dla gałęzi. Ten dodatkowy poziom folderu oznacza, że nie można wdrożyć pliku zip bezpośrednio po pobraniu go z gitHub. Jeśli używasz repozytorium GitHub do obsługi aplikacji funkcji, należy użyć [ciągłej integracji](functions-continuous-deployment.md) do wdrażania aplikacji.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Za pomocą narzędzia Azure CLI można wyzwolić wdrożenie wypychania. Wypychanie pliku zip w aplikacji funkcji za pomocą polecenia [az functionapp deployment source config-zip.](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) Aby użyć tego polecenia, należy użyć interfejsu wiersza polecenia platformy Azure w wersji 2.0.21 lub nowszej. Aby zobaczyć, jakiej wersji interfejsu `az --version` wiersza polecenia platformy Azure używasz, użyj polecenia.

W poniższym poleceniu zastąp `<zip_file_path>` symbol zastępczy ścieżką do lokalizacji pliku zip. Ponadto zastąp `<app_name>` unikatową nazwą aplikacji `<resource_group>` funkcji i zastąp nazwą grupy zasobów.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

To polecenie wdraża pliki projektu z pobranego pliku zip do aplikacji funkcji na platformie Azure. Następnie uruchamia ponownie aplikację. Aby wyświetlić listę wdrożeń dla tej aplikacji funkcji, należy użyć interfejsów API REST.

Podczas korzystania z interfejsu wiersza polecenia `<zip_file_path>` platformy Azure na komputerze lokalnym jest ścieżka do pliku zip na komputerze. Można również uruchomić narzędzie interfejsu wiersza polecenia platformy Azure w [usłudze Azure Cloud Shell](../cloud-shell/overview.md). Korzystając z usługi Cloud Shell, należy najpierw przekazać plik zip wdrożenia do konta usługi Azure Files, które jest skojarzone z powłoką chmury. W takim `<zip_file_path>` przypadku jest to lokalizacja magazynu, której używa twoje konto Cloud Shell. Aby uzyskać więcej informacji, zobacz [Utrwalanie plików w usłudze Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Uruchamianie funkcji z pakietu wdrożeniowego

Można również uruchomić funkcje bezpośrednio z pliku pakietu wdrażania. Ta metoda pomija etap wdrażania kopiowania plików z `wwwroot` pakietu do katalogu aplikacji funkcji. Zamiast tego plik pakietu jest instalowany przez środowisko wykonawcze Functions, a zawartość `wwwroot` katalogu staje się tylko do odczytu.  

Wdrożenie zip integruje się z tą funkcją, `WEBSITE_RUN_FROM_PACKAGE` którą można `1`włączyć, ustawiając ustawienie aplikacji funkcji na wartość . Aby uzyskać więcej informacji, zobacz [Uruchamianie funkcji z pliku pakietu wdrażania](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ciągłe wdrażanie dla usług Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
