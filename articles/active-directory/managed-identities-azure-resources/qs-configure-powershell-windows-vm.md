---
title: Konfigurowanie tożsamości zarządzanych na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell — usługa Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu programu PowerShell.
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
ms.openlocfilehash: f24c89477d71df3f497590b49841403576343bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547215"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule za pomocą programu PowerShell dowiesz się, jak wykonać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na maszynie Wirtualnej platformy Azure.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md). **Pamiętaj, aby przejrzeć [różnicę między tożsamością zarządzaną przypisaną do systemu a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell,](/powershell/azure/install-az-ps) jeśli jeszcze tego nie zrobiłeś.

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana do systemu

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamości zarządzanej przypisane do systemu przy użyciu programu Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Włączanie tożsamości zarządzanej przypisanej do systemu podczas tworzenia maszyny Wirtualnej platformy Azure

Aby utworzyć maszynę wirtualną platformy Azure z włączoną tożsamością zarządzaną przypisaną do systemu, twoje konto wymaga przypisania roli [współautora maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Zapoznaj się z jednym z następujących przewodników Szybki start na platformie Azure VM, wypełniając tylko niezbędne sekcje ("Zaloguj się na platformie Azure", "Utwórz grupę zasobów", "Utwórz grupę sieciową", "Utwórz maszynę wirtualną").
    
    Po dojście do sekcji "Tworzenie maszyny Wirtualnej", należy wprowadzić nieznaczną modyfikację składni cmdlet [New-AzVMConfig.](/powershell/module/az.compute/new-azvm) Pamiętaj, aby `-AssignIdentity:$SystemAssigned` dodać parametr do aprowizowania maszyny Wirtualnej z włączoną tożsamością przypisaną przez system, na przykład:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Tworzenie maszyny wirtualnej z systemem Windows za pomocą programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Tworzenie maszyny wirtualnej systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Włączanie tożsamości zarządzanej przypisanej do systemu na istniejącej maszynie wirtualnej platformy Azure

Aby włączyć tożsamość zarządzaną przypisaną do systemu na maszynie wirtualnej, która została pierwotnie zainicjowana bez niej, twoje konto wymaga przypisania roli [współautora maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Zaloguj się na `Connect-AzAccount`platformie Azure przy użyciu programu . Użyj konta skojarzonego z subskrypcją platformy Azure zawierającą maszynę wirtualną.

   ```powershell
   Connect-AzAccount
   ```

2. Najpierw pobierz właściwości maszyny Wirtualnej `Get-AzVM` przy użyciu polecenia cmdlet. Następnie, aby włączyć tożsamość zarządzaną `-AssignIdentity` przypisaną do systemu, użyj przełącznika polecenia cmdlet [Update-AzVM:](/powershell/module/az.compute/update-azvm)

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>Dodawanie tożsamości przypisanej tożsamości systemu maszyn wirtualnych do grupy

Po włączeniu tożsamości przypisanej systemowi na maszynie wirtualnej można ją dodać do grupy.  Poniższa procedura dodaje system maszyny Wirtualnej przypisaną tożsamość do grupy.

1. Zaloguj się na `Connect-AzAccount`platformie Azure przy użyciu programu . Użyj konta skojarzonego z subskrypcją platformy Azure zawierającą maszynę wirtualną.

   ```powershell
   Connect-AzAccount
   ```

2. Pobieranie i zanotowywać `ObjectID` (jak określono w `Id` polu zwracanych wartości) jednostki usługi maszyny Wirtualnej:

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. Pobierz i zanotuj `ObjectID` (jak określono w `Id` polu zwróconych wartości) grupy:

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. Dodaj jednostkę usługi maszyny Wirtualnej do grupy:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z maszyny Wirtualnej platformy Azure

Aby wyłączyć tożsamość zarządzaną przypisaną do systemu na maszynie wirtualnej, twoje konto wymaga przypisania roli [współautora maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

Jeśli masz maszynę wirtualną, która nie potrzebuje już tożsamości zarządzanej przypisanej do systemu, ale nadal potrzebuje tożsamości zarządzanych przypisanych przez użytkownika, użyj następującego polecenia cmdlet:

1. Zaloguj się na `Connect-AzAccount`platformie Azure przy użyciu programu . Użyj konta skojarzonego z subskrypcją platformy Azure zawierającą maszynę wirtualną.

   ```powershell
   Connect-AzAccount
   ```

2. Pobierz właściwości maszyny Wirtualnej `Get-AzVM` przy użyciu polecenia `-IdentityType` cmdlet i ustaw parametr na: `UserAssigned`

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Jeśli masz maszynę wirtualną, która nie potrzebuje już tożsamości zarządzanej przypisanej do systemu i nie ma tożsamości zarządzanych przypisanych przez użytkownika, użyj następujących poleceń:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```



## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przypisaną przez użytkownika z maszyny Wirtualnej przy użyciu programu Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do maszyny Wirtualnej podczas tworzenia

Aby przypisać tożsamość przypisaną przez użytkownika do maszyny Wirtualnej, twoje konto wymaga przypisania roli [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Zapoznaj się z jednym z następujących przewodników Szybki start na platformie Azure VM, wypełniając tylko niezbędne sekcje ("Zaloguj się na platformie Azure", "Utwórz grupę zasobów", "Utwórz grupę sieciową", "Utwórz maszynę wirtualną"). 
  
    Po dojście do sekcji "Tworzenie maszyny Wirtualnej", [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) należy wprowadzić nieznaczną modyfikację składni cmdlet. Dodaj `-IdentityType UserAssigned` i `-IdentityID` parametry do aprowizowania maszyny Wirtualnej z tożsamością przypisaną przez użytkownika.  `<VM NAME>`Zamień `<RESROURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>` `<SUBSCRIPTION ID>`i własnymi wartościami.  Przykład:
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Tworzenie maszyny wirtualnej z systemem Windows za pomocą programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Tworzenie maszyny wirtualnej systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącej maszyny Wirtualnej platformy Azure

Aby przypisać tożsamość przypisaną przez użytkownika do maszyny Wirtualnej, twoje konto wymaga przypisania roli [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Zaloguj się na `Connect-AzAccount`platformie Azure przy użyciu programu . Użyj konta skojarzonego z subskrypcją platformy Azure zawierającą maszynę wirtualną.

   ```powershell
   Connect-AzAccount
   ```

2. Utwórz tożsamość zarządzaną przypisaną przez użytkownika przy użyciu polecenia cmdlet [New-AzUserAssignedIdentity.](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity)  Zanotuj `Id` dane wyjściowe, ponieważ będzie to potrzebne w następnym kroku.

   > [!IMPORTANT]
   > Tworzenie tożsamości zarządzanych przypisanych przez użytkownika obsługuje tylko znaki alfanumeryczne, podkreślenia i łącznika \_ (0-9 lub a-z lub A-Z lub -). Ponadto nazwa powinna być ograniczona od 3 do 128 długości znaków dla przypisania do maszyny Wirtualnej/VMSS do poprawnego działania. Aby uzyskać więcej informacji, zobacz [często zadawane pytania i znane problemy](known-issues.md)

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Pobieranie właściwości maszyny Wirtualnej `Get-AzVM` przy użyciu polecenia cmdlet. Następnie, aby przypisać tożsamość zarządzaną przypisaną przez `-IdentityType` użytkownika `-IdentityID` do maszyny Wirtualnej platformy Azure, użyj i włącz polecenie cmdlet [Update-AzVM.](/powershell/module/az.compute/update-azvm)  Wartość parametru`-IdentityId` jest `Id` odnotowana w poprzednim kroku.  `<VM NAME>`Zamień `<RESROURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>` `<SUBSCRIPTION ID>`i własnymi wartościami.

   > [!WARNING]
   > Aby zachować wszystkie wcześniej przypisane przez użytkownika tożsamości zarządzane przypisane do maszyny Wirtualnej, `Identity` `$vm.Identity`kwerenda właściwości obiektu maszyny Wirtualnej (na przykład ).  Jeśli zwracane są dowolne tożsamości zarządzane przypisane przez użytkownika, dołącz je do następującego polecenia wraz z nową tożsamością zarządzana przypisaną przez nowego użytkownika, którą chcesz przypisać do maszyny Wirtualnej.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny Wirtualnej platformy Azure

Aby usunąć tożsamość przypisaną przez użytkownika do maszyny Wirtualnej, twoje konto wymaga przypisania roli [współautora maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

Jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika, można usunąć wszystkie oprócz ostatniej za pomocą następujących poleceń. Upewnij się, że parametry `<RESOURCE GROUP>` i `<VM NAME>` zostały zastąpione własnymi wartościami. Jest `<USER ASSIGNED IDENTITY NAME>` właściwością nazwy tożsamości zarządzanej przypisanej przez użytkownika, która powinna pozostać na maszynie Wirtualnej. Te informacje można znaleźć, `Identity` przeprowadzając kwerendę właściwości obiektu maszyny Wirtualnej.  Na przykład: `$vm.Identity`

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Jeśli maszyna wirtualna nie ma tożsamości zarządzanej przypisanej do systemu i chcesz usunąć z niej wszystkie tożsamości zarządzane przypisane przez użytkownika, użyj następującego polecenia:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Jeśli maszyna wirtualna ma zarówno tożsamości zarządzane przypisane do systemu, jak i przypisane przez użytkownika, można usunąć wszystkie tożsamości zarządzane przypisane przez użytkownika, przełączając się na używanie tylko tożsamości zarządzanych przypisanych do systemu.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie tożsamości zarządzanych dla zasobów platformy Azure](overview.md)
- Aby uzyskać pełną platformę Szybkiego startu tworzenia maszyn wirtualnych platformy Azure, zobacz:
  
  - [Tworzenie maszyny wirtualnej z systemem Windows za pomocą programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Tworzenie maszyny wirtualnej z systemem Linux za pomocą programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
