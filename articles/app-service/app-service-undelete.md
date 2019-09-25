---
title: Przywracanie usuniętych App Service aplikacji — Azure App Service
description: Dowiedz się, jak przywrócić usuniętą aplikację App Service przy użyciu programu PowerShell.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: a766f7ed7a82874df8ef7506226de0d6f38a5847
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219538"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Przywracanie usuniętej App Service aplikacji przy użyciu programu PowerShell

Jeśli przypadkowo usuniesz aplikację w Azure App Service, możesz ją przywrócić za pomocą poleceń z [modułu AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="list-deleted-apps"></a>Wyświetlanie listy usuniętych aplikacji

Aby pobrać kolekcję usuniętych aplikacji, można użyć `Get-AzDeletedWebApp`programu.

Aby uzyskać szczegółowe informacje dotyczące konkretnej usuniętej aplikacji, można użyć:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

Szczegółowe informacje obejmują:

- **DeletedSiteId**: Unikatowy identyfikator aplikacji używany w scenariuszach, w których usunięto wiele aplikacji o tej samej nazwie
- Identyfikator **subskrypcji**: Subskrypcja zawierająca usunięty zasób
- **Lokalizacja**: Lokalizacja oryginalnej aplikacji
- **ResourceGroupName**: Nazwa oryginalnej grupy zasobów
- **Nazwa**: Nazwa oryginalnej aplikacji.
- **Gniazdo**: Nazwa gniazda.
- **Godzina usunięcia**: Kiedy aplikacja została usunięta  

## <a name="restore-deleted-app"></a>Przywróć usuniętą aplikację

Gdy aplikacja, którą chcesz przywrócić, została zidentyfikowana, możesz ją przywrócić za pomocą `Restore-AzDeletedWebApp`polecenia.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

Dane wejściowe polecenia są następujące:

- **Grupa zasobów**: Docelowa Grupa zasobów, do której zostanie przywrócona aplikacja
- **Nazwa**: Nazwa aplikacji powinna być globalnie unikatowa.
- **TargetAppServicePlanName**: Plan App Service połączony z aplikacją

Domyślnie `Restore-AzDeletedWebApp` program przywraca zarówno konfigurację aplikacji, jak i zawartość. Jeśli chcesz tylko przywrócić zawartość, użyj `-RestoreContentOnly` flagi z tym polecenia cmdlet.
