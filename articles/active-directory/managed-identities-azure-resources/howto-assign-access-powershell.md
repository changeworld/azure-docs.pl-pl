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
ms.openlocfilehash: 82fa5019e740d16d0b97111fcf8dbc4f6c91d57b
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183998"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Przypisywanie dostępu tożsamości zarządzanej do zasobów przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure za pomocą tożsamości zarządzanej, można zapewnić dostęp tożsamość zarządzaną, do innego zasobu, podobnie jak podmiot zabezpieczeń. W tym przykładzie pokazano, jak zapewnić maszynie wirtualnej platformy Azure w tożsamości zarządzanej dostęp do konta usługi Azure storage przy użyciu programu PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-it-work)** .
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](/powershell/azure/install-az-ps) Jeśli jeszcze go.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Przypisywanie dostępu tożsamości zarządzanej do innego zasobu przy użyciu kontroli RBAC

Po włączeniu tożsamość zarządzaną w obrębie zasobu platformy Azure, [takich jak Maszynie wirtualnej platformy Azure](qs-configure-powershell-windows-vm.md):

1. Zaloguj się do platformy Azure za pomocą `Connect-AzAccount` polecenia cmdlet. Użyj konta które jest skojarzone z subskrypcją platformy Azure, w którym skonfigurowano tożsamość zarządzana:

   ```powershell
   Connect-AzAccount
   ```
2. W tym przykładzie udostępniamy możliwość dostępu do maszyny Wirtualnej platformy Azure na koncie magazynu. Najpierw użyjemy polecenie [Get-AzVM](/powershell/module/az.compute/get-azvm) , aby uzyskać nazwę główną usługi dla maszyny wirtualnej o nazwie `myVM`, która została utworzona podczas włączania zarządzanej tożsamości. Następnie użyj polecenie [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) , aby uzyskać dostęp do **czytnika** maszyn wirtualnych do konta magazynu o nazwie `myStorageAcct`:

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Kolejne kroki

- [Tożsamość zarządzaną dla zasobów platformy Azure — omówienie](overview.md)
- Aby włączyć tożsamość zarządzana na Maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu programu PowerShell](qs-configure-powershell-windows-vm.md).
