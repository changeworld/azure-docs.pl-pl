---
title: Ciągłe wdrażanie dla usługi Azure Functions
description: Użyj funkcji ciągłego wdrażania usługi Azure App Service, aby opublikować swoje funkcje.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277026"
---
# <a name="continuous-deployment-for-azure-functions"></a>Ciągłe wdrażanie dla usługi Azure Functions

Usługi Azure Functions umożliwiają ciągłe wdrażanie kodu przy użyciu [integracji formantów źródłowych.](functions-deployment-technologies.md#source-control) Integracja z kontrolą źródła umożliwia przepływ pracy, w którym aktualizacja kodu wyzwala wdrożenie na platformie Azure. Jeśli jesteś nowym użytkownikiem usługi Azure Functions, rozpocznij pracę, przeglądając [omówienie funkcji platformy Azure.](functions-overview.md)

Ciągłe wdrażanie jest dobrym rozwiązaniem dla projektów, w których można zintegrować wiele i częste wkłady. Korzystając z ciągłego wdrażania, można zachować jedno źródło prawdy dla kodu, co pozwala zespołom łatwo współpracować. Ciągłe wdrażanie w usłudze Azure Functions można skonfigurować z następujących lokalizacji kodu źródłowego:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Jednostką wdrażania dla funkcji na platformie Azure jest aplikacja funkcji. Wszystkie funkcje w aplikacji funkcji są wdrażane w tym samym czasie. Po włączeniu ciągłego wdrażania dostęp do kodu funkcji w witrynie Azure portal jest skonfigurowany jako tylko do *odczytu,* ponieważ źródło prawdy jest ustawione w innym miejscu.

## <a name="requirements-for-continuous-deployment"></a>Wymagania dotyczące ciągłego wdrażania

Aby ciągłe wdrażanie zakończyło się pomyślnie, struktura katalogów musi być zgodna z podstawową strukturą folderów, których oczekuje usługa Azure Functions.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> Ciągłe wdrażanie nie jest jeszcze obsługiwane dla aplikacji systemu Linux działających w planie zużycia. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Konfigurowanie ciągłego wdrażania

Aby skonfigurować ciągłe wdrażanie dla istniejącej aplikacji funkcji, wykonaj następujące kroki. Kroki pokazują integrację z repozytorium GitHub, ale podobne kroki dotyczą repozytorium platformy Azure lub innych repozytoriów kodu źródłowego.

1. W aplikacji funkcji w [witrynie Azure portal](https://portal.azure.com)wybierz pozycję**Centrum wdrażania** **funkcji** > platformy .

    ![Otwórz Centrum wdrażania](./media/functions-continuous-deployment/platform-features.png)

2. W **Centrum wdrażania**wybierz pozycję **GitHub**, a następnie wybierz pozycję **Autoryzuj**. Jeśli masz już autoryzowaną gitHub, wybierz pozycję **Kontynuuj**. 

    ![Centrum wdrażania usługi Azure App Service](./media/functions-continuous-deployment/github.png)

3. W usłudze GitHub wybierz przycisk **Autoryzuj usługę AzureAppService.** 

    ![Autoryzuj usługę Azure App Service](./media/functions-continuous-deployment/authorize.png)
    
    W **Centrum wdrażania** w witrynie Azure portal wybierz pozycję **Kontynuuj**.

4. Wybierz jednego z następujących dostawców kompilacji:

    * **Usługa kompilacji usługi app service:** najlepiej, gdy nie potrzebujesz kompilacji lub jeśli potrzebujesz kompilacji ogólnej.
    * **Potoki platformy Azure (wersja zapoznawcza)**: Najlepiej, gdy potrzebujesz większej kontroli nad kompilacją. Ten dostawca jest obecnie w wersji zapoznawczej.

    ![Wybierz dostawcę kompilacji](./media/functions-continuous-deployment/build.png)

5. Skonfiguruj informacje specyficzne dla określonej opcji kontroli źródła. W przypadku gitHub należy wprowadzić lub wybrać wartości dla **organizacji,** **repozytorium**i **gałęzi.** Wartości są oparte na lokalizacji kodu. Następnie wybierz przycisk **Kontynuuj**.

    ![Konfigurowanie usługi GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Przejrzyj wszystkie szczegóły, a następnie wybierz pozycję **Zakończ,** aby ukończyć konfigurację wdrożenia.

    ![Podsumowanie](./media/functions-continuous-deployment/summary.png)

Po zakończeniu procesu cały kod z określonego źródła jest wdrażany w aplikacji. W tym momencie zmiany w źródle wdrożenia wyzwalają wdrożenie tych zmian w aplikacji funkcji na platformie Azure.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Przenoszenie istniejących funkcji do ciągłego wdrażania

Jeśli masz już napisane funkcje w [witrynie Azure portal](https://portal.azure.com) i chcesz pobrać zawartość aplikacji przed przejściem do ciągłego wdrażania, przejdź do karty **Przegląd** aplikacji funkcji. Wybierz przycisk **Pobierz zawartość aplikacji.**

![Pobieranie zawartości aplikacji](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Po skonfigurowaniu ciągłej integracji nie można już edytować plików źródłowych w portalu Functions.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
