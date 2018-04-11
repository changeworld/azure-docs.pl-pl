---
title: "Raportowanie dostępu — Azure RBAC | Dokumentacja firmy Microsoft"
description: "Generowanie raportu, który zawiera listę wszystkich zmian w dostęp do Twojej subskrypcji platformy Azure z opartej na rolach kontroli dostępu w ciągu ostatnich 90 dni."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 169ed8dd6d14d8d9d0fd49ad7306b1d4fb2c4d90
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-access-report-for-role-based-access-control"></a>Tworzenie raportu programu access do kontroli dostępu opartej na rolach
Ilekroć ktoś lub go przydziela odwołuje dostęp w ramach subskrypcji, zmiany mają być rejestrowane w zdarzeń platformy Azure. Można tworzyć raporty dotyczące historii zmian dostępu aby zobaczyć wszystkie zmiany w ciągu ostatnich 90 dni.

## <a name="create-a-report-with-azure-powershell"></a>Tworzenie raportu przy użyciu programu Azure PowerShell
Aby utworzyć raportu historii zmian dostępu w programie PowerShell, użyj [Get-AzureRMAuthorizationChangeLog](/powershell/module/azurerm.resources/get-azurermauthorizationchangelog) polecenia.

Po wywołaniu tego polecenia, można określić które właściwości przypisania mają wymienionych w tym następujące:

| Właściwość | Opis |
| --- | --- |
| **Akcja** |Określa, czy przyznany lub odwołany dostęp |
| **Caller** |Właściciel odpowiedzialny za zmianę dostępu |
| **PrincipalId** | Unikatowy identyfikator użytkownika, grupy lub aplikacji, który został przypisany do roli |
| **PrincipalName** |Nazwa użytkownika, grupy lub aplikacji |
| **PrincipalType** |Czy przydział był przeznaczony dla użytkownika, grupy lub aplikacji |
| **Wartość RoleDefinitionId** |Identyfikator GUID roli, który został przyznany lub odwołany |
| **RoleName** |Rola, w którym został przyznany lub odwołany |
| **Zakres** | Unikatowy identyfikator subskrypcji, grupy zasobów lub zasobu, który dotyczy przypisania | 
| **ScopeName** |Nazwa subskrypcji, grupy zasobów lub zasobów |
| **ScopeType** |Określa, czy przydział został w subskrypcji, grupy zasobów lub zasobów zakresu |
| **Znacznik czasu** |Data i godzina dostęp został zmieniony |

To polecenie wyświetla listę wszystkich zmian dostępu w ramach subskrypcji w ciągu ostatnich siedmiu dni:

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog — zrzut ekranu](./media/role-based-access-control-configure/access-change-history.png)

## <a name="create-a-report-with-azure-cli"></a>Tworzenie raportu z wiersza polecenia platformy Azure
Aby utworzyć raportu historii zmian dostępu w Azure interfejsu wiersza polecenia (CLI), użyj `azure role assignment changelog list` polecenia.

## <a name="export-to-a-spreadsheet"></a>Eksportuj do arkusza kalkulacyjnego
Aby zapisać raport lub manipulowania danymi, wyeksportuj zmian dostępu do pliku CSV. Następnie można wyświetlić raportu, w arkuszu kalkulacyjnym do przeglądu.

![Wykaz zmian wyświetlane jako arkusz kalkulacyjny — zrzut ekranu](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## <a name="next-steps"></a>Kolejne kroki
* Praca z [niestandardowych ról w Azure RBAC](role-based-access-control-custom-roles.md)
* Dowiedz się, jak zarządzać [Azure RBAC przy użyciu programu powershell](role-based-access-control-manage-access-powershell.md)

