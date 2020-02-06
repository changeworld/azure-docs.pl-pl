---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 256510f855256e648ae9203f46eb9f66c9ffaed6
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029288"
---
## <a name="publish-the-project-to-azure"></a>Publikowanie projektu na platformie Azure

W tej sekcji utworzysz aplikację funkcji i powiązane zasoby w ramach subskrypcji platformy Azure, a następnie wdrożono swój kod. 

1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz przycisk **Wdróż do aplikacji funkcji...** .

    ![Publikowanie projektu na platformie Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podaj następujące informacje na ekranie:

    + **Wybierz subskrypcję**: wybierz subskrypcję do użycia. Nie zobaczysz tego, jeśli masz tylko jedną subskrypcję.

    + **Wybierz Aplikacja funkcji na platformie Azure**: Wybierz `+ Create new Function App` (nie `Advanced`). Ten artykuł nie obsługuje [zaawansowanego przepływu publikacji](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options). 
    
    >[!IMPORTANT]
    > Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure. 
    
    + **Wprowadź globalnie unikatową nazwę aplikacji funkcji**: wpisz nazwę, która jest prawidłowa w ścieżce URL. Wpisana nazwa zostanie sprawdzona, aby upewnić się, że jest ona unikatowa w Azure Functions. 
    
    ::: zone pivot="programming-language-python"
    + **Wybierz środowisko uruchomieniowe**: Wybierz wersję języka Python, która była uruchomiona lokalnie. Aby sprawdzić swoją wersję, możesz użyć polecenia `python --version`.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Wybierz środowisko uruchomieniowe**: Wybierz wersję środowiska Node. js, która została uruchomiona lokalnie. Aby sprawdzić swoją wersję, możesz użyć polecenia `node --version`.
    ::: zone-end

    + **Wybierz lokalizację dla nowych zasobów**: Aby uzyskać lepszą wydajność, wybierz [region](https://azure.microsoft.com/regions/) w sąsiedztwie. 
    
1.  Po zakończeniu następujące zasoby platformy Azure są tworzone w ramach subskrypcji:

    + **[Grupa zasobów](../articles/azure-resource-manager/management/overview.md)** : zawiera wszystkie utworzone zasoby platformy Azure. Nazwa jest oparta na nazwie aplikacji funkcji.
    + **[Konto magazynu](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : konto magazynu w warstwie Standardowa jest tworzone z unikatową nazwą, która jest oparta na nazwie aplikacji funkcji.
    + **[Plan hostingu](../articles/azure-functions/functions-scale.md)** : plan zużycia jest tworzony w regionie zachodnie stany USA w celu hostowania aplikacji funkcji bezserwerowej.
    + **Aplikacja funkcji**: projekt jest wdrażany do i uruchamiany w tej nowej aplikacji funkcji.
    + **Application Insights**: wystąpienie, które jest połączone z aplikacją funkcji, jest tworzone na podstawie nazwy funkcji.

    Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. 
    
1. Wybierz pozycję **Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym utworzone zasoby platformy Azure. W przypadku odrzucenia powiadomienia wybierz ikonę dzwonka w prawym dolnym rogu, aby zobaczyć ją ponownie.

    ![Utwórz pełne powiadomienie](media/functions-publish-project-vscode/function-create-notifications.png)
