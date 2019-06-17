---
title: Instalowanie aplikacji Symantec Endpoint Protection na Windows maszyny Wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować i skonfigurować rozwiązanie Symantec Endpoint Protection rozszerzenie zabezpieczeń na nowej lub istniejącej maszyny Wirtualnej platformy Azure utworzonych za pomocą klasycznego modelu wdrażania.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: roiyz
ms.openlocfilehash: 65b52c88741e618e8048451370918b06db73a651
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60617881"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Jak zainstalować i skonfigurować rozwiązanie Symantec Endpoint Protection na maszynie wirtualnej systemu Windows
> [!IMPORTANT] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługi Resource Manager i Model Klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Ten artykuł pokazuje, jak zainstalować i skonfigurować klienta programu Symantec Endpoint Protection na istniejącej maszyny wirtualnej (VM) systemem Windows Server. Ta pełnego klienta obejmuje usługi, takie jak przed wirusami i ochrony przed programami szpiegującymi, zapory i zapobiegania nieautoryzowanego dostępu. Klient jest zainstalowany jako rozszerzenie zabezpieczeń przy użyciu agenta maszyny Wirtualnej.

Jeśli masz istniejącą subskrypcję od firmy Symantec dla rozwiązania w środowisku lokalnym, można użyć go do ochrony maszyn wirtualnych platformy Azure. Jeśli nie jesteś klientem jeszcze, możesz zarejestrować się w przypadku subskrypcji wersji próbnej. Aby uzyskać więcej informacji na temat tego rozwiązania, zobacz [rozwiązanie Symantec Endpoint Protection na platformie Azure firmy Microsoft][Symantec]. Ta strona zawiera także łącza do informacji dotyczących licencjonowania i instrukcje dotyczące instalowania klienta, jeśli jesteś klientem firmy Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Zainstaluj rozwiązanie Symantec Endpoint Protection na istniejącej maszyny Wirtualnej
Przed rozpoczęciem potrzebne są następujące elementy:

* Moduł Azure PowerShell, wersja 0.8.2 lub nowszych wersjach na komputerze służbowym. Możesz sprawdzić wersję programu Azure PowerShell, który został zainstalowany przy użyciu **Get-Module azure | format-table wersji** polecenia. Aby uzyskać instrukcje i łącza do najnowszej wersji, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell][PS]. Zaloguj się do swojej subskrypcji platformy Azure za pomocą `Add-AzureAccount`.
* Agent maszyny Wirtualnej, systemem maszyny wirtualnej platformy Azure.

Po pierwsze sprawdzenie, czy Agent maszyny Wirtualnej jest już zainstalowane na maszynie wirtualnej. Wypełnij pola nazwy usługi w chmurze i Nazwa maszyny wirtualnej, a następnie uruchom następujące polecenia w wierszu polecenia programu Azure PowerShell z poziomu administratora. Zastąp wszystkie elementy w ramach oferty, w tym < a > znaków.

> [!TIP]
> Jeśli nie znasz nazwy maszyny wirtualnej i usługi w chmurze, uruchom **Get-AzureVM** listę nazw wszystkich maszyn wirtualnych w Twojej bieżącej subskrypcji.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Jeśli **zapisu hosta** polecenie wyświetla **True**, jest zainstalowany Agent maszyny Wirtualnej. Jeśli zostanie wyświetlony **False**, zobacz instrukcje i linkiem umożliwiającym pobranie wpis w blogu platformy Azure [VM Agent i rozszerzenia — część 2][Agent].

Jeśli jest zainstalowany Agent maszyny Wirtualnej, uruchom następujące polecenia, aby zainstalować agenta programu Symantec Endpoint Protection.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Aby sprawdzić, czy rozszerzenie zabezpieczeń firmy Symantec został zainstalowany i jest aktualne:

1. Zaloguj się do maszyny wirtualnej. Aby uzyskać instrukcje, zobacz [jak logować się do maszyny wirtualnej systemem Windows Server][Logon].
2. Dla systemu Windows Server 2008 R2, kliknij przycisk **Start > rozwiązanie Symantec Endpoint Protection**. Dla systemu Windows Server 2012 lub Windows Server 2012 R2, na ekranie startowym wpisz **Symantec**, a następnie kliknij przycisk **rozwiązanie Symantec Endpoint Protection**.
3. Z **stan** karcie **stan Symantec Endpoint Protection** okna, zastosować aktualizacje lub ponowne uruchomienie w razie potrzeby.

## <a name="additional-resources"></a>Dodatkowe zasoby
[Jak logować się do maszyny wirtualnej z systemem Windows Server][Logon]

[Rozszerzenia maszyny Wirtualnej platformy Azure i funkcji][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: https://go.microsoft.com/fwlink/p/?linkid=390493
