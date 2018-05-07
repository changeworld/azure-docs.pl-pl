---
title: Jak skonfigurować MSI w VMSS Azure przy użyciu programu PowerShell
description: Krok po kroku instrukcje dotyczące konfigurowania systemu i użytkownika przypisane tożsamości na VMSS Azure przy użyciu programu PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 8dad00995ab9f90e9eebec16697d55f4491431e0
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>Skonfiguruj VMSS zarządzane usługi tożsamości (MSI) przy użyciu programu PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej zapewnia usług platformy Azure przy użyciu tożsamości automatycznie zarządzane w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule Dowiedz się jak wykonać następujące operacje zarządzane tożsamości usługi na Azure maszyny wirtualnej skali Ustaw (VMSS), za pomocą programu PowerShell:
- Włączanie i wyłączanie tożsamość na VMSS Azure przypisanego przez system
- Dodawanie i usuwanie użytkownika przypisanego tożsamość na VMSS Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli znasz zarządzane tożsamość usługi, zapoznaj się [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między systemu przypisane i przypisać tożsamość użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) Jeśli jeszcze. 

## <a name="system-assigned-managed-identity"></a>Przypisana tożsamości zarządzanych przez system

W tej sekcji dowiesz sposobu włączania i usuwanie tożsamości systemu przypisane, przy użyciu programu Azure PowerShell.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Włącz system przypisane tożsamości podczas tworzenia VMSS Azure

Aby utworzyć VMSS o tożsamości systemu przypisane włączone:

1. Zapoznaj się *przykład 1* w [AzureRmVmssConfig nowy](/powershell/module/azurerm.compute/new-azurermvmssconfig) artykule odwołanie do polecenia cmdlet, aby utworzyć VMSS o tożsamości systemu przypisane.  Dodaj parametr `-IdentityType SystemAssigned` do `New-AzureRmVmssConfig` polecenia cmdlet:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Opcjonalnie) Dodaj przy użyciu rozszerzenia MSI VMSS `-Name` i `-Type` parametru [AzureRmVmssExtension Dodaj](/powershell/module/azurerm.compute/add-azurermvmssextension) polecenia cmdlet. Można przekazać "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny wirtualnej i nadaj mu nazwę o `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth token nabycia:

    > [!NOTE]
    > Ten krok jest opcjonalny, jako punkt końcowy tożsamości Azure wystąpienie metadanych usługi (IMDS), umożliwia pobranie również tokenów.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>Włącz system przypisane tożsamość na istniejących VMSS Azure

Jeśli musisz włączyć tożsamość systemu przypisane na istniejących VMSS Azure:

1. Zaloguj się do platformy Azure przy użyciu `Login-AzureRmAccount`. Użyj konta, które jest skojarzone z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się, Twoje konto należy do roli, która umożliwia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautora maszyny wirtualnej":

   ```powershell
   Login-AzureRmAccount
   ```

2. Najpierw pobrać za pomocą właściwości VMSS [ `Get-AzureRmVmss` ](/powershell/module/azurerm.compute/get-azurermvmss) polecenia cmdlet. Następnie tożsamości systemu przypisane Użyj, aby włączyć `-IdentityType` Włącz [AzureRmVM aktualizacji](/powershell/module/azurerm.compute/update-azurermvm) polecenia cmdlet:

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. Dodaj przy użyciu rozszerzenia MSI VMSS `-Name` i `-Type` parametru [AzureRmVmssExtension Dodaj](/powershell/module/azurerm.compute/add-azurermvmssextension) polecenia cmdlet. Można przekazać "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny wirtualnej i nadaj mu nazwę o `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth token nabycia:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>Wyłącz system przypisany tożsamości z VMSS Azure

> [!NOTE]
> Wyłączanie zarządzane tożsamość usługi z zestawu skalowania maszyn wirtualnych nie jest obecnie obsługiwane. Tymczasem można przełączać się między przypisane systemu i przypisane tożsamości użytkowników.

Jeśli masz zestawu skalowania maszyn wirtualnych nie będzie już potrzebował systemu przypisane tożsamości, ale nadal należy przypisać tożsamości użytkownika, należy użyć następującego polecenia cmdlet:

1. Zaloguj się do platformy Azure przy użyciu `Login-AzureRmAccount`. Użyj konta, które jest skojarzone z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się, Twoje konto należy do roli, która umożliwia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautora maszyny wirtualnej":

2. Uruchom następujące polecenie cmdlet:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>Użytkownik, któremu przypisano tożsamości

W tej sekcji możesz dowiedzieć się, jak dodać i usunąć użytkownika przypisanego tożsamości z VMSS, przy użyciu programu Azure PowerShell.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>Przypisać użytkownicy przypisani tożsamości podczas tworzenia VMSS Azure

Tworzenie nowego VMSS z użytkownikiem przypisane tożsamości nie jest obecnie obsługiwane za pośrednictwem programu PowerShell. Jak dodać tożsamość użytkownika z przypisanym do istniejących VMSS, zobacz następną sekcję. Sprawdzanie dostępności aktualizacji.

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>Przypisz tożsamości użytkownika do istniejącego VMSS Azure

Aby przypisać użytkownika do istniejącego VMSS Azure przypisuje tożsamości:

1. Zaloguj się do platformy Azure przy użyciu `Connect-AzureRmAccount`. Użyj konta, które jest skojarzone z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się, Twoje konto należy do roli, która umożliwia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautora maszyny wirtualnej":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Najpierw pobrać właściwości maszyny Wirtualnej za pomocą `Get-AzureRmVM` polecenia cmdlet. Następnie można przypisać tożsamość użytkownika z przypisanym do Azure VMSS, użyj `-IdentityType` i `-IdentityID` Włącz [AzureRmVM aktualizacji](/powershell/module/azurerm.compute/update-azurermvm) polecenia cmdlet. Zastąp `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` z własne wartości.

   > [!IMPORTANT]
   > Tworzenie tożsamości użytkowników przypisanych obsługuje tylko alfanumeryczne i łączniki (0-9 lub a-z lub A-Z lub -) znaków. Ponadto nazwa powinna być ograniczona do 24 znaków do przypisania do maszyny Wirtualnej/VMSS działała poprawnie. Sprawdzanie dostępności aktualizacji. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy](known-issues.md)


   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Usuń użytkownika przypisanego tożsamości z VMSS Azure

> [!NOTE]
> Usuwanie wszystkich tożsamości przypisane przez użytkownika z zestawu skalowania maszyn wirtualnych obecnie nie jest obsługiwane, chyba że masz przypisane tożsamości systemu. Sprawdzanie dostępności aktualizacji.

Jeśli Twoje VMSS ma wiele tożsamości przypisane przez użytkownika, można usunąć wszystkie oprócz ostatnią za pomocą następujących poleceń. Pamiętaj zastąpić `<RESOURCE GROUP>` i `<VMSS NAME>` wartości parametrów z własne wartości. `<MSI NAME>` Jest tożsamość użytkownika z przypisanym nazwę właściwości, która może pozostawać na VMSS. Te informacje można znaleźć przez w sekcji tożsamości za pomocą VMSS `az vmss show`:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

Twoje VMSS ma przypisany systemu i przypisać tożsamości użytkownika, po usunięciu wszystkich użytkowników przypisanych tożsamości przełączając na system tylko przypisane. Użyj następującego polecenia:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Zawartość pokrewna

- [Omówienie tożsamość usługi zarządzanej](overview.md)
- Pełna tworzenia maszyny Wirtualnej platformy Azure — przewodniki szybkiego startu dla:
  
  - [Utwórz maszynę wirtualną z systemem Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















