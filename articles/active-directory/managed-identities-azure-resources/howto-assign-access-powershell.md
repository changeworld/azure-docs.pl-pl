---
title: Przypisywanie zarządzanej tożsamości dostępu do zasobu przy użyciu programu PowerShell — Azure AD
description: Krok po kroku instrukcje dotyczące przypisywania tożsamość zarządzaną w jeden zasób, uzyskać dostęp do innego zasobu przy użyciu programu PowerShell.
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
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547244"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Przypisywanie dostępu tożsamości zarządzanej do zasobów przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure za pomocą tożsamości zarządzanej, można zapewnić dostęp tożsamość zarządzaną, do innego zasobu, podobnie jak podmiot zabezpieczeń. W tym przykładzie pokazano, jak zapewnić maszynie wirtualnej platformy Azure w tożsamości zarządzanej dostęp do konta usługi Azure storage przy użyciu programu PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](/powershell/azure/install-az-ps) , jeśli jeszcze tego nie zrobiono.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Przypisywanie dostępu tożsamości zarządzanej do innego zasobu przy użyciu kontroli RBAC

Po włączeniu tożsamości zarządzanej w zasobie platformy Azure, na przykład na [maszynie wirtualnej platformy Azure](qs-configure-powershell-windows-vm.md):

1. Zaloguj się do platformy Azure za pomocą polecenia cmdlet `Connect-AzAccount`. Użyj konta które jest skojarzone z subskrypcją platformy Azure, w którym skonfigurowano tożsamość zarządzana:

   ```powershell
   Connect-AzAccount
   ```
2. W tym przykładzie udostępniamy możliwość dostępu do maszyny Wirtualnej platformy Azure na koncie magazynu. Najpierw użyjemy polecenie [Get-AzVM](/powershell/module/az.compute/get-azvm) , aby uzyskać nazwę główną usługi dla maszyny wirtualnej o nazwie `myVM`, która została utworzona podczas włączania zarządzanej tożsamości. Następnie użyj polecenie [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) , aby uzyskać dostęp do **czytnika** maszyn wirtualnych do konta magazynu o nazwie `myStorageAcct`:

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Następne kroki

- [Tożsamość zarządzana dla zasobów platformy Azure — omówienie](overview.md)
- Aby włączyć zarządzaną tożsamość na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell](qs-configure-powershell-windows-vm.md).
