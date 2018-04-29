---
title: Jak skonfigurować MSI w maszynie Wirtualnej platformy Azure przy użyciu programu PowerShell
description: Krok kroku instrukcje dotyczące konfigurowania zarządzane tożsamości usługi (MSI) na maszynie Wirtualnej platformy Azure, przy użyciu programu PowerShell.
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
ms.openlocfilehash: 0c316a2c73b451e4d8f67ace7b41c38dcfbc52f0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurowanie maszyny Wirtualnej zarządzane usługi tożsamości (MSI) przy użyciu programu PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej zapewnia usług platformy Azure przy użyciu tożsamości automatycznie zarządzane w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, bez konieczności poświadczeń w kodzie. 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>W tym artykule Dowiedz się jak wykonać następujące operacje zarządzane tożsamości usługi na maszynie Wirtualnej platformy Azure, przy użyciu programu PowerShell:
- 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli znasz zarządzane tożsamość usługi, zapoznaj się [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między systemu przypisane i przypisać tożsamość użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) Jeśli jeszcze.

## <a name="system-assigned-identity"></a>System przypisane tożsamości

W tej sekcji dowiesz się, jak włączyć lub wyłączyć tożsamości systemu przypisane przy użyciu programu Azure PowerShell.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Włącz system przypisane tożsamości podczas tworzenia maszyny Wirtualnej platformy Azure

Aby utworzyć maszyny Wirtualnej platformy Azure w systemie przypisane włączone tożsamości:

1. Odwołanie do jednego z następujących Quickstarts maszyny Wirtualnej Azure Kończenie tylko niezbędne sekcje ("Logowanie do platformy Azure", "Tworzenie grupy zasobów", "Tworzenie grupy sieci", "Utwórz maszynę Wirtualną").
    
    Po przejściu do sekcji "Tworzenie maszyny Wirtualnej" wprowadzić nieznaczne modyfikację [AzureRmVMConfig nowy](/powershell/module/azurerm.compute/new-azurermvm) składnię poleceń cmdlet. Pamiętaj dodać `-AssignIdentity "SystemAssigned"` parametr, aby udostępnić maszynę Wirtualną z tożsamości systemu przypisane włączona, na przykład:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity "SystemAssigned" ...
    ```

   - [Utwórz maszynę wirtualną z systemem Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Opcjonalnie) Dodawanie przy użyciu rozszerzenia maszyny Wirtualnej MSI `-Type` parametru [AzureRmVMExtension zestaw](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny wirtualnej i nadaj mu nazwę o `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth token nabycia:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Ten krok jest opcjonalny, jako punkt końcowy tożsamości Azure wystąpienie metadanych usługi (IMDS), umożliwia pobranie również tokenów.

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Włącz system przypisane tożsamości na istniejącej maszynie Wirtualnej Azure

Jeśli musisz włączyć tożsamością systemu przypisane na już istniejącej maszynie wirtualnej:

1. Zaloguj się do platformy Azure przy użyciu `Login-AzureRmAccount`. Użyj konta, które jest skojarzone z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się, Twoje konto należy do roli, która umożliwia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautora maszyny wirtualnej":

   ```powershell
   Login-AzureRmAccount
   ```

2. Najpierw pobrać właściwości maszyny Wirtualnej za pomocą `Get-AzureRmVM` polecenia cmdlet. Następnie tożsamości systemu przypisane Użyj, aby włączyć `-AssignIdentity` Włącz [AzureRmVM aktualizacji](/powershell/module/azurerm.compute/update-azurermvm) polecenia cmdlet:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity "SystemAssigned"
   ```

3. (Opcjonalnie) Dodawanie przy użyciu rozszerzenia maszyny Wirtualnej MSI `-Type` parametru [AzureRmVMExtension zestaw](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny wirtualnej i nadaj mu nazwę o `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth uzyskania tokenu. Podaj poprawny `-Location` parametru dopasowania lokalizacji istniejącej maszyny wirtualnej:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Ten krok jest opcjonalny, jako punkt końcowy tożsamości Azure wystąpienie metadanych usługi (IMDS), umożliwia pobranie również tokenów.

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Wyłącz system przypisany tożsamości z maszyny Wirtualnej platformy Azure

> [!NOTE]
>  Wyłączanie zarządzane tożsamość usługi z maszyny wirtualnej nie jest obecnie obsługiwane. Tymczasem można przełączać się między przypisane systemu i przypisane tożsamości użytkowników.

Jeśli masz maszyny wirtualnej, która nie będzie już potrzebował systemu przypisane tożsamości, ale nadal należy przypisać tożsamości użytkownika, należy użyć następującego polecenia cmdlet:

1. Zaloguj się do platformy Azure przy użyciu `Login-AzureRmAccount`. Użyj konta, które jest skojarzone z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się, Twoje konto należy do roli, która umożliwia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautora maszyny wirtualnej":

   ```powershell
   Login-AzureRmAccount
   ```

2. Uruchom następujące polecenie cmdlet: 
    ```powershell       
    Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -IdentityType "UserAssigned"
    ```
Aby usunąć rozszerzenia maszyny Wirtualnej MSI użytkownika przełącznika - Name, z [AzureRmVMExtension Usuń](/powershell/module/azurerm.compute/remove-azurermvmextension) polecenia cmdlet, określenie tej samej nazwie, używane podczas dodawania rozszerzenia:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>Użytkownik, któremu przypisano tożsamości

W tej sekcji możesz dowiedzieć się, jak dodać i usunąć użytkownika przypisanego tożsamości z maszyny Wirtualnej przy użyciu programu Azure PowerShell.

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>Przypisz przypisać tożsamość maszyny Wirtualnej podczas tworzenia użytkownika

Aby przypisać tożsamość użytkownika z przypisanym do maszyny Wirtualnej platformy Azure, podczas tworzenia maszyny Wirtualnej:

1. Odwołanie do jednego z następujących Quickstarts maszyny Wirtualnej Azure Kończenie tylko niezbędne sekcje ("Logowanie do platformy Azure", "Tworzenie grupy zasobów", "Tworzenie grupy sieci", "Utwórz maszynę Wirtualną"). 
  
    Po przejściu do sekcji "Tworzenie maszyny Wirtualnej" wprowadzić nieznaczne modyfikację [ `New-AzureRmVMConfig` ](/powershell/module/azurerm.compute/new-azurermvm) składnię poleceń cmdlet. Dodaj `-IdentityType UserAssigned` i `-IdentityID ` parametry, aby udostępnić maszynę Wirtualną za pomocą tożsamości przypisane przez użytkownika.  Zastąp `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, i `<MSI NAME>` z własne wartości.  Na przykład:
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [Utwórz maszynę wirtualną z systemem Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Opcjonalnie) Dodawanie przy użyciu rozszerzenia maszyny Wirtualnej MSI `-Type` parametru [AzureRmVMExtension zestaw](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny wirtualnej i nadaj mu nazwę o `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth uzyskania tokenu. Podaj poprawny `-Location` parametru dopasowania lokalizacji istniejącej maszyny wirtualnej:
      > [!NOTE]
    > Ten krok jest opcjonalny, jako punkt końcowy tożsamości Azure wystąpienie metadanych usługi (IMDS), umożliwia pobranie również tokenów.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>Przypisz tożsamości użytkownika do istniejącej maszyny Wirtualnej Azure

Aby przypisać użytkownika przypisane tożsamości do istniejącej maszyny Wirtualnej platformy Azure:

1. Zaloguj się do platformy Azure przy użyciu `Connect-AzureRmAccount`. Użyj konta, które jest skojarzone z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się, Twoje konto należy do roli, która umożliwia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautora maszyny wirtualnej":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Utwórz użytkownika przypisane przy użyciu tożsamości [AzureRmUserAssignedIdentity nowy](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) polecenia cmdlet.  Uwaga `Id` w danych wyjściowych, ponieważ będzie on potrzebny w następnym kroku.

    > [!IMPORTANT]
    > Tworzenie użytkownika z przypisanym tożsamości z znaki specjalne (np. podkreślenie) w nazwie nie jest obecnie obsługiwane. Użyj znaków alfanumerycznych. Sprawdzanie dostępności aktualizacji.  Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy](known-issues.md)

  ```powershell
  New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
  ```
3. Pobierz właściwości maszyny Wirtualnej za pomocą `Get-AzureRmVM` polecenia cmdlet. Następnie można przypisać tożsamość użytkownika z przypisanym do maszyny Wirtualnej Azure, użyj `-IdentityType` i `-IdentityID` Włącz [AzureRmVM aktualizacji](/powershell/module/azurerm.compute/update-azurermvm) polecenia cmdlet.  Wartość`-IdentityId` parametr jest `Id` zanotowaną w poprzednim kroku.  Zastąp `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, i `<USER ASSIGNED IDENTITY NAME>` z własne wartości.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Dodawanie przy użyciu rozszerzenia maszyny Wirtualnej MSI `-Type` parametru [AzureRmVMExtension zestaw](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny wirtualnej i nadaj mu nazwę o `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth uzyskania tokenu. Określ poprawny `-Location` parametru dopasowania lokalizacji istniejącej maszyny wirtualnej.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuń użytkownika przypisanego tożsamości zarządzanego z maszyny Wirtualnej platformy Azure

> [!NOTE]
>  Usuwanie wszystkich tożsamości przypisane przez użytkownika z maszyny wirtualnej obecnie nie jest obsługiwane, chyba że masz przypisane tożsamości systemu. Sprawdzanie dostępności aktualizacji.

Jeśli maszyna wirtualna ma wiele tożsamości przypisane przez użytkownika, można usunąć wszystkie oprócz ostatnią za pomocą następujących poleceń. Pamiętaj zastąpić `<RESOURCE GROUP>` i `<VM NAME>` wartości parametrów z własne wartości. `<MSI NAME>` Jest tożsamość użytkownika z przypisanym nazwę właściwości, która może pozostawać na maszynie Wirtualnej. Te informacje można znaleźć za część tożsamości maszynę Wirtualną przy użyciu `az vm show`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm
```

Maszyna wirtualna ma przypisany systemu i przypisać tożsamości użytkownika, po usunięciu wszystkich użytkowników przypisanych tożsamości przełączając na system tylko przypisane. Użyj następującego polecenia:

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = $null
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Zawartość pokrewna

- [Omówienie tożsamość usługi zarządzanej](overview.md)
- Pełna tworzenia maszyny Wirtualnej platformy Azure — przewodniki szybkiego startu dla:
  
  - [Utwórz maszynę wirtualną z systemem Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















