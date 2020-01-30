---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f1553a5c9d55366b2764877b48d0606ff8e0b370
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842191"
---
## <a name="publish-the-project-to-azure"></a>Publikowanie projektu na platformie Azure

W tej sekcji utworzysz aplikację funkcji i powiązane zasoby w ramach subskrypcji platformy Azure, a następnie wdrożono swój kod. 

1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz przycisk **Wdróż do aplikacji funkcji...** .

    ![Publikowanie projektu na platformie Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podaj następujące informacje na ekranie:

    ::: zone pivot="programming-language-csharp,programming-language-powershell"

    | Monit | Wartość | Opis |
    | ------ | ----- | ----- |
    | Wybierz subskrypcję | Twoja subskrypcja | Wyświetlane, gdy masz wiele subskrypcji. |
    | Wybierz aplikacja funkcji na platformie Azure | + Utwórz nowe aplikacja funkcji | Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure. |
    | Wprowadź globalnie unikatową nazwę aplikacji funkcji | Unikatowa nazwa | Prawidłowe znaki dla nazwy aplikacji funkcji to `a-z`, `0-9` i `-`. |
    | Wybierz lokalizację dla nowych zasobów | Region | Wybierz [region](https://azure.microsoft.com/regions/) blisko siebie. | 

    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python"

    | Monit | Wartość | Opis |
    | ------ | ----- | ----- |
    | Wybierz subskrypcję | Twoja subskrypcja | Wyświetlane, gdy masz wiele subskrypcji. |
    | Wybierz aplikacja funkcji na platformie Azure | + Utwórz nowe aplikacja funkcji | Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure. |
    | Wprowadź globalnie unikatową nazwę aplikacji funkcji | Unikatowa nazwa | Prawidłowe znaki dla nazwy aplikacji funkcji to `a-z`, `0-9` i `-`. |
    | Wybierz środowisko uruchomieniowe | Twoja wersja | Wybierz wersję językową, która została uruchomiona lokalnie. |
    | Wybierz lokalizację dla nowych zasobów | Region | Wybierz [region](https://azure.microsoft.com/regions/) blisko siebie. | 

    ::: zone-end

    
1.  Po zakończeniu następujące zasoby platformy Azure są tworzone w ramach subskrypcji:

    + **[Grupa zasobów](../articles/azure-resource-manager/management/overview.md)** : zawiera wszystkie utworzone zasoby platformy Azure. Nazwa jest oparta na nazwie aplikacji funkcji.
    + **[Konto magazynu](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : konto magazynu w warstwie Standardowa jest tworzone z unikatową nazwą, która jest oparta na nazwie aplikacji funkcji.
    + **[Plan hostingu](../articles/azure-functions/functions-scale.md)** : plan zużycia jest tworzony w regionie zachodnie stany USA w celu hostowania aplikacji funkcji bezserwerowej.
    + **Aplikacja funkcji**: projekt jest wdrażany do i uruchamiany w tej nowej aplikacji funkcji.
    + **[Application Insights]()** : wystąpienie, które jest połączone z aplikacją funkcji, jest tworzone na podstawie nazwy funkcji.

    Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. 
    
1. Wybierz pozycję **Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym utworzone zasoby platformy Azure.

    ![Utwórz pełne powiadomienie](media/functions-publish-project-vscode/function-create-notifications.png)

1. Wróć do obszaru **Azure: Functions** na pasku bocznym, a następnie rozwiń nową aplikację funkcji w ramach subskrypcji. Rozwiń węzeł **funkcje**, kliknij prawym przyciskiem myszy (Windows) lub Ctrl + kliknięcie (MacOS) w **HttpExample**, a następnie wybierz polecenie **Kopiuj adres URL funkcji**.

    ![Skopiuj adres URL funkcji dla nowego wyzwalacza HTTP](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
