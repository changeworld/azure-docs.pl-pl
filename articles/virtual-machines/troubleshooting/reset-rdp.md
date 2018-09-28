---
title: Resetowanie hasła lub w konfiguracji pulpitu zdalnego na maszynie Wirtualnej Windows | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zresetować hasło do konta lub usług pulpitu zdalnego na maszynie Wirtualnej Windows przy użyciu witryny Azure portal lub programu Azure PowerShell.
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
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: cynthn
ms.openlocfilehash: a8db7ef82136bae51c99bcfd2a4743e09ebf5712
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413823"
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Jak zresetować swoje hasło logowania w maszyny Wirtualnej z systemem Windows lub usługi pulpitu zdalnego
Jeśli nie możesz połączyć z maszyną wirtualną (VM) Windows, można zresetować hasła administratora lokalnego lub zresetowanie konfiguracji usługi pulpitu zdalnego (nie obsługiwany na kontrolerach domeny Windows). Aby zresetować hasło, można użyć witryny Azure portal lub rozszerzenie dostępu do maszyny Wirtualnej w programie Azure PowerShell. Po zalogowaniu się do maszyny Wirtualnej, należy zresetować hasło dla tego użytkownika.  
Jeśli używasz programu PowerShell, upewnij się, że masz [najnowszy moduł programu PowerShell, zainstalować i skonfigurować](/powershell/azure/overview) i zalogowano się do subskrypcji platformy Azure. Możesz również [wykonać te kroki dla maszyn wirtualnych utworzonych za pomocą klasycznego modelu wdrażania](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

## <a name="ways-to-reset-configuration-or-credentials"></a>Sposoby resetowania konfiguracji lub poświadczeń
Możesz zresetować usług pulpitu zdalnego i poświadczenia na kilka różnych sposobów, w zależności od potrzeb:

- [Resetuj przy użyciu witryny Azure portal](#azure-portal)
- [Resetuj przy użyciu programu Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure Portal
Aby rozwinąć menu portalu, kliknij trzy paski w lewym górnym rogu, a następnie kliknij przycisk **maszyn wirtualnych**:

![Przeglądaj w poszukiwaniu sieci maszyny Wirtualnej platformy Azure](./media/reset-rdp/Portal-Select-VM.png)

### <a name="reset-the-local-administrator-account-password"></a>**Zresetuj hasło konta administratora lokalnego**

Wybierz maszynę wirtualną Windows, a następnie kliknij przycisk **pomoc techniczna i rozwiązywanie problemów** > **Resetuj hasło**. Zostanie wyświetlony blok resetowania hasła:

![Strony resetowania hasła](./media/reset-rdp/Portal-RM-PW-Reset-Windows.png)

Wprowadź nazwę użytkownika i nowe hasło, a następnie kliknij przycisk **aktualizacji**. Spróbuj ponownie nawiązać połączenie z maszyną wirtualną.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Zresetowanie konfiguracji usługi pulpitu zdalnego**

Wybierz maszynę wirtualną Windows, a następnie kliknij przycisk **pomoc techniczna i rozwiązywanie problemów** > **Resetuj hasło**. Zostanie wyświetlony blok resetowania hasła. 

![Resetowanie konfiguracji RDP](./media/reset-rdp/Portal-RM-RDP-Reset.png)

Wybierz **tylko konfiguracji resetowania** z menu rozwijanego, następnie kliknij przycisk **aktualizacji**. Spróbuj ponownie nawiązać połączenie z maszyną wirtualną.


## <a name="vmaccess-extension-and-powershell"></a>Rozszerzenie VMAccess i programu PowerShell
Upewnij się, że masz [najnowszy moduł programu PowerShell, zainstalować i skonfigurować](/powershell/azure/overview) i zalogowano się do subskrypcji platformy Azure za pomocą `Connect-AzureRmAccount` polecenia cmdlet.

### <a name="reset-the-local-administrator-account-password"></a>**Zresetuj hasło konta administratora lokalnego**
Resetuj nazwy użytkownika lub hasło administratora przy użyciu [AzureRmVMAccessExtension zestaw](/powershell/module/azurerm.compute/set-azurermvmaccessextension) polecenia cmdlet programu PowerShell. TypeHandlerVersion należy 2.0 lub nowszej, jak w wersji 1 jest przestarzały. 

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
> Jeśli wpiszesz inną nazwę niż bieżące konto administratora lokalnego na maszynie Wirtualnej, rozszerzenie VMAccess dodać konto administratora lokalnego z tą nazwą i przypisać określonego hasła do tego konta. Jeśli konto administratora lokalnego na maszynie Wirtualnej istnieje, spowoduje to zresetowanie hasła i jeśli to konto jest wyłączone, rozszerzenie VMAccess włączy ją.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Zresetowanie konfiguracji usługi pulpitu zdalnego**
Zresetuj dostęp zdalny do maszyny Wirtualnej za pomocą [AzureRmVMAccessExtension zestaw](/powershell/module/azurerm.compute/set-azurermvmaccessextension) polecenia cmdlet programu PowerShell. Poniższy przykład powoduje zresetowanie rozszerzenia dostępu o nazwie `myVMAccess` na maszynie Wirtualnej o nazwie `myVM` w `myResourceGroup` grupy zasobów:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> W dowolnym momencie maszyna wirtualna może mieć tylko jednego agenta dostęp do maszyny Wirtualnej. Aby ustawić właściwości agenta dostęp do maszyny Wirtualnej pomyślnie, `-ForceRerun` opcja może być używana. Korzystając z `-ForceRerun`, upewnij się, że jest używana ta sama nazwa agenta dostęp do maszyny Wirtualnej w przypadku użycia w poprzednie polecenia.

Jeśli nadal nie możesz połączyć zdalne z maszyną wirtualną, zobacz więcej czynności do wypróbowania w [Rozwiązywanie problemów z pulpitu zdalnego połączenia z systemem Windows maszyny wirtualnej platformy Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). W przypadku utraty połączenia z kontrolerem domeny Windows, należy ją przywrócić z kopii zapasowej kontrolera domeny.

## <a name="next-steps"></a>Kolejne kroki
Jeśli rozszerzenie dostępu do maszyny Wirtualnej platformy Azure nie odpowiada i nie można zresetować hasła, możesz to zrobić [Resetowanie hasła lokalnego Windows w trybie offline](../windows/reset-local-password-without-agent.md). Ta metoda jest bardziej zaawansowany proces i wymaga podłączenia wirtualnego dysku twardego problematyczne maszyny wirtualnej do innej maszyny Wirtualnej. Wykonaj kroki opisane w tym artykule najpierw, a tylko próba metoda resetowania hasła w trybie offline w ostateczności.

[Rozszerzenia maszyn wirtualnych platformy Azure i funkcji](../extensions/features-windows.md)

[Nawiązać połączenie z maszyną wirtualną platformy Azure przy użyciu protokołu RDP lub SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Rozwiązywanie problemów z połączeniami pulpitu zdalnego z maszyną wirtualną na podstawie Windows Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

