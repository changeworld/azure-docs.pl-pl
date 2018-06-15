---
title: Instalowanie programu Symantec Endpoint Protection w systemie Windows maszyny Wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie instalowania i konfigurowania rozszerzeń zabezpieczeń firmy Symantec Endpoint Protection w nowej lub istniejącej maszyny Wirtualnej platformy Azure utworzonych za pomocą klasycznego modelu wdrożenia.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 54ce769d9442a4c95ad59875097893dc0b89a004
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942669"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Jak zainstalować i skonfigurować rozwiązanie Symantec Endpoint Protection na maszynie wirtualnej systemu Windows
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

W tym artykule przedstawiono sposób instalowania i konfigurowania klienta programu Symantec Endpoint Protection na istniejącej maszyny wirtualnej (VM) system operacyjny Windows Server. Ta pełnego klienta zawiera usług, takich jak wirusów i ochrony przed programami szpiegującymi, zapory i zapobiegania nieautoryzowanego dostępu. Klient jest instalowany jako rozszerzenia zabezpieczeń przy użyciu agenta maszyny Wirtualnej.

Jeśli masz istniejącą subskrypcję od firmy Symantec dla rozwiązania lokalnego, można użyć go do ochrony maszyn wirtualnych platformy Azure. Jeśli nie jesteś klientem jeszcze, można założyć dla subskrypcji wersji próbnej. Aby uzyskać więcej informacji o tym rozwiązaniu, zobacz [Symantec Endpoint Protection na platformie Azure przez firmę Microsoft][Symantec]. Ta strona zawiera również linki do informacji dotyczących licencjonowania i instrukcje dotyczące instalowania klienta, jeśli już masz klienta firmy Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Zainstaluj Symantec Endpoint Protection na istniejącej maszyny Wirtualnej
Przed rozpoczęciem, potrzebne są następujące elementy:

* Moduł Azure PowerShell, wersja 0.8.2 lub nowszym, na komputerze pracy. Można sprawdzić wersji programu Azure PowerShell, który został zainstalowany z **azure Get-Module | wersja formatu tabeli** polecenia. Aby uzyskać instrukcje i łącza do najnowszej wersji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell][PS]. Zaloguj się przy użyciu subskrypcji platformy Azure `Add-AzureAccount`.
* Agent maszyny Wirtualnej uruchomione na maszynie wirtualnej platformy Azure.

Po pierwsze sprawdzenie, czy Agent maszyny Wirtualnej jest już zainstalowany na maszynie wirtualnej. Wypełnij pola Nazwa usługi w chmurze i Nazwa maszyny wirtualnej, a następnie uruchom następujące polecenia w wierszu polecenia programu Azure PowerShell poziomie administratora. Zastąp wszystko w obrębie oferty, w tym < i > znaków.

> [!TIP]
> Jeśli nie znasz nazwy maszyny wirtualnej i usługi w chmurze, uruchom **Get AzureVM** Aby wyświetlić listę nazw dla wszystkich maszyn wirtualnych w ramach Twojej bieżącej subskrypcji.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Jeśli **hosta zapisu** polecenie wyświetla **True**, Agent maszyny Wirtualnej jest zainstalowany. Jeśli Wyświetla **False**, zobacz instrukcje i łącze do pobrania w Azure blogu [agenta maszyny Wirtualnej i rozszerzenia — część 2][Agent].

Jeśli jest zainstalowany Agent maszyny Wirtualnej, uruchom następujące polecenia, aby zainstalować agenta programu Symantec Endpoint Protection.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Aby sprawdzić, czy rozszerzenie zabezpieczeń firmy Symantec został zainstalowany i jest aktualny:

1. Zaloguj się do maszyny wirtualnej. Aby uzyskać instrukcje, zobacz [jak Zaloguj się do maszyny wirtualnej systemem Windows Server][Logon].
2. Dla systemu Windows Server 2008 R2, kliknij przycisk **Start > Symantec Endpoint Protection**. Dla systemu Windows Server 2012 lub Windows Server 2012 R2, na ekranie startowym wpisz **firmy Symantec**, a następnie kliknij przycisk **Symantec Endpoint Protection**.
3. Z **stan** karcie **stan Symantec Endpoint Protection** okna, stosowania aktualizacji lub uruchom ponownie w razie potrzeby.

## <a name="additional-resources"></a>Zasoby dodatkowe
[Jak Zaloguj się do maszyny wirtualnej z systemem Windows Server][Logon]

[Maszyna wirtualna platformy Azure rozszerzeń i funkcji][Ext]

<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493
