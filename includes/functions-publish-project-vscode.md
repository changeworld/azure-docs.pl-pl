---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 256510f855256e648ae9203f46eb9f66c9ffaed6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77029288"
---
## <a name="publish-the-project-to-azure"></a>Publikowanie projektu na platformie Azure

W tej sekcji utworzysz aplikację funkcji i powiązane zasoby w subskrypcji platformy Azure, a następnie wdrożyć kod. 

1. Wybierz ikonę platformy Azure na pasku aktywności, a następnie w obszarze **Azure: Functions** wybierz przycisk **Wdrażanie do aplikacji funkcji....**

    ![Publikowanie projektu na platformie Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podaj następujące informacje w monitach:

    + **Wybierz subskrypcję:** Wybierz subskrypcję do użycia. Nie zobaczysz tego, jeśli masz tylko jedną subskrypcję.

    + **Wybierz aplikację funkcji na platformie Azure:** Wybierz `+ Create new Function App` (nie `Advanced`). Ten artykuł nie obsługuje [zaawansowanego przepływu publikowania](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options). 
    
    >[!IMPORTANT]
    > Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure. 
    
    + **Wprowadź globalnie unikatową nazwę aplikacji funkcji:** Wpisz nazwę prawidłową w ścieżce adresu URL. Wpisywanie nazwy jest sprawdzane, aby upewnić się, że jest unikatowa w usłudze Azure Functions. 
    
    ::: zone pivot="programming-language-python"
    + **Wybierz środowisko wykonawcze:** Wybierz wersję języka Python, na której działa lokalnie. Za pomocą `python --version` polecenia można sprawdzić wersję.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Wybierz środowisko wykonawcze:** Wybierz wersję pliku Node.js, na której działa lokalnie. Za pomocą `node --version` polecenia można sprawdzić wersję.
    ::: zone-end

    + **Wybierz lokalizację dla nowych zasobów:** Aby uzyskać lepszą wydajność, wybierz [region](https://azure.microsoft.com/regions/) w pobliżu. 
    
1.  Po zakończeniu w ramach subskrypcji tworzone są następujące zasoby platformy Azure:

    + **[Grupa zasobów:](../articles/azure-resource-manager/management/overview.md)** Zawiera wszystkie utworzone zasoby platformy Azure. Nazwa jest oparta na nazwie aplikacji funkcji.
    + **[Konto magazynu:](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** standardowe konto magazynu jest tworzone z unikatową nazwą, która jest oparta na nazwie aplikacji funkcji.
    + **[Plan hostingu:](../articles/azure-functions/functions-scale.md)** Plan zużycia jest tworzony w regionie Zachodnie stany USA, aby hostować aplikację funkcji bezserwerowych.
    + **Aplikacja funkcji:** Projekt jest wdrażany i uruchamia się w tej nowej aplikacji funkcji.
    + **Usługa Application Insights**: Wystąpienie, które jest połączone z aplikacją funkcji, jest tworzone na podstawie nazwy funkcji.

    Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. 
    
1. Wybierz **opcję Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym utworzone zasoby platformy Azure. Jeśli przegapisz powiadomienie, wybierz ikonę dzwonka w prawym dolnym rogu, aby zobaczyć je ponownie.

    ![Tworzenie pełnego powiadomienia](media/functions-publish-project-vscode/function-create-notifications.png)
