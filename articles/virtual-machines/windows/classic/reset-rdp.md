---
title: Resetowanie hasła lub w konfiguracji pulpitu zdalnego na maszynie Wirtualnej systemu Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można zresetować hasło do konta lub usług pulpitu zdalnego na maszynie Wirtualnej Windows utworzone przy użyciu klasycznego modelu wdrażania przy użyciu portalu Azure lub programu Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 516cb740f9acad19dac77db0239341b42a2b27fe
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>Jak można zresetować usług pulpitu zdalnego lub jego hasło logowania w utworzonych przy użyciu klasycznego modelu wdrożenia maszyny Wirtualnej systemu Windows
> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Możesz również [wykonaj te kroki dla maszyn wirtualnych utworzonych za pomocą modelu wdrażania usługi Resource Manager](../reset-rdp.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]


Jeśli nie można połączyć z maszyną wirtualną (VM) systemu Windows, można zresetować hasła administratora lokalnego lub zresetowanie konfiguracji usługi pulpitu zdalnego. Aby zresetować hasło, można użyć portalu Azure lub rozszerzenia dostępu do maszyny Wirtualnej w programie Azure PowerShell.

## <a name="ways-to-reset-configuration-or-credentials"></a>Sposoby resetowania konfiguracji lub poświadczeń
Można zresetować usług pulpitu zdalnego i poświadczenia na kilka różnych sposobów, w zależności od potrzeb:

- [Resetuj przy użyciu portalu Azure](#azure-portal)
- [Resetuj przy użyciu programu Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure Portal
Można użyć [portalu Azure](https://portal.azure.com) można zresetować usług pulpitu zdalnego. Aby rozwinąć menu portalu, kliknij trzy paski w lewym górnym rogu, a następnie kliknij **maszyn wirtualnych (klasyczne)**:

![Przeglądaj w poszukiwaniu Azure maszyny Wirtualnej](./media/reset-rdp/Portal-Select-Classic-VM.png)

Wybierz maszynę wirtualną systemu Windows, a następnie kliknij przycisk **zresetować zdalnego...** . Można zresetować konfiguracji pulpitu zdalnego zostanie wyświetlone następujące okno dialogowe:

![Strony konfiguracji RDP resetowania](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

Można również zresetować nazwę użytkownika i hasło konta administratora lokalnego. Z maszyny Wirtualnej, kliknij przycisk **pomocy technicznej i rozwiązywania problemów** > **resetowania hasła**. Zostanie wyświetlony blok resetowania hasła:

![Strona Resetowanie hasła](./media/reset-rdp/Portal-PW-Reset-Windows.png)

Po wprowadzeniu nową nazwę użytkownika i hasło, kliknij przycisk **zapisać**.

## <a name="vmaccess-extension-and-powershell"></a>Rozszerzenia VMAccess i programu PowerShell
Upewnij się, że Agent maszyny Wirtualnej jest zainstalowany na maszynie wirtualnej. Rozszerzenia VMAccess nie muszą być zainstalowane przed użyciem go, dopóki Agent maszyny Wirtualnej jest dostępna. Sprawdź, czy Agent maszyny Wirtualnej jest już zainstalowany za pomocą następującego polecenia. (Zamiast "myCloudService" i "myVM" według nazwy usługi w chmurze i maszyny Wirtualnej, odpowiednio. Dowiedz się te nazwy, uruchamiając `Get-AzureVM` bez żadnych parametrów.)

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

Jeśli **hosta zapisu** polecenie wyświetla **True**, Agent maszyny Wirtualnej jest zainstalowany. Jeśli Wyświetla **False**, zobacz instrukcje i łącze do pobrania w [agenta maszyny Wirtualnej i rozszerzenia — część 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) Azure wpis w blogu.

Jeśli utworzono maszynę wirtualną przy użyciu portalu, sprawdź, czy `$vm.GetInstance().ProvisionGuestAgent` zwraca **True**. Jeśli nie można ustawić za pomocą tego polecenia:

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

To polecenie uniemożliwia następujący błąd podczas pracy **AzureVMExtension zestaw** polecenia w następnych krokach: "Udostępniania Agent gościa musi być włączona obiektu maszyny Wirtualnej przed ustawieniem rozszerzenia dostępu do maszyn wirtualnych IaaS."

### <a name="reset-the-local-administrator-account-password"></a>**Zresetuj hasło konta administratora lokalnego**
Utwórz poświadczenia logowania z bieżącej nazwy konta administratora lokalnego i nowe hasło, a następnie uruchom `Set-AzureVMAccessExtension` w następujący sposób.

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

Jeśli wpiszesz nazwę inną niż bieżące konto rozszerzenia VMAccess zmienia nazwę konta administratora lokalnego, przypisuje hasło do tego konta i wystawia wylogowania pulpitu zdalnego. Wyłączenie konta administratora lokalnego rozszerzenia VMAccess włączy ją.

Te polecenia również zresetowanie konfiguracji usługi pulpitu zdalnego.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Zresetowanie konfiguracji usługi pulpitu zdalnego**
Aby zresetować konfigurację usługi pulpitu zdalnego, uruchom następujące polecenie:

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

Rozszerzenia VMAccess dwa polecenia są uruchamiane na maszynie wirtualnej:

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

To polecenie włącza wbudowanej grupy Zapory systemu Windows, który zezwala na ruch przychodzący pulpitu zdalnego, który korzysta z portu 3389 protokołu TCP.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

To polecenie ustawia fDenyTSConnections wartości rejestru na 0, włączenie połączeń pulpitu zdalnego.

## <a name="next-steps"></a>Kolejne kroki
Jeśli rozszerzenia dostępu do maszyny Wirtualnej platformy Azure nie odpowiada i nie można zresetować hasło, możesz [zresetować lokalne hasło systemu Windows w trybie offline](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ta metoda jest procesem bardziej zaawansowane i wymaga łączenie się inną maszynę Wirtualną z wirtualnego dysku twardego maszyny wirtualnej powodować problemy. Wykonaj kroki opisane w tym artykule najpierw, a następnie spróbuj tylko metody resetowania hasła w trybie offline w ostateczności.

[Rozszerzenia maszyny Wirtualnej platformy Azure i funkcje](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Połącz do maszyny wirtualnej platformy Azure z protokołu RDP lub SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Rozwiązywanie problemów z połączeniami pulpitu zdalnego do maszyny wirtualnej opartych na systemie Windows Azure](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

