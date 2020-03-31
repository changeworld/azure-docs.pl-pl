---
title: Przypisywanie dostępu do tożsamości zarządzanej do zasobu przy użyciu programu PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące przypisywania tożsamości zarządzanej do jednego zasobu, dostępu do innego zasobu przy użyciu programu PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2283ac076ef761fd098d75e7120e6557a959574
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547244"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Przypisywanie dostępu do tożsamości zarządzanej do zasobu przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure z tożsamością zarządzaną można przyznać tożsamości zarządzanej dostęp do innego zasobu, podobnie jak każdy podmiot zabezpieczeń. W tym przykładzie pokazano, jak udzielić maszyny wirtualnej platformy Azure dostępu do tożsamości zarządzanego konta magazynu platformy Azure przy użyciu programu PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md). **Pamiętaj, aby przejrzeć [różnicę między tożsamością zarządzaną przypisaną do systemu a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell,](/powershell/azure/install-az-ps) jeśli jeszcze tego nie zrobiłeś.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Przypisywanie dostępu do tożsamości zarządzanej do innego zasobu za pomocą funkcji RBAC

Po włączeniu tożsamości zarządzanej w zasobie platformy Azure, [takim jak maszyna wirtualna platformy Azure:](qs-configure-powershell-windows-vm.md)

1. Zaloguj się na `Connect-AzAccount` platformie Azure przy użyciu polecenia cmdlet. Użyj konta skojarzonego z subskrypcją platformy Azure, w ramach której skonfigurowano tożsamość zarządzaną:

   ```powershell
   Connect-AzAccount
   ```
2. W tym przykładzie dajemy maszyny Wirtualnej platformy Azure dostęp do konta magazynu. Najpierw używamy [Get-AzVM,](/powershell/module/az.compute/get-azvm) aby uzyskać jednostkę `myVM`usługi dla maszyny Wirtualnej o nazwie , która została utworzona, gdy włączono tożsamość zarządzaną. Następnie użyj [new-AzRoleAssignment,](/powershell/module/Az.Resources/New-AzRoleAssignment) aby dać **czytnikowi** maszyn `myStorageAcct`wirtualnych dostęp do konta magazynu o nazwie:

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Następne kroki

- [Omówienie tożsamości zarządzanej dla zasobów platformy Azure](overview.md)
- Aby włączyć tożsamość zarządzaną na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu programu PowerShell.](qs-configure-powershell-windows-vm.md)
