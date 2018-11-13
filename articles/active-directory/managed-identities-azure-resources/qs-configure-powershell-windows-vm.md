---
title: Jak skonfigurować zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu programu PowerShell
description: Krok po kroku instrukcje dotyczące konfigurowania zarządzania tożsamościami dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu programu PowerShell.
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
ms.openlocfilehash: a29980da64775ca39f103b7430239f38c98a43fc
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578460"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule przy użyciu programu PowerShell, nauczysz się wykonywać następujące zarządzanych tożsamości dla operacji tworzenia zasobów platformy Azure w Maszynie wirtualnej platformy Azure:

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) Jeśli jeszcze go.

## <a name="system-assigned-managed-identity"></a>Przypisana przez system tożsamości zarządzanej

W tej sekcji dowiesz się, jak włączyć i wyłączyć przypisany systemowo tożsamości zarządzanej przy użyciu programu Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Włącz przypisany systemowo tożsamości zarządzanej podczas tworzenia maszyny Wirtualnej platformy Azure

Aby utworzyć Maszynę wirtualną platformy Azure z przypisany systemowo zarządzaną tożsamością, włączone, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) przypisania roli.  Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

1. Można skorzystać z jednego z usługi Azure VM poradników, kończenie tylko niezbędne sekcje ("Logowanie do platformy Azure", "Tworzenie grupy zasobów", "Tworzenie sieci grupy", "Tworzenie maszyny Wirtualnej").
    
    Gdy pojawi się w sekcji "Tworzenie maszyny Wirtualnej" Wprowadź niewielkich modyfikacji do [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) Składnia poleceń cmdlet. Pamiętaj dodać `-AssignIdentity:$SystemAssigned` parametr do aprowizowania maszyny Wirtualnej przy użyciu tożsamości przypisanych przez system, włączone, na przykład:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Opcjonalnie) Dodawanie zarządzanych tożsamości dla zasobów platformy Azure maszyny Wirtualnej rozszerzenie (zaplanowane do wycofania z użycia w styczniu 2019) przy użyciu `-Type` parametru [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać elementu "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny Wirtualnej i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Ten krok jest opcjonalny, zgodnie z punktu końcowego tożsamości Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również. Zarządzanych tożsamości dla rozszerzenia maszyny Wirtualnej zasoby platformy Azure planowane jest przestarzała w styczniu 2019 r. 

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Włącz przypisany systemowo tożsamość zarządzaną istniejącej maszyny wirtualnej platformy Azure

Aby włączyć tożsamość zarządzana na maszynie Wirtualnej, która pierwotnie została aprowizowana bez niego przypisana przez system, konto usługi musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) przypisania roli.  Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

1. Zaloguj się do platformy Azure za pomocą `Login-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.

   ```powershell
   Login-AzureRmAccount
   ```

2. Najpierw pobrać właściwości maszyny Wirtualnej za pomocą `Get-AzureRmVM` polecenia cmdlet. Następnie można włączyć tożsamości zarządzanych z przypisaną przez system, należy użyć `-AssignIdentity` Włącz [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) polecenia cmdlet:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

3. (Opcjonalnie) Dodawanie zarządzanych tożsamości dla zasobów platformy Azure maszyny Wirtualnej rozszerzenie (zaplanowane do wycofania z użycia w styczniu 2019) przy użyciu `-Type` parametru [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać elementu "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny Wirtualnej i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu. Pamiętaj określić poprawny `-Location` parametru dopasowania lokalizacji istniejącej maszyny Wirtualnej:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Ten krok jest opcjonalny, zgodnie z punktu końcowego tożsamości Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również.

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Wyłącz przypisany systemowo tożsamości zarządzanej maszyny wirtualnej platformy Azure

Aby wyłączyć przypisany systemowo tożsamości zarządzanej maszyny wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) przypisania roli.  Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

Jeśli masz maszyny wirtualnej, która nie wymaga tożsamości zarządzanej przypisana przez system, ale nadal wymaga tożsamości zarządzanej przypisanych przez użytkownika, należy użyć następującego polecenia cmdlet:

1. Zaloguj się do platformy Azure za pomocą `Login-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.

   ```powershell
   Login-AzureRmAccount
   ```

2. Pobierz właściwości maszyny Wirtualnej przy użyciu `Get-AzureRmVM` polecenia cmdlet i ustaw `-IdentityType` parametr `UserAssigned`:

   ```powershell   
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM    
   Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Jeśli masz maszyny wirtualnej, która nie wymaga tożsamości zarządzanej przypisana przez system, i przedstawiono w nim nie przypisanych do użytkowników zarządzanych tożsamości, użyj następujących poleceń:

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

Aby usunąć zarządzanych tożsamości dla rozszerzenia maszyny Wirtualnej zasoby platformy Azure, użytkownik przełącznik - Name, z [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) polecenia cmdlet, określenie tej samej nazwie, które są używane podczas dodawania rozszerzenia:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-managed-identity"></a>Przypisane przez użytkownika z tożsamości zarządzanej

W tej sekcji dowiesz się, jak dodawać i usuwać przypisanych przez użytkownika tożsamości zarządzanej maszyny wirtualnej przy użyciu programu Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Przypisz tożsamości zarządzanej użytkownik przypisany do maszyny Wirtualnej podczas tworzenia

Aby przypisać tożsamości przypisanych przez użytkownika do maszyny Wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) przypisań ról. Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

1. Można skorzystać z jednego z usługi Azure VM poradników, kończenie tylko niezbędne sekcje ("Logowanie do platformy Azure", "Tworzenie grupy zasobów", "Tworzenie sieci grupy", "Tworzenie maszyny Wirtualnej"). 
  
    Gdy pojawi się w sekcji "Tworzenie maszyny Wirtualnej" Wprowadź niewielkich modyfikacji do [ `New-AzureRmVMConfig` ](/powershell/module/azurerm.compute/new-azurermvm) Składnia poleceń cmdlet. Dodaj `-IdentityType UserAssigned` i `-IdentityID ` parametry do aprowizowania maszyny Wirtualnej przy użyciu tożsamości przypisanych przez użytkownika.  Zastąp `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, i `<USER ASSIGNED IDENTITY NAME>` własnymi wartościami.  Na przykład:
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Opcjonalnie) Dodaj tożsamość zarządzaną dla rozszerzenie maszyny Wirtualnej zasoby platformy Azure przy użyciu polecenia `-Type` parametru [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać elementu "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny Wirtualnej i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu. Pamiętaj określić poprawny `-Location` parametru dopasowania lokalizacji istniejącej maszyny Wirtualnej:
      > [!NOTE]
    > Ten krok jest opcjonalny, zgodnie z punktu końcowego tożsamości Azure wystąpienie metadanych usługi (IMDS), można użyć do pobierania tokenów, jak również. Zarządzanych tożsamości dla rozszerzenia maszyny Wirtualnej zasoby platformy Azure planowane jest przestarzała w styczniu 2019 r.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Przypisz tożsamości zarządzanej użytkownik przypisany do istniejącej maszyny Wirtualnej platformy Azure

Aby przypisać tożsamości przypisanych przez użytkownika do maszyny Wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) przypisań ról. Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

1. Zaloguj się do platformy Azure za pomocą `Connect-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.

   ```powershell
   Connect-AzureRmAccount
   ```

2. Tworzenie przy użyciu przypisanych przez użytkownika z tożsamości zarządzanej [New AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) polecenia cmdlet.  Uwaga `Id` w danych wyjściowych, ponieważ będzie on potrzebny w następnym kroku.

   > [!IMPORTANT]
   > Tworzenie zarządzanych tożsamości przypisanych przez użytkownika obsługuje tylko alfanumeryczne i łącznik (0 – 9 lub a-z lub A-Z lub -) znaków. Ponadto nazwa powinna być ograniczona do 24 znaków w celu przypisania do VM/VMSS, aby zapewnić prawidłowe działanie. Wracaj tutaj, aby zapoznać się z aktualizacjami. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy](known-issues.md)

   ```powershell
   New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Pobierz właściwości maszyny Wirtualnej przy użyciu `Get-AzureRmVM` polecenia cmdlet. Następnie można przypisać przypisanych przez użytkownika tożsamości zarządzanej maszyny Wirtualnej platformy Azure, należy użyć `-IdentityType` i `-IdentityID` Włącz [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) polecenia cmdlet.  Wartość`-IdentityId` parametr `Id` zauważyć w poprzednim kroku.  Zastąp `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, i `<USER ASSIGNED IDENTITY NAME>` własnymi wartościami.

   > [!WARNING]
   > Aby zachować żadnych wcześniej przypisanych przez użytkownika tożsamości zarządzanych przypisane do maszyny Wirtualnej, należy zbadać `Identity` własności obiektu maszyny Wirtualnej (na przykład `$vm.Identity`).  Jeśli żaden użytkownik przypisany tożsamości zarządzanych powrócisz, objęte następujące polecenie, wraz z nowego zarządzanych tożsamości przypisanych przez użytkownika, który chcesz przypisać do maszyny Wirtualnej.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Dodaj tożsamość zarządzaną dla zasobów platformy Azure maszyny Wirtualnej rozszerzenie (zaplanowane do wycofania z użycia w styczniu 2019) przy użyciu `-Type` parametru [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać elementu "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny Wirtualnej i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu. Określ poprawny `-Location` parametru dopasowania lokalizacji istniejącej maszyny Wirtualnej.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuń przypisanych przez użytkownika tożsamości zarządzanej maszyny wirtualnej platformy Azure

Aby usunąć tożsamości przypisanych przez użytkownika do maszyny Wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) przypisania roli.

Jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika, możesz usunąć wszystkie oprócz ostatni z nich przy użyciu następujących poleceń. Upewnij się, że parametry `<RESOURCE GROUP>` i `<VM NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY NAME>` Jest przypisanych do użytkowników zarządzanych tożsamości nazwę właściwości, która powinna pozostać na maszynie Wirtualnej. Te informacje można znaleźć, badając `Identity` własności obiektu maszyny Wirtualnej.  Na przykład `$vm.Identity`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Jeśli maszyna wirtualna nie ma przypisany systemowo zarządzanych tożsamości i chcesz usunąć wszystkich przypisanych do użytkowników zarządzanych tożsamości z niego, użyj następującego polecenia:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Jeśli maszyna wirtualna ma przypisany systemowo i przypisanych do użytkowników zarządzanych tożsamości, możesz usunąć wszystkich tożsamości przypisanych przez użytkownika zarządzanego, przełączając się używać tylko przypisany systemowo zarządzanych tożsamości.

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanych tożsamości dla zasobów platformy Azure — omówienie](overview.md)
- Pełne tworzenie maszyny Wirtualnej platformy Azure przewodników Szybki Start zobacz:
  
  - [Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
