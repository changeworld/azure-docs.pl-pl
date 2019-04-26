---
title: Jak skonfigurować zarządzanych tożsamości dla zasobów platformy Azure na zestaw skalowania maszyn wirtualnych można ustawić przy użyciu programu PowerShell
description: Krok po kroku instrukcje dotyczące konfigurowania systemu i zarządzanych tożsamości przypisanych przez użytkownika na zestaw skalowania maszyn wirtualnych zestawu przy użyciu programu PowerShell.
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
ms.date: 11/27/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4917720af2396b68ccd36cc0410c9acbbba2d9b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60304591"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na zestawach skalowania maszyn wirtualnych przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule przy użyciu programu PowerShell, nauczysz się wykonywać zarządzanych tożsamości dla operacji tworzenia zasobów platformy Azure w zestawie skalowania maszyn wirtualnych:
- Włączanie i wyłączanie przypisany systemowo tożsamość zarządzaną w zestawie skalowania maszyn wirtualnych
- Dodawanie i usuwanie tożsamości zarządzanej użytkownik przypisany na zestaw skalowania maszyn wirtualnych

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisany systemu i zarządzane przez użytkownika tożsamości przypisanej](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujące przypisania kontroli dostępu opartej na rolach platformy Azure:

    > [!NOTE]
    > Nie dodatkowych Azure przypisań ról katalogu usługi AD wymagane.

    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) utworzyć skalę maszyny wirtualnej zestawu i włączanie i usuwanie przypisany systemowo zarządzanych i/lub tożsamości przypisanych przez użytkownika zarządzanej z zestawu skalowania maszyn wirtualnych.
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) tożsamości zarządzanej roli do utworzenia przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli przypisywania i usuwania, użytkownik przypisany tożsamości zarządzanej, od i do zestawu skalowania maszyn wirtualnych.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](/powershell/azure/install-az-ps) Jeśli jeszcze go. 

## <a name="system-assigned-managed-identity"></a>Przypisana przez system tożsamości zarządzanej

W tej sekcji dowiesz się, jak włączyć i Usuń przypisany systemowo tożsamości zarządzanej przy użyciu programu Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Włącz przypisany systemowo tożsamości zarządzanej podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Aby utworzyć maszyny wirtualnej zestawu skalowania z przypisany systemowo tożsamość zarządzaną włączone:

1. Zapoznaj się *przykład 1* w [New AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) artykule odwołanie do polecenia cmdlet, aby utworzyć zestaw skalowania maszyn wirtualnych zestawu przy użyciu tożsamości zarządzanej przypisana przez system.  Dodaj parametr `-IdentityType SystemAssigned` do `New-AzVmssConfig` polecenia cmdlet:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```
> [!NOTE]
> Rozszerzenie zestawu skalowania maszyn wirtualnych z zasobami platformy Azure, ale wkrótce zostaną wycofane, może opcjonalnie udostępnić zarządzanych tożsamości. Zalecamy używanie punktu końcowego usługi Azure Instance Metadata tożsamości do uwierzytelniania. Aby uzyskać więcej informacji, zobacz [zaprzestać korzystania z rozszerzenia maszyny Wirtualnej i rozpocząć korzystanie z punktu końcowego IMDS platformy Azure do uwierzytelniania](howto-migrate-vm-extension.md).


## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Włącz przypisany systemowo tożsamość zarządzana na podstawie istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli musisz włączyć przypisany systemowo tożsamości zarządzanej na podstawie istniejącego zestawu skalowania maszyn wirtualnych platformy Azure:

1. Zaloguj się do platformy Azure za pomocą `Connect-AzAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, takich jak "Współautor maszyny wirtualnej":

   ```powershell
   Connect-AzAccount
   ```

2. Pierwszy pobierania właściwości za pomocą zestawu skalowania maszyn wirtualnych [ `Get-AzVmss` ](/powershell/module/az.compute/get-azvmss) polecenia cmdlet. Następnie można włączyć tożsamości zarządzanych z przypisaną przez system, należy użyć `-IdentityType` Włącz [AzVmss aktualizacji](/powershell/module/az.compute/update-azvmss) polecenia cmdlet:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

> [!NOTE]
> Rozszerzenie zestawu skalowania maszyn wirtualnych z zasobami platformy Azure, ale wkrótce zostaną wycofane, może opcjonalnie udostępnić zarządzanych tożsamości. Zalecamy używanie punktu końcowego usługi Azure Instance Metadata tożsamości do uwierzytelniania. Aby uzyskać więcej informacji, zobacz [migracja z rozszerzenia maszyny Wirtualnej do endpoint IMDS platformy Azure do uwierzytelniania](howto-migrate-vm-extension.md).

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłącz przypisany systemowo tożsamość zarządzaną z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz zestaw skalowania maszyn wirtualnych, które nie wymaga tożsamości zarządzanej przypisana przez system, ale nadal wymaga tożsamości zarządzanej przypisanych przez użytkownika, należy użyć następującego polecenia cmdlet:

1. Zaloguj się do platformy Azure za pomocą `Connect-AzAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, takich jak "Współautor maszyny wirtualnej":

2. Uruchom następujące polecenie cmdlet:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie wymaga tożsamości zarządzanej przypisana przez system, i przedstawiono w nim nie przypisanych do użytkowników zarządzanych tożsamości, użyj następujących poleceń:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodawać i usuwać przypisane przez użytkownika tożsamości zarządzanej maszyny wirtualnej zestawu skalowania przy użyciu programu Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Przypisz przypisanych przez użytkownika tożsamości zarządzanej podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Tworzenie nowej maszyny wirtualnej zestawu skalowania z tożsamości zarządzanej przypisanych przez użytkownika nie jest obecnie obsługiwane za pośrednictwem programu PowerShell. Zobacz następną sekcję na temat dodawania tożsamości zarządzanej użytkownik przypisany do istniejącego zestawu skalowania maszyn wirtualnych. Wracaj tutaj, aby zapoznać się z aktualizacjami.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Przypisz tożsamości zarządzanej użytkownik przypisany do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

Aby przypisać tożsamości zarządzanej użytkownik przypisany do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure:

1. Zaloguj się do platformy Azure za pomocą `Connect-AzAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, takich jak "Współautor maszyny wirtualnej":

   ```powershell
   Connect-AzAccount
   ```

2. Pierwszy pobierania właściwości za pomocą zestawu skalowania maszyn wirtualnych `Get-AzVM` polecenia cmdlet. Następnie można przypisać tożsamości zarządzanej użytkownik przypisany do zestawu skalowania maszyn wirtualnych, należy użyć `-IdentityType` i `-IdentityID` Włącz [AzVmss aktualizacji](/powershell/module/az.compute/update-azvmss) polecenia cmdlet. Zastąp `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` własnymi wartościami.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości przypisanych przez użytkownika zarządzanej z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli zestaw skalowania maszyn wirtualnych ma wiele tożsamości zarządzanych przypisanych przez użytkownika, możesz usunąć wszystkie oprócz ostatni z nich przy użyciu następujących poleceń. Upewnij się, że parametry `<RESOURCE GROUP>` i `<VIRTUAL MACHINE SCALE SET NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY NAME>` Jest przypisanych do użytkowników zarządzanych tożsamości nazwę właściwości, która powinna pozostać w zestawie skalowania maszyn wirtualnych. Te informacje można znaleźć w sekcji tożsamości maszyny wirtualnej zestawu skalowania przy użyciu `az vmss show`:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Jeśli zestaw skalowania maszyn wirtualnych nie ma przypisany systemowo zarządzanych tożsamości i chcesz usunąć wszystkich przypisanych do użytkowników zarządzanych tożsamości z niego, użyj następującego polecenia:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Jeśli w zestawie skalowania maszyn wirtualnych znajdują się oba przypisana przez system i tożsamości przypisanych przez użytkownika zarządzanego, można usunąć wszystkich tożsamości przypisanych przez użytkownika zarządzanego, przełączając się używać tylko przypisany systemowo zarządzanych tożsamości.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanych tożsamości dla zasobów platformy Azure — omówienie](overview.md)
- Pełne tworzenie maszyny Wirtualnej platformy Azure przewodników Szybki Start zobacz:
  
  - [Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















