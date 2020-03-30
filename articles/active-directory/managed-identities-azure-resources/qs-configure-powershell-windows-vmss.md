---
title: Konfigurowanie tożsamości zarządzanych w zestawach skalowania maszyny wirtualnej przy użyciu programu PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przypisanych przez użytkownika w zestawie skalowania maszyny wirtualnej przy użyciu programu PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 755aee312fd0492fd57a82cb7a437b04ebf72987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547267"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure w zestawach skalowania maszyny wirtualnej przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule za pomocą programu PowerShell dowiesz się, jak wykonywać tożsamości zarządzane dla operacji zasobów platformy Azure na zestawie skalowania maszyny wirtualnej:
- Włączanie i wyłączanie tożsamości zarządzanej przypisanej przez system w zestawie skalowania maszyny wirtualnej
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika do zestawu skalowania maszyny wirtualnej

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md). **Pamiętaj, aby przejrzeć [różnicę między przypisaną przez system tożsamością przypisaną przez użytkownika a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby wykonać operacje zarządzania w tym artykule, twoje konto wymaga następujących przypisań kontroli dostępu opartych na rolach platformy Azure:

    > [!NOTE]
    > Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

    - [Współautor maszyny wirtualnej,](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) aby utworzyć zestaw skalowania maszyny wirtualnej i włączyć i usunąć przypisaną do systemu tożsamość zarządzaną i/lub przypisaną przez użytkownika z zestawu skalowania maszyny wirtualnej.
    - [Rola współautora tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) w celu utworzenia tożsamości zarządzanej przypisanej przez użytkownika.
    - [Rola zarządzanego operatora tożsamości](/azure/role-based-access-control/built-in-roles#managed-identity-operator) do przypisywania i usuwania tożsamości zarządzanej przypisanej przez użytkownika z i do zestawu skalowania maszyny wirtualnej.
- Zainstaluj [najnowszą wersję programu Azure PowerShell,](/powershell/azure/install-az-ps) jeśli jeszcze tego nie zrobiłeś. 

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana do systemu

W tej sekcji dowiesz się, jak włączyć i usunąć tożsamość zarządzaną przypisaną do systemu przy użyciu programu Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Włączanie tożsamości zarządzanej przypisanej do systemu podczas tworzenia zestawu skalowania maszyny wirtualnej platformy Azure

Aby utworzyć zestaw skalowania maszyny wirtualnej z włączoną tożsamością zarządzaną przypisaną przez system:

1. Zapoznaj się z *przykładem 1* w [new-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet reference article to create a virtual machine scale set with a system-assigned managed identity.  Dodaj parametr `-IdentityType SystemAssigned` do `New-AzVmssConfig` polecenia cmdlet:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```



## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Włączanie tożsamości zarządzanej przypisanej do systemu w istniejącym zestawie skalowania maszyny wirtualnej platformy Azure

Jeśli chcesz włączyć tożsamość zarządzaną przypisaną do systemu w istniejącym zestawie skalowania maszyny wirtualnej platformy Azure:

1. Zaloguj się na `Connect-AzAccount`platformie Azure przy użyciu programu . Użyj konta skojarzonego z subskrypcją platformy Azure zawierającą zestaw skalowania maszyny wirtualnej. Upewnij się również, że twoje konto należy do roli, która daje uprawnienia do zapisu w zestawie skalowania maszyny wirtualnej, takie jak "Współautor maszyny wirtualnej":

   ```powershell
   Connect-AzAccount
   ```

2. Najpierw pobierz właściwości zestawu skalowania maszyny [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) wirtualnej przy użyciu polecenia cmdlet. Następnie, aby włączyć tożsamość zarządzaną `-IdentityType` przypisaną do systemu, użyj przełącznika polecenia cmdlet [Update-AzVmss:](/powershell/module/az.compute/update-azvmss)

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```



### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z zestawu skalowania maszyny wirtualnej platformy Azure

Jeśli masz zestaw skalowania maszyny wirtualnej, który nie potrzebuje już tożsamości zarządzanej przypisanej do systemu, ale nadal potrzebuje tożsamości zarządzanych przypisanych przez użytkownika, użyj następującego polecenia cmdlet:

1. Zaloguj się na `Connect-AzAccount`platformie Azure przy użyciu programu . Użyj konta skojarzonego z subskrypcją platformy Azure zawierającą maszynę wirtualną. Upewnij się również, że twoje konto należy do roli, która daje uprawnienia do zapisu w zestawie skalowania maszyny wirtualnej, takie jak "Współautor maszyny wirtualnej":

2. Uruchom następujące polecenie cmdlet:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Jeśli masz zestaw skalowania maszyny wirtualnej, który nie wymaga już tożsamości zarządzanej przypisanej do systemu i nie ma tożsamości zarządzanych przypisanych przez użytkownika, użyj następujących poleceń:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przypisaną przez użytkownika z zestawu skalowania maszyny wirtualnej przy użyciu programu Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia zestawu skalowania maszyny wirtualnej platformy Azure

Tworzenie nowego zestawu skalowania maszyny wirtualnej z tożsamością zarządzaną przypisaną przez użytkownika nie jest obecnie obsługiwane przez program PowerShell. Zobacz następną sekcję dotyczącą dodawania tożsamości zarządzanej przypisanej przez użytkownika do istniejącego zestawu skalowania maszyny wirtualnej. Wracaj tutaj, aby zapoznać się z aktualizacjami.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącego zestawu skalowania maszyny wirtualnej platformy Azure

Aby przypisać tożsamość zarządzaną przypisaną przez użytkownika do istniejącego zestawu skalowania maszyny wirtualnej platformy Azure:

1. Zaloguj się na `Connect-AzAccount`platformie Azure przy użyciu programu . Użyj konta skojarzonego z subskrypcją platformy Azure zawierającą zestaw skalowania maszyny wirtualnej. Upewnij się również, że twoje konto należy do roli, która daje uprawnienia do zapisu w zestawie skalowania maszyny wirtualnej, takie jak "Współautor maszyny wirtualnej":

   ```powershell
   Connect-AzAccount
   ```

2. Najpierw pobierz właściwości zestawu skalowania maszyny `Get-AzVM` wirtualnej przy użyciu polecenia cmdlet. Następnie, aby przypisać tożsamość zarządzaną przypisaną przez użytkownika `-IdentityType` `-IdentityID` do zestawu skalowania maszyny wirtualnej, użyj polecenia cmdlet [Update-AzVmss.](/powershell/module/az.compute/update-azvmss) `<VM NAME>`Zamień `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, własnymi wartościami.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z zestawu skalowania maszyny wirtualnej platformy Azure

Jeśli zestaw skalowania maszyny wirtualnej ma wiele tożsamości zarządzanych przypisanych przez użytkownika, można usunąć wszystkie oprócz ostatniego przy użyciu następujących poleceń. Upewnij się, że parametry `<RESOURCE GROUP>` i `<VIRTUAL MACHINE SCALE SET NAME>` zostały zastąpione własnymi wartościami. Jest `<USER ASSIGNED IDENTITY NAME>` właściwością nazwy tożsamości zarządzanej przypisanej przez użytkownika, która powinna pozostać w zestawie skalowania maszyny wirtualnej. Te informacje można znaleźć w sekcji tożsamości zestawu `az vmss show`skalowania maszyny wirtualnej przy użyciu:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Jeśli zestaw skalowania maszyny wirtualnej nie ma tożsamości zarządzanej przypisanej do systemu i chcesz usunąć z niej wszystkie tożsamości zarządzane przypisane przez użytkownika, użyj następującego polecenia:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Jeśli zestaw skalowania maszyny wirtualnej ma zarówno tożsamości zarządzane przypisane przez system, jak i przypisane przez użytkownika, można usunąć wszystkie tożsamości zarządzane przypisane przez użytkownika, przełączając się na używanie tylko tożsamości zarządzanej przypisanej przez system.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie tożsamości zarządzanych dla zasobów platformy Azure](overview.md)
- Aby uzyskać pełną platformę Szybkiego startu tworzenia maszyn wirtualnych platformy Azure, zobacz:
  
  - [Tworzenie maszyny wirtualnej z systemem Windows za pomocą programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Tworzenie maszyny wirtualnej z systemem Linux za pomocą programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















