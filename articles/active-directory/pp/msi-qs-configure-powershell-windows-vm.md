---
title: Jak skonfigurować MSI na Maszynie wirtualnej platformy Azure przy użyciu programu PowerShell
description: Krok po kroku instrukcje dotyczące konfigurowania tożsamość usługi zarządzanej (MSI) na Maszynie wirtualnej platformy Azure, przy użyciu programu PowerShell.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611003"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurowanie maszyny Wirtualnej tożsamość usługi zarządzanej (MSI) przy użyciu programu PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć i usunąć tożsamości usługi Zarządzanej dla maszyny Wirtualnej platformy Azure, przy użyciu programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Ponadto Zainstaluj [najnowszą wersję programu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) (w wersji 4.3.1 lub nowszej) Jeśli jeszcze nie.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Włączanie tożsamości usługi Zarządzanej podczas tworzenia maszyny Wirtualnej platformy Azure

Aby utworzyć Maszynę wirtualną z obsługą MSI:

1. Można skorzystać z jednego z usługi Azure VM poradników, kończenie tylko niezbędne sekcje ("Logowanie do platformy Azure", "Tworzenie grupy zasobów", "Tworzenie sieci grupy", "Tworzenie maszyny Wirtualnej"). 

   > [!IMPORTANT] 
   > Gdy pojawi się w sekcji "Tworzenie maszyny Wirtualnej" Wprowadź niewielkich modyfikacji do [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) Składnia poleceń cmdlet. Pamiętaj dodać `-IdentityType "SystemAssigned"` parametr do aprowizowania maszyny Wirtualnej przy użyciu pliku MSI, na przykład:
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell](~/articles/virtual-machines/windows/quick-create-powershell.md)
   - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](~/articles/virtual-machines/linux/quick-create-powershell.md)



2. Dodaj przy użyciu rozszerzenia maszyny Wirtualnej MSI `-Type` parametru [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać elementu "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny Wirtualnej i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Włączanie tożsamości usługi Zarządzanej istniejącej maszyny wirtualnej platformy Azure

Jeśli musisz włączyć tożsamości usługi Zarządzanej w istniejącej maszyny wirtualnej:

1. Zaloguj się do platformy Azure za pomocą `Connect-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautor maszyny wirtualnej":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Najpierw pobrać właściwości maszyny Wirtualnej za pomocą `Get-AzureRmVM` polecenia cmdlet. Następnie można włączyć tożsamości usługi Zarządzanej, należy użyć `-IdentityType` Włącz [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) polecenia cmdlet:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Dodaj przy użyciu rozszerzenia maszyny Wirtualnej MSI `-Type` parametru [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) polecenia cmdlet. Można przekazać elementu "ManagedIdentityExtensionForWindows" lub "ManagedIdentityExtensionForLinux", w zależności od typu maszyny Wirtualnej i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu. Pamiętaj określić poprawny `-Location` parametru dopasowania lokalizacji istniejącej maszyny Wirtualnej:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Usuń plik MSI z maszyny Wirtualnej platformy Azure

Jeśli masz maszyny wirtualnej, która nie wymaga Instalatora MSI, możesz użyć `RemoveAzureRmVMExtension` polecenia cmdlet, aby usunąć plik MSI z maszyny Wirtualnej:

1. Zaloguj się do platformy Azure za pomocą `Connect-AzureRmAccount`. Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautor maszyny wirtualnej":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Użyj `-Name` przełącznik z [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) polecenia cmdlet, określenie tej samej nazwie, które są używane podczas dodawania rozszerzenia:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Powiązana zawartość

- [Omówienie usługi zarządzanej tożsamości usługi](msi-overview.md)
- Pełne tworzenie maszyny Wirtualnej platformy Azure przewodników Szybki Start zobacz:
  
  - [Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell](~/articles/virtual-machines/windows/quick-create-powershell.md) 
  - [Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell](~/articles/virtual-machines/linux/quick-create-powershell.md) 

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.
















