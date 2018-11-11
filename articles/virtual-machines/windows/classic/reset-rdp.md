---
title: Resetowanie hasła lub w konfiguracji pulpitu zdalnego na maszynie Wirtualnej Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zresetować hasło do konta lub usług pulpitu zdalnego na maszynie Wirtualnej Windows utworzonych przy użyciu klasycznego modelu wdrażania przy użyciu witryny Azure portal lub programu Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cynthn
ms.openlocfilehash: e91f75299dddf0ac173499a531ea959292082a9f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246552"
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>Jak zresetować swoje hasło logowania na maszynie Wirtualnej Windows utworzonych przy użyciu klasycznego modelu wdrażania lub usługi pulpitu zdalnego
> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Resource Manager i model klasyczny](../../../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Możesz również [wykonać te kroki dla maszyn wirtualnych utworzonych przy użyciu modelu wdrażania usługi Resource Manager](../reset-rdp.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]


Jeśli nie możesz połączyć z maszyną wirtualną (VM) Windows, można zresetować hasła administratora lokalnego lub zresetowanie konfiguracji usługi pulpitu zdalnego. Aby zresetować hasło, można użyć witryny Azure portal lub rozszerzenie dostępu do maszyny Wirtualnej w programie Azure PowerShell.

## <a name="ways-to-reset-configuration-or-credentials"></a>Sposoby resetowania konfiguracji lub poświadczeń
Możesz zresetować usług pulpitu zdalnego i poświadczenia na kilka różnych sposobów, w zależności od potrzeb:

- [Resetuj przy użyciu witryny Azure portal](#azure-portal)
- [Resetuj przy użyciu programu Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure Portal
Możesz użyć [witryny Azure portal](https://portal.azure.com) do Zresetuj usługę pulpitu zdalnego. Aby rozwinąć menu portalu, kliknij trzy paski w lewym górnym rogu, a następnie kliknij przycisk **maszyny wirtualne (klasyczne)**:

![Przeglądaj w poszukiwaniu sieci maszyny Wirtualnej platformy Azure](./media/reset-rdp/Portal-Select-Classic-VM.png)

Wybierz maszynę wirtualną Windows, a następnie kliknij przycisk **Resetuj dostęp zdalny...** . Można zresetować konfiguracji pulpitu zdalnego zostanie wyświetlone następujące okno dialogowe:

![Strona konfiguracji RDP resetowania](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

Możesz także zresetować nazwę użytkownika i hasło konta administratora lokalnego. Z maszyny Wirtualnej, kliknij przycisk **pomoc techniczna i rozwiązywanie problemów** > **Resetuj hasło**. Zostanie wyświetlony blok resetowania hasła:

![Strony resetowania hasła](./media/reset-rdp/Portal-PW-Reset-Windows.png)

Po wprowadzeniu nową nazwę użytkownika i hasło, kliknij przycisk **Zapisz**.

## <a name="vmaccess-extension-and-powershell"></a>Rozszerzenie VMAccess i programu PowerShell
Upewnij się, że Agent maszyny Wirtualnej jest zainstalowany na maszynie wirtualnej. Rozszerzenie VMAccess nie muszą zostać zainstalowane zanim użyjesz go, tak długo, jak jest dostępny Agent maszyny Wirtualnej. Sprawdź, czy Agent maszyny Wirtualnej już został zainstalowany przy użyciu następującego polecenia. (Zastąp "myCloudService" i "myVM" według nazwy usługi w chmurze i maszyny Wirtualnej, odpowiednio. Dowiedz się te nazwy, uruchamiając `Get-AzureVM` bez żadnych parametrów.)

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

Jeśli **zapisu hosta** polecenie wyświetla **True**, jest zainstalowany Agent maszyny Wirtualnej. Jeśli zostanie wyświetlony **False**, zobacz instrukcje oraz link do pobrania w [VM Agent i rozszerzenia — część 2](https://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) wpis w blogu platformy Azure.

Jeśli utworzono maszynę wirtualną przy użyciu portalu, sprawdź, czy `$vm.GetInstance().ProvisionGuestAgent` zwraca **True**. W przeciwnym razie można ustawić za pomocą tego polecenia:

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

To polecenie uniemożliwia następujący błąd, gdy uruchamiasz **AzureVMExtension zestaw** polecenia w następnych krokach: "Aprowizacji agenta gościa musi być włączona na obiektu maszyny Wirtualnej przed ustawieniem rozszerzenia dostępu do maszyn wirtualnych IaaS."

### <a name="reset-the-local-administrator-account-password"></a>**Zresetuj hasło konta administratora lokalnego**
Tworzenie poświadczeń logowania przy użyciu bieżącej nazwy konta administratora lokalnego i nowe hasło, a następnie uruchom `Set-AzureVMAccessExtension` w następujący sposób.

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

Wpisz inną nazwę niż bieżące konto rozszerzenie VMAccess zmienia nazwę konta administratora lokalnego, przypisuje hasło do tego konta i wystawia wylogowania pulpitu zdalnego. Jeśli lokalne konto administratora jest wyłączone, rozszerzenie VMAccess włączy ją.

Te polecenia również zresetowanie konfiguracji usługi pulpitu zdalnego.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Zresetowanie konfiguracji usługi pulpitu zdalnego**
Aby zresetować konfigurację usługi pulpitu zdalnego, uruchom następujące polecenie:

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

Rozszerzenie VMAccess wykonuje dwa polecenia na maszynie wirtualnej:

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

To polecenie włącza wbudowanej grupy Zapory Windows, która zezwala na ruch przychodzący pulpitu zdalnego, który używa portu TCP 3389.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

To polecenie ustawia fDenyTSConnections wartość rejestru na 0, włączenie połączeń pulpitu zdalnego.

## <a name="next-steps"></a>Kolejne kroki
Jeśli rozszerzenie dostępu do maszyny Wirtualnej platformy Azure nie odpowiada i nie można zresetować hasła, możesz to zrobić [Resetowanie hasła lokalnego Windows w trybie offline](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ta metoda jest bardziej zaawansowany proces i wymaga podłączenia wirtualnego dysku twardego problematyczne maszyny wirtualnej do innej maszyny Wirtualnej. Wykonaj kroki opisane w tym artykule najpierw, a tylko próba metoda resetowania hasła w trybie offline w ostateczności.

[Rozszerzenia maszyn wirtualnych platformy Azure i funkcji](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Nawiązać połączenie z maszyną wirtualną platformy Azure przy użyciu protokołu RDP lub SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx)

[Rozwiązywanie problemów z połączeniami pulpitu zdalnego z maszyną wirtualną na podstawie Windows Azure](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

