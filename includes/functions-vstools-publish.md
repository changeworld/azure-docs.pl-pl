---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 8a4fbb3803f2eed4f961792a29a6bf8a327ea208
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80502196"
---
1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

2. W **polu Wybierz cel publikacji**użyj opcji publikowania określonych w poniższej tabeli: 

    | Opcja      | Opis                                |
    | ------------ |  -------------------------------------------------- |
    | **Aplikacja funkcji platformy Azure** | Tworzenie aplikacji funkcji w środowisku chmury platformy Azure. | 
    | **Tworzenie nowych** | Nowa aplikacja funkcji, z powiązanymi zasobami, jest tworzony na platformie Azure. <br/>Jeśli wybierzesz **Wybierz istniejące**, wszystkie pliki w istniejącej aplikacji funkcji na platformie Azure są zastępowane przez pliki z projektu lokalnego. Tej opcji należy używać tylko podczas ponownego publikowania aktualizacji istniejącej aplikacji funkcji. |
    | **Uruchamianie z pliku pakietu** | Aplikacja funkcji jest wdrażana przy użyciu [zip deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) z włączonym trybem [Uruchamianie od pakietu.](../articles/azure-functions/run-functions-from-deployment-package.md) To wdrożenie, co skutkuje lepszą wydajnością, jest zalecanym sposobem uruchamiania funkcji. <br/>Jeśli nie używasz tej opcji, przed opublikowaniem na platformie Azure należy zatrzymać uruchamianie projektu aplikacji funkcji. |

    ![Wybieranie miejsca docelowego publikacji](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)


3. Wybierz pozycję **Publikuj**. Jeśli nie zalogowano się jeszcze do konta platformy Azure w programie Visual Studio, wybierz opcję **Zaloguj się.** Można również utworzyć bezpłatne konto platformy Azure.

4. W **usłudze Azure App Service: Utwórz nowe**, użyj wartości określonych w poniższej tabeli:

    | Ustawienie      | Wartość  | Opis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa** | Nazwa unikatowa w skali globalnej | Unikatowa nazwa identyfikująca nową aplikację funkcji. Zaakceptuj tę nazwę lub wprowadź nową nazwę. Prawidłowe znaki `a-z` `0-9`to: `-`, , i . |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja platformy Azure, która ma być używana. Zaakceptuj tę subskrypcję lub wybierz nową z listy rozwijanej. |
    | **[Grupa zasobów](../articles/azure-resource-manager/management/overview.md)** | Nazwa grupy zasobów |  Grupa zasobów, w której ma być utworzona aplikacja funkcji. Wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz pozycję **Nowy,** aby utworzyć nową grupę zasobów.|
    | **[Plan hostingowy](../articles/azure-functions/functions-scale.md)** | Nazwa planu hostingowego | Wybierz **pozycję Nowy,** aby skonfigurować plan bezserwerowy. Pamiętaj, aby wybrać **opcję Zużycie** w obszarze **Rozmiar**. Po opublikowaniu projektu do aplikacji funkcji, która działa w [planie zużycia](../articles/azure-functions/functions-scale.md#consumption-plan), płacisz tylko za wykonanie aplikacji funkcji. Inne plany hostingowe ponoszą wyższe koszty. Jeśli uruchomisz w planie innym niż **Zużycie,** musisz [zarządzać skalowaniem aplikacji funkcyjnej](../articles/azure-functions/functions-scale.md). Wybierz **lokalizację** w [regionie](https://azure.microsoft.com/regions/) w pobliżu lub inne usługi, do których mają dostęp twoje funkcje.  |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | Konto magazynu ogólnego przeznaczenia | Konto usługi Azure Storage jest wymagane przez środowisko wykonawcze functions. Wybierz **pozycję Nowy,** aby skonfigurować konto magazynu ogólnego przeznaczenia. Można również wybrać istniejące konto, które spełnia [wymagania konta magazynu](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![Okno dialogowe Tworzenie usługi App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

5. Wybierz **utwórz,** aby utworzyć aplikację funkcji i jej powiązanych zasobów na platformie Azure z tych ustawień i wdrożyć kod projektu funkcji. 

6. Wybierz pozycję Publikuj i po zakończeniu wdrażania zanotuj wartość **adresu URL witryny,** która jest adresem aplikacji funkcji na platformie Azure.

    ![Komunikat o powodzeniu publikowania](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
