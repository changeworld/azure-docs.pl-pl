---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/02/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f1a8ecd0e53d28aed66546c41f95cc3855d1783a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608302"
---
1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

2. Wybierz kolejno pozycje **Aplikacja funkcji platformy Azure**, **Utwórz nową** i **Publikuj**.

    ![Wybieranie miejsca docelowego publikacji](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png) 

    Po kliknięciu **uruchamiania z pliku pakietu (zalecane)** , aplikacja funkcji zostanie wdrożony przy użyciu [Zip wdrażanie](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) z [uruchomienia z pakietu](../articles/azure-functions/run-functions-from-deployment-package.md) włączony tryb. Jest to zalecany sposób uruchamiania funkcji i spowoduje lepszą wydajność.

    >[!CAUTION]
    >Po wybraniu pozycji **Wybierz istniejącą** wszystkie pliki w istniejącej aplikacji funkcji na platformie Azure zostaną zastąpione przez pliki z projektu lokalnego. Tej opcji należy używać tylko w przypadku ponownego publikowania aktualizacji do istniejącej aplikacji funkcji.

3. Jeśli program Visual Studio nie został jeszcze połączony z kontem platformy Azure, wybierz pozycję **Dodaj konto...** .

4. W oknie dialogowym **Tworzenie usługi App Service** zastosuj w obszarze **Hosting** ustawienia określone w tabeli pod obrazem:

    ![Okno dialogowe Tworzenie usługi App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa aplikacji** | Nazwa unikatowa w skali globalnej | Unikatowa nazwa identyfikująca nową aplikację funkcji. |
    | **Subskrypcja** | Wybierz subskrypcję | Subskrypcja platformy Azure, która ma być używana. |
    | **[Grupa zasobów](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nazwa grupy zasobów, w której ma zostać utworzona aplikacja funkcji. Wybierz pozycję **Nowa**, aby utworzyć nową grupę zasobów.|
    | **[Plan usługi App Service](../articles/azure-functions/functions-scale.md)** | Plan Zużycie | Pamiętaj, aby po kliknięciu pozycji **Nowy** w celu utworzenia planu bezserwerowego wybrać pozycję **Zużycie** w obszarze **Rozmiar**. Ponadto w polu **Lokalizacja** wybierz [region](https://azure.microsoft.com/regions/) w swojej okolicy lub w pobliżu innych usług, do których Twoje funkcje uzyskują dostęp. W razie działania w planie innym niż **Zużycie** konieczne jest zarządzanie [skalowaniem aplikacji funkcji](../articles/azure-functions/functions-scale.md).  |
    | **[Konto magazynu](../articles/storage/common/storage-quickstart-create-account.md)** | Konto magazynu ogólnego przeznaczenia | Środowisko uruchomieniowe usługi Functions wymaga konta magazynu platformy Azure. Kliknij przycisk **New** do utworzenia konta magazynu ogólnego przeznaczenia. Możesz również użyć istniejącego konta, które spełnia [wymagania dotyczące konta magazynu](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. Kliknij przycisk **Utwórz**, aby utworzyć aplikację funkcji i powiązane zasoby na platformie Azure przy użyciu tych ustawień i wdrożyć kod projektu funkcji. 

6. Po ukończeniu wdrażania zanotuj wartość **Adres URL witryny**, która jest adresem aplikacji funkcji na platformie Azure.

    ![Komunikat o powodzeniu publikowania](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
