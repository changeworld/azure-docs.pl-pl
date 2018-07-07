---
title: Jak skonfigurować MSI na Maszynie wirtualnej platformy Azure przy użyciu programu PowerShell
description: Krok po kroku instrukcje dotyczące konfigurowania tożsamość usługi zarządzanej (MSI) na Maszynie wirtualnej platformy Azure, przy użyciu programu PowerShell.
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
ms.openlocfilehash: add61dbbdaa90ae23e200163f1fa962adc2b3b8e
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902099"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurowanie maszyny Wirtualnej tożsamość usługi zarządzanej (MSI) przy użyciu programu PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>W tym artykule dowiesz się, jak wykonywać następujące operacje tożsamości usługi zarządzanej na Maszynie wirtualnej platformy Azure, przy użyciu programu PowerShell:
- 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) Jeśli jeszcze go.

## <a name="system-assigned-identity"></a>Tożsamości przypisanej w systemie

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamości przypisanej w systemie przy użyciu programu Azure PowerShell.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Włącz system tożsamości przypisanej podczas tworzenia maszyny Wirtualnej platformy Azure

Aby utworzyć Maszynę wirtualną platformy Azure z systemem przypisane włączono tożsamość:

1. Można skorzystać z jednego z usługi Azure VM poradników, kończenie tylko niezbędne sekcje ("Logowanie do platformy Azure", "Tworzenie grupy zasobów", "Tworzenie sieci grupy", "Tworzenie maszyny Wirtualnej").
    
    Gdy pojawi się w sekcji "Tworzenie maszyny Wirtualnej" Wprowadź niewielkich modyfikacji do [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) Składnia poleceń cmdlet. Pamiętaj dodać `-AssignIdentity:$SystemAssigned` parametr do aprowizowania maszyny Wirtualnej przy użyciu tożsamości przypisanej w systemie włączony, na przykład:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Opcjonalnie) Dodaj przy użyciu rozszerzenia maszyny Wirtualnej MSI `-Type` parametru [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać elementu "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny Wirtualnej i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Ten krok jest opcjonalny, zgodnie z punktu końcowego tożsamości Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również.

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Włącz system przypisane tożsamości istniejącej maszyny wirtualnej platformy Azure

Jeśli musisz włączyć tożsamości przypisanej w systemie, na istniejącej maszyny wirtualnej:

1. Zaloguj się do platformy Azure za pomocą `Login-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautor maszyny wirtualnej":

   ```powershell
   Login-AzureRmAccount
   ```

2. Najpierw pobrać właściwości maszyny Wirtualnej za pomocą `Get-AzureRmVM` polecenia cmdlet. Następnie można włączyć tożsamości przypisanej w systemie, należy użyć `-AssignIdentity` Włącz [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) polecenia cmdlet:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

3. (Opcjonalnie) Dodaj przy użyciu rozszerzenia maszyny Wirtualnej MSI `-Type` parametru [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać elementu "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny Wirtualnej i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu. Pamiętaj określić poprawny `-Location` parametru dopasowania lokalizacji istniejącej maszyny Wirtualnej:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Ten krok jest opcjonalny, zgodnie z punktu końcowego tożsamości Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również.

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Wyłącz system tożsamości przypisanej w Maszynie wirtualnej platformy Azure

> [!NOTE]
>  Wyłączanie tożsamości usługi zarządzanej z maszyny wirtualnej nie jest obecnie obsługiwane. W międzyczasie można przełączać się między przypisana przez System i tożsamości przypisanych użytkowników.

Jeśli masz maszyny wirtualnej, która nie wymaga systemu tożsamości przypisanej, ale nadal wymaga tożsamości przypisanych przez użytkownika, należy użyć następującego polecenia cmdlet:

1. Zaloguj się do platformy Azure za pomocą `Login-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautor maszyny wirtualnej":

   ```powershell
   Login-AzureRmAccount
   ```

2. Uruchom następujące polecenie cmdlet: 
    ```powershell       
    Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -IdentityType "UserAssigned"
    ```
Aby usunąć rozszerzenie tożsamości usługi Zarządzanej maszyny Wirtualnej użytkownika przełącznika - Name, za pomocą [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) polecenia cmdlet, określenie tej samej nazwie, które są używane podczas dodawania rozszerzenia:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>Tożsamości przypisanych przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć użytkownika z tożsamości przypisanej z maszyny Wirtualnej przy użyciu programu Azure PowerShell.

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>Przypisywanie użytkownika tożsamości przypisanej do maszyny Wirtualnej podczas tworzenia

Aby przypisać tożsamości przypisanych przez użytkownika na Maszynie wirtualnej platformy Azure, podczas tworzenia maszyny Wirtualnej:

1. Można skorzystać z jednego z usługi Azure VM poradników, kończenie tylko niezbędne sekcje ("Logowanie do platformy Azure", "Tworzenie grupy zasobów", "Tworzenie sieci grupy", "Tworzenie maszyny Wirtualnej"). 
  
    Gdy pojawi się w sekcji "Tworzenie maszyny Wirtualnej" Wprowadź niewielkich modyfikacji do [ `New-AzureRmVMConfig` ](/powershell/module/azurerm.compute/new-azurermvm) Składnia poleceń cmdlet. Dodaj `-IdentityType UserAssigned` i `-IdentityID ` parametry do aprowizowania maszyny Wirtualnej przy użyciu tożsamości przypisanych przez użytkownika.  Zastąp `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, i `<MSI NAME>` własnymi wartościami.  Na przykład:
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Opcjonalnie) Dodaj przy użyciu rozszerzenia maszyny Wirtualnej MSI `-Type` parametru [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać elementu "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny Wirtualnej i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu. Pamiętaj określić poprawny `-Location` parametru dopasowania lokalizacji istniejącej maszyny Wirtualnej:
      > [!NOTE]
    > Ten krok jest opcjonalny, zgodnie z punktu końcowego tożsamości Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>Przypisz tożsamość użytkownika do istniejącej maszyny Wirtualnej platformy Azure

Aby przypisać użytkownika tożsamości przypisanej do istniejącej maszyny Wirtualnej platformy Azure:

1. Zaloguj się do platformy Azure za pomocą `Connect-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautor maszyny wirtualnej":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Utwórz użytkownika przypisane przy użyciu tożsamości [New AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) polecenia cmdlet.  Uwaga `Id` w danych wyjściowych, ponieważ będzie on potrzebny w następnym kroku.

    > [!IMPORTANT]
    > Tworzenie tożsamości przypisanych przez użytkownika obsługuje tylko alfanumeryczne i łącznik (0 – 9 lub a-z lub A-Z lub -) znaków. Ponadto nazwa powinna być ograniczona do 24 znaków w celu przypisania do VM/VMSS, aby zapewnić prawidłowe działanie. Sprawdź aktualizacje. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy](known-issues.md)


  ```powershell
  New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
  ```
3. Pobierz właściwości maszyny Wirtualnej przy użyciu `Get-AzureRmVM` polecenia cmdlet. Następnie można przypisać tożsamości przypisanych przez użytkownika do maszyny Wirtualnej platformy Azure, należy użyć `-IdentityType` i `-IdentityID` Włącz [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) polecenia cmdlet.  Wartość`-IdentityId` parametr `Id` zauważyć w poprzednim kroku.  Zastąp `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, i `<USER ASSIGNED IDENTITY NAME>` własnymi wartościami.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Dodaj przy użyciu rozszerzenia maszyny Wirtualnej MSI `-Type` parametru [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać elementu "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny Wirtualnej i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu. Określ poprawny `-Location` parametru dopasowania lokalizacji istniejącej maszyny Wirtualnej.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuń użytkownika z przypisaną tożsamości zarządzanej maszyny wirtualnej platformy Azure

> [!NOTE]
>  Usuwanie wszystkich tożsamości przypisanych przez użytkownika z maszyny wirtualnej aktualnie nie jest obsługiwane, chyba że używany jest system tożsamości przypisanej. Sprawdź aktualizacje.

Jeśli maszyna wirtualna ma wiele tożsamości przypisanych przez użytkownika, możesz usunąć wszystkie oprócz ostatni z nich przy użyciu następujących poleceń. Koniecznie Zastąp `<RESOURCE GROUP>` i `<VM NAME>` wartości parametrów własnymi wartościami. `<MSI NAME>` Jest tożsamości przypisanych przez użytkownika nazwa właściwości, która powinna pozostać na maszynie Wirtualnej. Te informacje można znaleźć w sekcji tożsamości maszyny Wirtualnej przy użyciu `az vm show`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm
```

Jeśli maszyna wirtualna ma przypisanej w systemie i tożsamości przypisanych przez użytkownika, możesz usunąć wszystkich użytkowników tożsamości przypisanych przez przełączenie na system tylko przypisane. Użyj następującego polecenia:

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = $null
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Powiązana zawartość

- [Omówienie usługi zarządzanej tożsamości usługi](overview.md)
- Pełne tworzenie maszyny Wirtualnej platformy Azure przewodników Szybki Start zobacz:
  
  - [Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
