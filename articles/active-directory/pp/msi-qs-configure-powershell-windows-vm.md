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
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9a466a5c695277a7b5833f997e2ad7281c962f3f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31588255"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurowanie maszyny Wirtualnej zarządzane usługi tożsamości (MSI) przy użyciu programu PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamość usługi zarządzanej zapewnia usług platformy Azure przy użyciu tożsamości automatycznie zarządzane w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule dowiesz sposobu włączania i usunąć MSI dla maszyny Wirtualnej platformy Azure, przy użyciu programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Ponadto zainstalować [najnowszą wersję programu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) (wersja 4.3.1 lub nowszym) Jeśli jeszcze.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Włącz MSI podczas tworzenia maszyny Wirtualnej platformy Azure

Aby utworzyć maszynę Wirtualną z włączoną MSI:

1. Odwołanie do jednego z następujących Quickstarts maszyny Wirtualnej Azure Kończenie tylko niezbędne sekcje ("Logowanie do platformy Azure", "Tworzenie grupy zasobów", "Tworzenie grupy sieci", "Utwórz maszynę Wirtualną"). 

   > [!IMPORTANT] 
   > Po przejściu do sekcji "Tworzenie maszyny Wirtualnej" wprowadzić nieznaczne modyfikację [AzureRmVMConfig nowy](/powershell/module/azurerm.compute/new-azurermvm) składnię poleceń cmdlet. Pamiętaj dodać `-IdentityType "SystemAssigned"` parametr, aby udostępnić maszynę Wirtualną za pomocą Instalatora MSI, na przykład:
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [Utwórz maszynę wirtualną z systemem Windows przy użyciu programu PowerShell](~/articles/virtual-machines/windows/quick-create-powershell.md)
   - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](~/articles/virtual-machines/linux/quick-create-powershell.md)



2. Dodawanie przy użyciu rozszerzenia maszyny Wirtualnej MSI `-Type` parametru [AzureRmVMExtension zestaw](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny wirtualnej i nadaj mu nazwę o `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth token nabycia:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Włącz MSI na istniejącej maszynie Wirtualnej Azure

Jeśli musisz włączyć MSI na już istniejącej maszynie wirtualnej:

1. Zaloguj się do platformy Azure przy użyciu `Connect-AzureRmAccount`. Użyj konta, które jest skojarzone z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się, Twoje konto należy do roli, która umożliwia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautora maszyny wirtualnej":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Najpierw pobrać właściwości maszyny Wirtualnej za pomocą `Get-AzureRmVM` polecenia cmdlet. Następnie MSI Użyj, aby włączyć `-IdentityType` Włącz [AzureRmVM aktualizacji](/powershell/module/azurerm.compute/update-azurermvm) polecenia cmdlet:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Dodawanie przy użyciu rozszerzenia maszyny Wirtualnej MSI `-Type` parametru [AzureRmVMExtension zestaw](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny wirtualnej i nadaj mu nazwę o `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth uzyskania tokenu. Podaj poprawny `-Location` parametru dopasowania lokalizacji istniejącej maszyny wirtualnej:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Usuń MSI w maszynie Wirtualnej platformy Azure

Jeśli masz maszyny wirtualnej, która nie będzie już potrzebował MSI, możesz użyć `RemoveAzureRmVMExtension` polecenia cmdlet, aby usunąć MSI z maszyny Wirtualnej:

1. Zaloguj się do platformy Azure przy użyciu `Connect-AzureRmAccount`. Użyj konta, które jest skojarzone z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się, Twoje konto należy do roli, która umożliwia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautora maszyny wirtualnej":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Użyj `-Name` przełącznik z [AzureRmVMExtension Usuń](/powershell/module/azurerm.compute/remove-azurermvmextension) polecenia cmdlet, określenie tej samej nazwie, używane podczas dodawania rozszerzenia:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Zawartość pokrewna

- [Omówienie tożsamość usługi zarządzanej](msi-overview.md)
- Pełna tworzenia maszyny Wirtualnej platformy Azure — przewodniki szybkiego startu dla:
  
  - [Utwórz maszynę wirtualną z systemem Windows przy użyciu programu PowerShell](~/articles/virtual-machines/windows/quick-create-powershell.md) 
  - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](~/articles/virtual-machines/linux/quick-create-powershell.md) 

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.
















