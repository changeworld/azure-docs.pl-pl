---
title: Przywracanie usuniętych aplikacji
description: Dowiedz się, jak przywrócić usuniętą aplikację w usłudze Azure App Service. Unikaj bólu głowy przypadkowo usuniętej aplikacji.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: c7d778a0afca4b3552976526d58a2cb2efe12161
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75689619"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Przywracanie usuniętej aplikacji usługi App Service przy użyciu programu PowerShell

Jeśli zdarzyło ci się przypadkowo usunąć aplikację w usłudze Azure App Service, możesz ją przywrócić za pomocą poleceń z [modułu Az PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> Usunięte aplikacje są usuwane z systemu 30 dni po początkowym usunięciu. Po usunięciu aplikacji nie można jej odzyskać.
>

## <a name="re-register-app-service-resource-provider"></a>Ponowne zarejestrowanie dostawcy zasobów usługi App Service
Niektórzy klienci mogą natknąć się na problem polegający na tym, że pobieranie listy usuniętych aplikacji kończy się niepowodzeniem. Aby rozwiązać ten problem, uruchom następujące polecenie:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Lista usuniętych aplikacji

Aby uzyskać kolekcję usuniętych aplikacji, możesz użyć programu `Get-AzDeletedWebApp`.

Szczegółowe informacje na temat konkretnej usuniętej aplikacji można użyć:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

Szczegółowe informacje obejmują:

- **DeletedSiteId**: Unikatowy identyfikator aplikacji, używany w scenariuszach, w których wiele aplikacji o tej samej nazwie zostało usuniętych
- **Identyfikator subskrypcji**: Subskrypcja zawierająca usunięty zasób
- **Lokalizacja**: Lokalizacja oryginalnej aplikacji
- **ResourceGroupName**: Nazwa oryginalnej grupy zasobów
- **Nazwa**: Nazwa oryginalnej aplikacji.
- **Gniazdo:** nazwa gniazda.
- **Czas usuwania**: Kiedy aplikacja została usunięta  

## <a name="restore-deleted-app"></a>Przywracanie usuniętej aplikacji

Po zidentyfikowaniu aplikacji, którą chcesz przywrócić, można `Restore-AzDeletedWebApp`ją przywrócić za pomocą programu .

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

Dane wejściowe dla polecenia są następujące:

- **Grupa zasobów:** Grupa zasobów docelowych, w której aplikacja zostanie przywrócona
- **Nazwa**: Nazwa aplikacji powinna być unikatowa globalnie.
- **TargetAppServicePlanName**: Plan usługi aplikacji połączony z aplikacją

Domyślnie `Restore-AzDeletedWebApp` spowoduje przywrócenie zarówno konfiguracji aplikacji, jak i zawartości. Jeśli chcesz tylko przywrócić zawartość, `-RestoreContentOnly` użyj flagi z tym poleceniem.

> [!NOTE]
> Jeśli aplikacja została hostowana, a następnie usunięta ze środowiska usługi aplikacji, można ją przywrócić tylko wtedy, gdy nadal istnieje odpowiednie środowisko usługi app service.
>

Pełne polecenie można znaleźć tutaj: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
