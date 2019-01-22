---
title: Jak skonfigurować zarządzanych tożsamości dla zasobów platformy Azure na zestaw skalowania maszyn wirtualnych można ustawić przy użyciu programu PowerShell
description: Krok po kroku instrukcje dotyczące konfigurowania systemu i zarządzanych tożsamości przypisanych przez użytkownika na zestaw skalowania maszyn wirtualnych zestawu przy użyciu programu PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 516216e435722e27e7c3c9330db4991e280c4014
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429066"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na zestawach skalowania maszyn wirtualnych przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule przy użyciu programu PowerShell, nauczysz się wykonywać zarządzanych tożsamości dla operacji tworzenia zasobów platformy Azure w zestawie skalowania maszyn wirtualnych:
- Włączanie i wyłączanie przypisany systemowo tożsamość zarządzaną w zestawie skalowania maszyn wirtualnych
- Dodawanie i usuwanie tożsamości zarządzanej użytkownik przypisany na zestaw skalowania maszyn wirtualnych

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisany systemu i zarządzane przez użytkownika tożsamości przypisanej](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujące przypisania kontroli dostępu opartej na rolach platformy Azure:

    > [!NOTE]
    > Nie dodatkowych Azure przypisań ról katalogu usługi AD wymagane.

    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) utworzyć skalę maszyny wirtualnej zestawu i włączanie i usuwanie przypisany systemowo zarządzanych i/lub tożsamości przypisanych przez użytkownika zarządzanej z zestawu skalowania maszyn wirtualnych.
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) tożsamości zarządzanej roli do utworzenia przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli przypisywania i usuwania, użytkownik przypisany tożsamości zarządzanej, od i do zestawu skalowania maszyn wirtualnych.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) Jeśli jeszcze go. 

## <a name="system-assigned-managed-identity"></a>Przypisana przez system tożsamości zarządzanej

W tej sekcji dowiesz się, jak włączyć i Usuń przypisany systemowo tożsamości zarządzanej przy użyciu programu Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Włącz przypisany systemowo tożsamości zarządzanej podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Do utworzenia zestawu skalowania maszyn wirtualnych z przypisany systemowo tożsamość zarządzaną włączone:

1. Zapoznaj się *przykład 1* w [polecenia New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) artykuł dokumentacji polecenia cmdlet do tworzenia zestawu skalowania maszyn wirtualnych za pomocą tożsamości zarządzanej przypisana przez system.  Dodaj parametr `-IdentityType SystemAssigned` do `New-AzureRmVmssConfig` polecenia cmdlet:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Opcjonalnie) Dodawanie zarządzanych tożsamości dla zestawu skalowania maszyny wirtualnej zasoby platformy Azure przy użyciu rozszerzenia `-Name` i `-Type` parametru [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) polecenia cmdlet. Można przekazać albo "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu skalowania maszyn wirtualnych zestawu i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu:

    > [!NOTE]
    > Ten krok jest opcjonalny, zgodnie z punktu końcowego tożsamości Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Włącz przypisany systemowo tożsamość zarządzana na podstawie istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli musisz włączyć przypisany systemowo tożsamości zarządzanej na podstawie istniejącego zestawu skalowania maszyn wirtualnych platformy Azure:

1. Zaloguj się do platformy Azure za pomocą `Login-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, takich jak "Współautor maszyny wirtualnej":

   ```powershell
   Login-AzureRmAccount
   ```

2. Pierwszy pobierania właściwości za pomocą zestawu skalowania maszyn wirtualnych [ `Get-AzureRmVmss` ](/powershell/module/azurerm.compute/get-azurermvmss) polecenia cmdlet. Następnie można włączyć tożsamości zarządzanych z przypisaną przez system, należy użyć `-IdentityType` Włącz [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) polecenia cmdlet:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

3. Dodawanie zarządzanych tożsamości dla rozszerzenie zestawu skalowania maszyn wirtualnych zasobów platformy Azure przy użyciu polecenia `-Name` i `-Type` parametru [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) polecenia cmdlet. Można przekazać albo "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu skalowania maszyn wirtualnych zestawu i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłącz przypisany systemowo tożsamość zarządzaną z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz zestaw skalowania maszyn wirtualnych, które nie wymaga tożsamości zarządzanej przypisana przez system, ale nadal wymaga tożsamości zarządzanej przypisanych przez użytkownika, należy użyć następującego polecenia cmdlet:

1. Zaloguj się do platformy Azure za pomocą `Login-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, takich jak "Współautor maszyny wirtualnej":

2. Uruchom następujące polecenie cmdlet:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie wymaga tożsamości zarządzanej przypisana przez system, i przedstawiono w nim nie przypisanych do użytkowników zarządzanych tożsamości, użyj następujących poleceń:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Przypisane przez użytkownika z tożsamości zarządzanej

W tej sekcji dowiesz się, jak dodawać i usuwać przypisane przez użytkownika tożsamości zarządzanej maszyny wirtualnej zestawu skalowania przy użyciu programu Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Przypisz przypisanych przez użytkownika tożsamości zarządzanej podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Tworzenie nowej maszyny wirtualnej zestawu skalowania z tożsamości zarządzanej przypisanych przez użytkownika nie jest obecnie obsługiwane za pośrednictwem programu PowerShell. Zobacz następną sekcję na temat dodawania tożsamości zarządzanej użytkownik przypisany do istniejącego zestawu skalowania maszyn wirtualnych. Wracaj tutaj, aby zapoznać się z aktualizacjami.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Przypisz tożsamości zarządzanej użytkownik przypisany do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

Aby przypisać tożsamości zarządzanej użytkownik przypisany do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure:

1. Zaloguj się do platformy Azure za pomocą `Connect-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, takich jak "Współautor maszyny wirtualnej":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Pierwszy pobierania właściwości za pomocą zestawu skalowania maszyn wirtualnych `Get-AzureRmVM` polecenia cmdlet. Następnie można przypisać tożsamości zarządzanej użytkownik przypisany do zestawu skalowania maszyn wirtualnych, należy użyć `-IdentityType` i `-IdentityID` Włącz [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) polecenia cmdlet. Zastąp `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` własnymi wartościami.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości przypisanych przez użytkownika zarządzanej z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli zestaw skalowania maszyn wirtualnych ma wiele tożsamości zarządzanych przypisanych przez użytkownika, możesz usunąć wszystkie oprócz ostatni z nich przy użyciu następujących poleceń. Upewnij się, że parametry `<RESOURCE GROUP>` i `<VMSS NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY NAME>` Jest przypisanych do użytkowników zarządzanych tożsamości nazwę właściwości, która powinna pozostać w zestawie skalowania maszyn wirtualnych. Te informacje można znaleźć w sekcji tożsamości maszyny wirtualnej zestawu skalowania przy użyciu `az vmss show`:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Jeśli zestaw skalowania maszyn wirtualnych nie ma przypisany systemowo zarządzanych tożsamości i chcesz usunąć wszystkich przypisanych do użytkowników zarządzanych tożsamości z niego, użyj następującego polecenia:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Jeśli w zestawie skalowania maszyn wirtualnych znajdują się oba przypisana przez system i tożsamości przypisanych przez użytkownika zarządzanego, można usunąć wszystkich tożsamości przypisanych przez użytkownika zarządzanego, przełączając się używać tylko przypisany systemowo zarządzanych tożsamości.

```powershell 
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanych tożsamości dla zasobów platformy Azure — omówienie](overview.md)
- Pełne tworzenie maszyny Wirtualnej platformy Azure przewodników Szybki Start zobacz:
  
  - [Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















