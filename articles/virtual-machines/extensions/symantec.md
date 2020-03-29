---
title: Instalowanie programu Symantec Endpoint Protection na maszynie Wirtualnej systemu Windows na platformie Azure
description: Dowiedz się, jak zainstalować i skonfigurować rozszerzenie zabezpieczeń programu Symantec Endpoint Protection na nowej lub istniejącej maszynie wirtualnej platformy Azure utworzonej za pomocą klasycznego modelu wdrażania.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 03/31/2017
ms.author: akjosh
ms.openlocfilehash: 63f9441d4df9551405c2ab2bf8c0c67d7de5753c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919910"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Jak zainstalować i skonfigurować program Symantec Endpoint Protection na maszynie Wirtualnej systemu Windows
[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Menedżer zasobów i Klasyczny](../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano przy użyciu modelu wdrażania klasycznego. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

W tym artykule pokazano, jak zainstalować i skonfigurować klienta programu Symantec Endpoint Protection na istniejącej maszynie wirtualnej z systemem Windows Server. Ten pełny klient obejmuje usługi, takie jak ochrona przed wirusami i programami szpiegującymi, zapora i zapobieganie włamaniom. Klient jest instalowany jako rozszerzenie zabezpieczeń przy użyciu agenta maszyny Wirtualnej.

Jeśli masz istniejącą subskrypcję firmy Symantec dla rozwiązania lokalnego, możesz jej użyć do ochrony maszyn wirtualnych platformy Azure. Jeśli nie jesteś jeszcze klientem, możesz zarejestrować się w subskrypcji próbnej. Aby uzyskać więcej informacji na temat tego rozwiązania, zobacz [Symantec Endpoint Protection na platformie Azure firmy Microsoft.][Symantec] Na tej stronie znajdują się również łącza do informacji licencyjnych i instrukcji instalacji klienta, jeśli jesteś już klientem firmy Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Instalowanie programu Symantec Endpoint Protection na istniejącej maszynie wirtualnej
Przed rozpoczęciem potrzebne są następujące elementy:

* Moduł programu Azure PowerShell w wersji 0.8.2 lub nowszej na komputerze służbowym. Można sprawdzić wersję programu Azure PowerShell, który został zainstalowany za pomocą **polecenia Get-Module azure | format-table.** Aby uzyskać instrukcje i łącze do najnowszej wersji, zobacz [Jak zainstalować i skonfigurować program Azure PowerShell][PS]. Zaloguj się do subskrypcji `Add-AzureAccount`platformy Azure przy użyciu programu .
* Agent maszyny wirtualnej uruchomiony na maszynie wirtualnej platformy Azure.

Najpierw sprawdź, czy agent maszyny Wirtualnej jest już zainstalowany na maszynie wirtualnej. Wprowadź nazwę usługi w chmurze i nazwę maszyny wirtualnej, a następnie uruchom następujące polecenia w wierszu polecenia programu Azure PowerShell na poziomie administratora. Zastąp wszystko w cudzysłowie, w tym znaki < i >.

> [!TIP]
> Jeśli nie znasz nazwy usługi w chmurze i maszyn wirtualnych, uruchom **get-AzureVM,** aby wyświetlić listę nazw wszystkich maszyn wirtualnych w bieżącej subskrypcji.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Jeśli polecenie **hosta zapisu** wyświetla **true,** agent maszyny Wirtualnej jest zainstalowany. Jeśli wyświetla **false**, zobacz instrukcje i link do pobrania w blogu azure [Agent maszyny Wirtualnej i rozszerzeń - część 2][Agent].

Jeśli agent maszyny Wirtualnej jest zainstalowany, uruchom te polecenia, aby zainstalować agenta ochrony punktu końcowego firmy Symantec.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Aby sprawdzić, czy rozszerzenie zabezpieczeń firmy Symantec zostało zainstalowane i jest aktualne:

1. Zaloguj się do maszyny wirtualnej. Aby uzyskać instrukcje, zobacz [Jak zalogować się do maszyny wirtualnej z systemem Windows Server][Logon].
2. W przypadku systemu Windows Server 2008 R2 kliknij przycisk **Rozpocznij > programem Symantec Endpoint Protection**. W przypadku systemu Windows Server 2012 lub Windows Server 2012 R2 na ekranie startowym wpisz polecenie **Symantec**, a następnie kliknij przycisk **Ochrona punktu końcowego firmy Symantec**.
3. Na karcie **Stan** okna **Ochrona punktu końcowego stanu Symantec** zastosuj aktualizacje lub uruchom ponownie, jeśli to konieczne.

## <a name="additional-resources"></a>Zasoby dodatkowe
[Jak zalogować się do maszyny wirtualnej z systemem Windows Server][Logon]

[Rozszerzenia i funkcje maszyn wirtualnych platformy Azure][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: https://go.microsoft.com/fwlink/p/?linkid=390493
