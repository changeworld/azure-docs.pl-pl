---
title: Jak skonfigurować MSI na platformie Azure zestawu skalowania maszyn wirtualnych przy użyciu programu PowerShell
description: Krok po kroku instrukcje dotyczące konfigurowania systemu i użytkownika tożsamości przypisanych przez na platformie Azure zestawu skalowania maszyn wirtualnych, przy użyciu programu PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: b82785d0f4b6a5952334e891e7adec570c624f2d
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238135"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>Konfigurowanie zestawu skalowania maszyn wirtualnych tożsamość usługi zarządzanej (MSI) przy użyciu programu PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wykonywać operacje tożsamości usługi zarządzanej w zestawie skalowania maszyn wirtualnych platformy Azure przy użyciu programu PowerShell:
- Włączanie i wyłączanie tożsamości przypisanej w systemie w zestawie skalowania maszyn wirtualnych
- Dodawanie i usuwanie tożsamości przypisanych przez użytkownika w zestawie skalowania maszyn wirtualnych

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujących przypisań ról:
    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) do tworzenia zestawu skalowania maszyn wirtualnych i włączyć i usuwania przypisanej w systemie zarządzanych i/lub ustawić tożsamość użytkownika z skalowania maszyn wirtualnych.
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roli do utworzenia tożsamości przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli przypisywania i usuwania tożsamości przypisanych przez użytkownika z i do zestawu skalowania maszyn wirtualnych.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) Jeśli jeszcze go. 

## <a name="system-assigned-managed-identity"></a>Zarządzanych tożsamości przypisanej przez system

W tej sekcji dowiesz się, jak włączyć i usuwanie tożsamości przypisanej w systemie, przy użyciu programu Azure PowerShell.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Włączanie tożsamości przypisanej w systemie podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Do utworzenia zestawu skalowania maszyn wirtualnych przy użyciu tożsamości przypisanej w systemie włączony:

1. Zapoznaj się *przykład 1* w [polecenia New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) artykuł dokumentacji polecenia cmdlet do tworzenia zestawu skalowania maszyn wirtualnych przy użyciu tożsamości przypisanej w systemie.  Dodaj parametr `-IdentityType SystemAssigned` do `New-AzureRmVmssConfig` polecenia cmdlet:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Opcjonalnie) Dodaj przy użyciu rozszerzenia MSI zestawu skalowania maszyn wirtualnych `-Name` i `-Type` parametru [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) polecenia cmdlet. Można przekazać albo "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu skalowania maszyn wirtualnych zestawu i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu:

    > [!NOTE]
    > Ten krok jest opcjonalny, zgodnie z punktu końcowego tożsamości Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Włączanie tożsamości przypisanej w systemie, na podstawie istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli potrzebujesz umożliwić tożsamości przypisanej w systemie, na podstawie istniejącego zestawu skalowania maszyn wirtualnych platformy Azure:

1. Zaloguj się do platformy Azure za pomocą `Login-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, takich jak "Współautor maszyny wirtualnej":

   ```powershell
   Login-AzureRmAccount
   ```

2. Pierwszy pobierania właściwości za pomocą zestawu skalowania maszyn wirtualnych [ `Get-AzureRmVmss` ](/powershell/module/azurerm.compute/get-azurermvmss) polecenia cmdlet. Następnie można włączyć tożsamości przypisanej w systemie, należy użyć `-IdentityType` Włącz [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) polecenia cmdlet:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

3. Dodaj przy użyciu rozszerzenia MSI zestawu skalowania maszyn wirtualnych `-Name` i `-Type` parametru [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) polecenia cmdlet. Można przekazać albo "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu skalowania maszyn wirtualnych zestawu i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłączanie tożsamości przypisanej w systemie z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz zestaw skalowania maszyn wirtualnych, które nie wymaga systemu tożsamości przypisanej, ale nadal wymaga tożsamości przypisanych przez użytkownika, należy użyć następującego polecenia cmdlet:

1. Zaloguj się do platformy Azure za pomocą `Login-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, takich jak "Współautor maszyny wirtualnej":

2. Uruchom następujące polecenie cmdlet:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie wymaga tożsamości przypisanej w systemie i ma żaden użytkownik nie tożsamości przypisanych przez, użyj następujących poleceń:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-identity"></a>Tożsamości przypisanych przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć użytkownika z tożsamości przypisanej z maszyny wirtualnej zestawu skalowania przy użyciu programu Azure PowerShell.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Przypisz tożsamości przypisanych przez użytkownika podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Tworzenie nowej maszyny wirtualnej zestawu skalowania przy użyciu tożsamości przypisanych przez użytkownika nie jest obecnie obsługiwane za pośrednictwem programu PowerShell. Zobacz następną sekcję na temat dodawania tożsamości przypisanych przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych. Wracaj tutaj, aby zapoznać się z aktualizacjami.

### <a name="assign-a-user-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Przypisz tożsamość użytkownika do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

Aby przypisać tożsamości przypisanych przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure:

1. Zaloguj się do platformy Azure za pomocą `Connect-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, takich jak "Współautor maszyny wirtualnej":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Pierwszy pobierania właściwości za pomocą zestawu skalowania maszyn wirtualnych `Get-AzureRmVM` polecenia cmdlet. Następnie można przypisać tożsamości przypisanych przez użytkownika do zestawu skalowania maszyn wirtualnych, należy użyć `-IdentityType` i `-IdentityID` Włącz [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) polecenia cmdlet. Zastąp `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` własnymi wartościami.

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Usuń użytkownika z zarządzanych tożsamości przypisanej z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli zestaw skalowania maszyn wirtualnych ma wiele tożsamości przypisanych przez użytkownika, możesz usunąć wszystkie oprócz ostatni z nich przy użyciu następujących poleceń. Upewnij się, że parametry `<RESOURCE GROUP>` i `<VMSS NAME>` zostały zastąpione własnymi wartościami. `<MSI NAME>` Jest tożsamości przypisanych przez użytkownika nazwa właściwości, która powinna pozostać w zestawie skalowania maszyn wirtualnych. Te informacje można znaleźć w sekcji tożsamości maszyny wirtualnej zestawu skalowania przy użyciu `az vmss show`:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<MSI NAME>"
```
Jeśli chcesz usunąć wszystkich użytkowników tożsamości przypisanych przez z niego zestawu skalowania maszyny wirtualnej bez zainstalowanego systemu tożsamości przypisanej, użyj następującego polecenia:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Jeśli zestaw skalowania maszyn wirtualnych ma przypisanej w systemie i tożsamości przypisanych przez użytkownika, możesz usunąć wszystkich użytkowników tożsamości przypisanych przez przełączenie na system tylko przypisane.

```powershell 
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Powiązana zawartość

- [Omówienie usługi zarządzanej tożsamości usługi](overview.md)
- Pełne tworzenie maszyny Wirtualnej platformy Azure przewodników Szybki Start zobacz:
  
  - [Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















