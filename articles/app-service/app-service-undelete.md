---
title: Przywróć usunięte aplikacje
description: Dowiedz się, jak przywrócić usuniętą aplikację w Azure App Service. Unikaj kłopotliwej przypadkowo usuniętej aplikacji.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: c7d778a0afca4b3552976526d58a2cb2efe12161
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689619"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Przywracanie usuniętej App Service aplikacji przy użyciu programu PowerShell

Jeśli przypadkowo usuniesz aplikację w Azure App Service, możesz ją przywrócić za pomocą poleceń z [modułu AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> Usunięte aplikacje są przeczyszczane z systemu 30 dni po początkowym usunięciu. Gdy aplikacja zostanie przeczyszczona, nie można jej odzyskać.
>

## <a name="re-register-app-service-resource-provider"></a>Zarejestruj ponownie dostawcę zasobów App Service
Niektórzy klienci mogą napotkać problem polegający na tym, że pobieranie listy usuniętych aplikacji zakończy się niepowodzeniem. Aby rozwiązać ten problem, uruchom następujące polecenie:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Wyświetlanie listy usuniętych aplikacji

Aby uzyskać kolekcję usuniętych aplikacji, można użyć `Get-AzDeletedWebApp`.

Aby uzyskać szczegółowe informacje dotyczące konkretnej usuniętej aplikacji, można użyć:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

Szczegółowe informacje obejmują:

- **DeletedSiteId**: unikatowy identyfikator aplikacji używany w scenariuszach, w których usunięto wiele aplikacji o tej samej nazwie
- **Identyfikator subskrypcji**: subskrypcja zawierająca usunięty zasób
- **Lokalizacja**: Lokalizacja oryginalnej aplikacji
- **ResourceGroupName**: nazwa oryginalnej grupy zasobów
- **Nazwa**: nazwa oryginalnej aplikacji.
- **Gniazdo**: Nazwa gniazda.
- **Godzina usunięcia**: Kiedy aplikacja została usunięta  

## <a name="restore-deleted-app"></a>Przywróć usuniętą aplikację

Gdy aplikacja, którą chcesz przywrócić, została zidentyfikowana, możesz ją przywrócić przy użyciu `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

Dane wejściowe polecenia są następujące:

- **Grupa zasobów**: docelowa Grupa zasobów, w której zostanie przywrócona aplikacja
- **Nazwa**: Nazwa aplikacji powinna być globalnie unikatowa.
- **TargetAppServicePlanName**: plan App Service połączony z aplikacją

Domyślnie `Restore-AzDeletedWebApp` przywraca zarówno konfigurację aplikacji, jak i zawartość. Jeśli chcesz tylko przywrócić zawartość, Użyj flagi `-RestoreContentOnly` z tym polecenia cmdlet.

> [!NOTE]
> Jeśli aplikacja była hostowana, a następnie usunięta z App Service Environment, może zostać przywrócona tylko wtedy, gdy istnieją odpowiednie App Service Environment nadal istnieją.
>

Pełne odwołanie polecenia cmdlet można znaleźć tutaj: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
