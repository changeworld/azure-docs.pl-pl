---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 08/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f0bcb2e7f99689d31be610f1523d2c57e332deb7
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049745"
---
1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

2. Wybierz kolejno pozycje **Aplikacja funkcji platformy Azure**, **Utwórz nową** i **Publikuj**.

    ![Wybieranie miejsca docelowego publikacji](./media/functions-vstools-publish/functions-vstools-create-new-function-app.png)

    W przypadku włączenia opcji **Uruchom z archiwum ZIP** Twoja aplikacja funkcji na platformie Azure będzie uruchamiana bezpośrednio z pakietu wdrożeniowego. Aby uzyskać więcej informacji, zobacz [Uruchamianie funkcji Azure Functions z pliku pakietu](../articles/azure-functions/run-functions-from-deployment-package.md).

3. Jeśli program Visual Studio nie został jeszcze połączony z kontem platformy Azure, wybierz pozycję **Dodaj konto...**.

4. W oknie dialogowym **Tworzenie usługi App Service** zastosuj w obszarze **Hosting** ustawienia określone w tabeli pod obrazem:

    ![Okno dialogowe Tworzenie usługi App Service](./media/functions-vstools-publish/functions-vstools-publish.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa aplikacji** | Nazwa unikatowa w skali globalnej | Unikatowa nazwa identyfikująca nową aplikację funkcji. |
    | **Subskrypcja** | Wybierz subskrypcję | Subskrypcja platformy Azure, która ma być używana. |
    | **[Grupa zasobów](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nazwa grupy zasobów, w której ma zostać utworzona aplikacja funkcji. Wybierz pozycję **Nowa**, aby utworzyć nową grupę zasobów.|
    | **[Plan usługi App Service](../articles/azure-functions/functions-scale.md)** | Plan Zużycie | Pamiętaj, aby po kliknięciu pozycji **Nowy** w celu utworzenia planu bezserwerowego wybrać pozycję **Zużycie** w obszarze **Rozmiar**. Ponadto w polu **Lokalizacja** wybierz [region](https://azure.microsoft.com/regions/) w swojej okolicy lub w pobliżu innych usług, do których Twoje funkcje uzyskują dostęp. W razie działania w planie innym niż **Zużycie** konieczne jest zarządzanie [skalowaniem aplikacji funkcji](../articles/azure-functions/functions-scale.md).  |
    | **[Konto magazynu](../articles/storage/common/storage-quickstart-create-account.md)** | Konto magazynu ogólnego przeznaczenia | Środowisko uruchomieniowe usługi Functions wymaga konta magazynu platformy Azure. Kliknij przycisk **Nowe**, aby utworzyć konto magazynu ogólnego przeznaczenia. Możesz również użyć istniejącego konta, które spełnia [wymagania dotyczące konta magazynu](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. Kliknij przycisk **Utwórz**, aby utworzyć aplikację funkcji i powiązane zasoby na platformie Azure przy użyciu tych ustawień i wdrożyć kod projektu funkcji. 

6. Po ukończeniu wdrażania zanotuj wartość **Adres URL witryny**, która jest adresem aplikacji funkcji na platformie Azure.

    ![Komunikat o powodzeniu publikowania](./media/functions-vstools-publish/functions-vstools-publish-profile.png)
