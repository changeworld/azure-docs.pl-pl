---
title: Resetowanie hasła lub w konfiguracji pulpitu zdalnego na maszynie Wirtualnej z systemem Windows | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można zresetować hasło do konta lub usług pulpitu zdalnego na maszynie Wirtualnej z systemem Windows przy użyciu portalu Azure lub programu Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: cynthn
ms.openlocfilehash: b61b7501c94e9682a3b324488caf119ce4aad3df
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267207"
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Jak można zresetować usług pulpitu zdalnego lub jego hasło logowania na maszynie wirtualnej systemu Windows
Jeśli nie można połączyć z maszyną wirtualną (VM) systemu Windows, można zresetować hasła administratora lokalnego lub zresetowanie konfiguracji usługi pulpitu zdalnego (nieobsługiwane na kontrolerach domeny z systemem Windows). Aby zresetować hasło, można użyć portalu Azure lub rozszerzenia dostępu do maszyny Wirtualnej w programie Azure PowerShell. Gdy użytkownik zalogował się do maszyny Wirtualnej, należy zresetować hasło dla tego użytkownika.  
Jeśli używasz programu PowerShell, upewnij się, że masz [najnowsze modułu PowerShell zainstalowany i skonfigurowany](/powershell/azure/overview) i jest zalogowany do subskrypcji platformy Azure. Możesz również [wykonaj te kroki dla maszyn wirtualnych utworzonych za pomocą klasycznego modelu wdrożenia](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

## <a name="ways-to-reset-configuration-or-credentials"></a>Sposoby resetowania konfiguracji lub poświadczeń
Można zresetować usług pulpitu zdalnego i poświadczenia na kilka różnych sposobów, w zależności od potrzeb:

- [Resetuj przy użyciu portalu Azure](#azure-portal)
- [Resetuj przy użyciu programu Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure Portal
Aby rozwinąć menu portalu, kliknij trzy paski w lewym górnym rogu, a następnie kliknij **maszyn wirtualnych**:

![Przeglądaj w poszukiwaniu Azure maszyny Wirtualnej](./media/reset-rdp/Portal-Select-VM.png)

### <a name="reset-the-local-administrator-account-password"></a>**Zresetuj hasło konta administratora lokalnego**

Wybierz maszynę wirtualną systemu Windows, a następnie kliknij przycisk **pomocy technicznej i rozwiązywania problemów** > **resetowania hasła**. Zostanie wyświetlony blok resetowania hasła:

![Strona Resetowanie hasła](./media/reset-rdp/Portal-RM-PW-Reset-Windows.png)

Wprowadź nazwę użytkownika i nowe hasło, a następnie kliknij przycisk **aktualizacji**. Spróbuj ponownie nawiązać połączenie z maszyną Wirtualną.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Zresetowanie konfiguracji usługi pulpitu zdalnego**

Wybierz maszynę wirtualną systemu Windows, a następnie kliknij przycisk **pomocy technicznej i rozwiązywania problemów** > **resetowania hasła**. Zostanie wyświetlony blok resetowania hasła. 

![Zresetowanie konfiguracji RDP](./media/reset-rdp/Portal-RM-RDP-Reset.png)

Wybierz **Resetowanie tylko konfiguracji** z menu rozwijanego, następnie kliknij przycisk **aktualizacji**. Spróbuj ponownie nawiązać połączenie z maszyną Wirtualną.


## <a name="vmaccess-extension-and-powershell"></a>Rozszerzenia VMAccess i programu PowerShell
Upewnij się, że masz [najnowsze modułu PowerShell zainstalowany i skonfigurowany](/powershell/azure/overview) i jest zalogowany do subskrypcji platformy Azure z `Connect-AzureRmAccount` polecenia cmdlet.

### <a name="reset-the-local-administrator-account-password"></a>**Zresetuj hasło konta administratora lokalnego**
Resetuj nazwa użytkownika lub hasło administratora z [AzureRmVMAccessExtension zestaw](/powershell/module/azurerm.compute/set-azurermvmaccessextension) polecenia cmdlet programu PowerShell. TypeHandlerVersion musi być w wersji 2.0 lub wyższej, jako wersja 1 jest przestarzały. 

```powershell
$SubID = "<SUBSCRIPTION ID>" 
$RgName = "<RESOURCE GROUP NAME>" 
$VmName = "<VM NAME>" 
$Location = "<LOCATION>" 
 
Connect-AzureRmAccount 
Select-AzureRMSubscription -SubscriptionId $SubID 
Set-AzureRmVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
```

> [!NOTE] 
> Jeśli wpiszesz nazwę inną niż bieżące konto administratora lokalnego na maszynie Wirtualnej, rozszerzenia VMAccess Dodaj konto administratora lokalnego o takiej nazwie i przypisać określonego hasła do tego konta. Jeśli konto administratora lokalnego na maszynie Wirtualnej istnieje, spowoduje zresetowanie hasła i wyłączenie konta rozszerzenia VMAccess włączy ją.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Zresetowanie konfiguracji usługi pulpitu zdalnego**
Zresetuj zdalny dostęp do maszyny Wirtualnej z [AzureRmVMAccessExtension zestaw](/powershell/module/azurerm.compute/set-azurermvmaccessextension) polecenia cmdlet programu PowerShell. Poniższy przykład resetuje rozszerzenie dostępu o nazwie `myVMAccess` na Maszynie wirtualnej o nazwie `myVM` w `myResourceGroup` grupy zasobów:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> W dowolnym momencie maszyna wirtualna może mieć tylko jednego agenta dostęp do maszyny Wirtualnej. Aby ustawić właściwości agenta dostęp maszyny Wirtualnej pomyślnie, `-ForceRerun` opcja może być używana. Korzystając z `-ForceRerun`, upewnij się, że jest używana ta sama nazwa agenta dostępu maszyny Wirtualnej, ponieważ używany w poprzednie polecenia.

Jeśli użytkownik nadal nie może połączyć się zdalnie z maszyną wirtualną, zobacz więcej kroki w celu na [połączeń Rozwiązywanie problemów z pulpitu zdalnego do maszyny wirtualnej opartych na systemie Windows Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). W przypadku utraty połączenia z kontrolerem domeny systemu Windows, należy przywrócić z kopii zapasowej kontrolera domeny.

## <a name="next-steps"></a>Kolejne kroki
Jeśli rozszerzenia dostępu do maszyny Wirtualnej platformy Azure nie odpowiada i nie można zresetować hasło, możesz [zresetować lokalne hasło systemu Windows w trybie offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ta metoda jest procesem bardziej zaawansowane i wymaga łączenie się inną maszynę Wirtualną z wirtualnego dysku twardego maszyny wirtualnej powodować problemy. Wykonaj kroki opisane w tym artykule najpierw, a następnie spróbuj tylko metody resetowania hasła w trybie offline w ostateczności.

[Rozszerzenia maszyny Wirtualnej platformy Azure i funkcje](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Połącz do maszyny wirtualnej platformy Azure z protokołu RDP lub SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Rozwiązywanie problemów z połączeniami pulpitu zdalnego do maszyny wirtualnej opartych na systemie Windows Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

