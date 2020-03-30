---
title: Resetowanie usług pulpitu zdalnego lub hasła administratora w maszynie Wirtualnej systemu Windows | Dokumenty firmy Microsoft
description: Dowiedz się, jak zresetować hasło do konta lub usługi pulpitu zdalnego na maszynie Wirtualnej systemu Windows przy użyciu witryny Azure portal lub programu Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 580ec443dc087f270e30856c336a5699bbf1ae71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058440"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Resetowanie usług pulpitu zdalnego lub hasła administratora w maszynie Wirtualnej systemu Windows
Jeśli nie można połączyć się z maszyną wirtualną systemu Windows, możesz zresetować hasło administratora lokalnego lub zresetować konfigurację usług pulpitu zdalnego (nieobsługiwanych na kontrolerach domeny systemu Windows). Możesz zresetować hasło w witrynie Azure Portal lub za pośrednictwem rozszerzenia VM Access w programie Azure PowerShell. Po zalogowaniu się na maszynie wirtualnej zresetuj hasło administratora lokalnego.  
Jeśli używasz programu PowerShell, upewnij się, że masz [zainstalowany i skonfigurowany najnowszy moduł programu PowerShell](/powershell/azure/overview) i zalogowano się do subskrypcji platformy Azure. Możesz również [wykonać te kroki w przypadku maszyn wirtualnych utworzonych za pomocą klasycznego modelu wdrożenia](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

Możesz zresetować usługi pulpitu zdalnego oraz  poświadczenia, korzystając z następujących metod:

- [Resetowanie przy użyciu witryny Azure portal](#reset-by-using-the-azure-portal)

- [Resetowanie przy użyciu rozszerzenia VMAccess i programu PowerShell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Resetowanie przy użyciu witryny Azure portal

Najpierw zaloguj się do [witryny Azure portal,](https://portal.azure.com) a następnie wybierz maszyny **wirtualne** w menu po lewej stronie. 

### <a name="reset-the-local-administrator-account-password"></a>**Resetowanie hasła do konta administratora lokalnego**

1. Wybierz maszynę wirtualną systemu Windows, a następnie wybierz pozycję **Resetuj hasło** w obszarze **Obsługa + Rozwiązywanie problemów**. Zostanie wyświetlone okno **Resetowanie hasła.**

2. Wybierz **pozycję Resetuj hasło**, wprowadź nazwę użytkownika i hasło, a następnie wybierz pozycję **Aktualizuj**. 

3. Spróbuj ponownie połączyć się z maszyną wirtualną.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Resetowanie konfiguracji usług pulpitu zdalnego**

Ten proces umożliwi usługę pulpitu zdalnego na maszynie wirtualnej i utworzy regułę zapory dla domyślnego portu RDP 3389.

1. Wybierz maszynę wirtualną systemu Windows, a następnie wybierz pozycję **Resetuj hasło** w obszarze **Obsługa + Rozwiązywanie problemów**. Zostanie wyświetlone okno **Resetowanie hasła.** 

2. Wybierz **tylko resetuj konfigurację,** a następnie wybierz pozycję **Aktualizuj**. 

3. Spróbuj ponownie połączyć się z maszyną wirtualną.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Resetowanie przy użyciu rozszerzenia VMAccess i programu PowerShell

Najpierw upewnij się, że masz [zainstalowany i skonfigurowany najnowszy moduł programu PowerShell](/powershell/azure/overview) i zalogowano się do subskrypcji platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)

### <a name="reset-the-local-administrator-account-password"></a>**Resetowanie hasła do konta administratora lokalnego**

- Zresetuj hasło administratora lub nazwę użytkownika za pomocą polecenia cmdlet [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell. Ustawienie `typeHandlerVersion` musi być 2.0 lub większe, ponieważ wersja 1 jest przestarzała. 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzAccount 
    Select-AzSubscription -SubscriptionId $SubID 
    Set-AzVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > Jeśli wprowadzisz inną nazwę niż bieżące konto administratora lokalnego na maszynie wirtualnej, rozszerzenie VMAccess doda konto administratora lokalnego o tej nazwie i przypisze określone hasło do tego konta. Jeśli istnieje konto administratora lokalnego na maszynie wirtualnej, rozszerzenie VMAccess zresetuje hasło. Jeśli konto jest wyłączone, rozszerzenie VMAccess włączy go.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Resetowanie konfiguracji usług pulpitu zdalnego**

1. Zresetuj dostęp zdalny do maszyny Wirtualnej za pomocą polecenia cmdlet [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell. Poniższy przykład resetuje rozszerzenie `myVMAccess` dostępu o `myVM` nazwie `myResourceGroup` na maszynie wirtualnej o nazwie w grupie zasobów:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > W dowolnym momencie maszyna wirtualna może mieć tylko jednego agenta dostępu do maszyny Wirtualnej. Aby ustawić właściwości agenta dostępu maszyny `-ForceRerun` Wirtualnej, użyj tej opcji. Podczas korzystania `-ForceRerun`upewnij się, że używasz tej samej nazwy dla agenta dostępu do maszyny Wirtualnej, który mógł być używany w poprzednich poleceniach.

1. Jeśli nadal nie możesz połączyć się zdalnie z maszyną [wirtualną, zobacz Rozwiązywanie problemów z połączeniami pulpitu zdalnego z maszyną wirtualną platformy Azure z systemem Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). W przypadku utraty połączenia z kontrolerem domeny systemu Windows należy przywrócić je z kopii zapasowej kontrolera domeny.

## <a name="next-steps"></a>Następne kroki

- Jeśli rozszerzenie dostępu do maszyny Wirtualnej platformy Azure nie odpowiada i nie można zresetować hasła, możesz [zresetować lokalne hasło systemu Windows w trybie offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ta metoda jest bardziej zaawansowana i wymaga połączenia wirtualnego dysku twardego problematycznej maszyny Wirtualnej z inną maszyną wirtualną. Wykonaj kroki opisane w tym artykule i spróbuj zresetować hasło w trybie offline tylko wtedy, gdy te kroki nie działają.

- [Dowiedz się więcej o rozszerzeniach i funkcjach maszyn wirtualnych platformy Azure.](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

- [Połącz się z maszyną wirtualną platformy Azure za pomocą protokołu RDP lub SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

- [Rozwiązywanie problemów z połączeniami pulpitu zdalnego z maszyną wirtualną platformy Azure z systemem Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

