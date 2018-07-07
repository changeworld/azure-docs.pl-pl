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
ms.openlocfilehash: 61fa6c94c0d717fe1e71bf8929f2e3b4a0982562
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903883"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>Konfigurowanie zestawu skalowania maszyn wirtualnych tożsamość usługi zarządzanej (MSI) przy użyciu programu PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wykonywać operacje tożsamości usługi zarządzanej na maszynę wirtualną skalowania Ustaw (zestawu skalowania maszyn wirtualnych), przy użyciu programu PowerShell:
- Włączanie i wyłączanie systemu przypisane tożsamość zestawu skalowania maszyn wirtualnych platformy Azure
- Dodawanie i usuwanie użytkownik, któremu przypisano tożsamość zestawu skalowania maszyn wirtualnych platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) Jeśli jeszcze go. 

## <a name="system-assigned-managed-identity"></a>Zarządzanych tożsamości przypisanej przez system

W tej sekcji dowiesz się, jak włączyć i usuwanie tożsamości przypisanej w systemie, przy użyciu programu Azure PowerShell.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Włącz system tożsamości przypisanej podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Do utworzenia zestawu skalowania maszyn wirtualnych przy użyciu tożsamości przypisanej w systemie włączony:

1. Zapoznaj się *przykład 1* w [polecenia New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) artykuł dokumentacji polecenia cmdlet do tworzenia zestawu skalowania maszyn wirtualnych przy użyciu tożsamości przypisanej w systemie.  Dodaj parametr `-IdentityType SystemAssigned` do `New-AzureRmVmssConfig` polecenia cmdlet:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Opcjonalnie) Dodaj przy użyciu rozszerzenia MSI zestawu skalowania maszyn wirtualnych `-Name` i `-Type` parametru [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) polecenia cmdlet. Można przekazać elementu "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny Wirtualnej i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu:

    > [!NOTE]
    > Ten krok jest opcjonalny, zgodnie z punktu końcowego tożsamości Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>Włącz system przypisane tożsamość istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli musisz włączyć tożsamości przypisanej w systemie, na istniejącego zestawu skalowania maszyn wirtualnych platformy Azure:

1. Zaloguj się do platformy Azure za pomocą `Login-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautor maszyny wirtualnej":

   ```powershell
   Login-AzureRmAccount
   ```

2. Najpierw pobierz właściwości zestawu skalowania maszyn wirtualnych przy użyciu [ `Get-AzureRmVmss` ](/powershell/module/azurerm.compute/get-azurermvmss) polecenia cmdlet. Następnie można włączyć tożsamości przypisanej w systemie, należy użyć `-IdentityType` Włącz [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) polecenia cmdlet:

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. Dodaj przy użyciu rozszerzenia MSI zestawu skalowania maszyn wirtualnych `-Name` i `-Type` parametru [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) polecenia cmdlet. Można przekazać elementu "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny Wirtualnej i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>Wyłącz system tożsamości przypisanej z zestawu skalowania maszyn wirtualnych platformy Azure

> [!NOTE]
> Wyłączanie tożsamości usługi zarządzanej z zestawu skalowania maszyn wirtualnych nie jest obecnie obsługiwane. W międzyczasie można przełączać się między przypisana przez System i tożsamości przypisanych użytkowników.

W przypadku zestawu skalowania maszyn wirtualnych nie potrzebuje już systemu tożsamości przypisanej, ale nadal wymaga tożsamości przypisanych przez użytkownika, należy użyć następującego polecenia cmdlet:

1. Zaloguj się do platformy Azure za pomocą `Login-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautor maszyny wirtualnej":

2. Uruchom następujące polecenie cmdlet:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>Tożsamości przypisanych przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć użytkownika z tożsamości przypisanej z zestawu skalowania maszyn wirtualnych przy użyciu programu Azure PowerShell.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>Przypisywanie użytkownika tożsamości przypisanej podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Tworzenie nowego zestawu skalowania maszyn wirtualnych przy użyciu tożsamości przypisanych przez użytkownika nie jest obecnie obsługiwane za pośrednictwem programu PowerShell. Zobacz następną sekcję na temat dodawania tożsamości przypisanych przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych. Sprawdź aktualizacje.

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>Przypisz tożsamość użytkownika do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

Aby przypisać użytkownika tożsamości przypisanej do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure:

1. Zaloguj się do platformy Azure za pomocą `Connect-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautor maszyny wirtualnej":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Najpierw pobrać właściwości maszyny Wirtualnej za pomocą `Get-AzureRmVM` polecenia cmdlet. Następnie można przypisać tożsamości przypisanych przez użytkownika do zestawu skalowania maszyn wirtualnych platformy Azure, należy użyć `-IdentityType` i `-IdentityID` Włącz [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) polecenia cmdlet. Zastąp `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` własnymi wartościami.

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Usuń użytkownika z tożsamości przypisanej z zestawu skalowania maszyn wirtualnych platformy Azure

> [!NOTE]
> Usuwanie wszystkich tożsamości przypisanych przez użytkownika z zestawu skalowania maszyn wirtualnych aktualnie nie jest obsługiwana, chyba że używany jest system tożsamości przypisanej. Sprawdź aktualizacje.

Jeśli Twojego zestawu skalowania maszyn wirtualnych ma wiele tożsamości przypisanych przez użytkownika, możesz usunąć wszystkie oprócz ostatni z nich przy użyciu następujących poleceń. Koniecznie Zastąp `<RESOURCE GROUP>` i `<VMSS NAME>` wartości parametrów własnymi wartościami. `<MSI NAME>` Jest tożsamości przypisanych przez użytkownika nazwa właściwości, która powinna pozostać na zestawu skalowania maszyn wirtualnych. Te informacje można znaleźć w sekcji tożsamości zestawu skalowania maszyn wirtualnych przy użyciu `az vmss show`:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

Jeśli Twojego zestawu skalowania maszyn wirtualnych ma przypisanej w systemie i tożsamości przypisanych przez użytkownika, możesz usunąć wszystkich użytkowników tożsamości przypisanych przez przełączenie na system tylko przypisane. Użyj następującego polecenia:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Powiązana zawartość

- [Omówienie usługi zarządzanej tożsamości usługi](overview.md)
- Pełne tworzenie maszyny Wirtualnej platformy Azure przewodników Szybki Start zobacz:
  
  - [Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















