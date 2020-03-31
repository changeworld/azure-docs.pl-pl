---
title: Rozszerzenia i funkcje maszyn wirtualnych platformy Azure
description: Dowiedz się, czym są rozszerzenia maszyn wirtualnych platformy Azure i jak z nich korzystać z maszyn wirtualnych platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/12/2019
ms.author: akjosh
ms.openlocfilehash: bf17f499c1e8339a1e9abb13cffd5e35c390f564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74072973"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Rozszerzenia i funkcje maszyn wirtualnych platformy Azure
Rozszerzenia maszyny wirtualnej platformy Azure to małe aplikacje, które zapewniają zadania konfiguracji i automatyzacji po wdrożeniu na maszynach wirtualnych platformy Azure, można użyć istniejących obrazów, a następnie dostosować je w ramach wdrożeń, co pozwala ci wyjść z działalności niestandardowej budynku obrazu.

Platforma Azure obsługuje wiele rozszerzeń, które obejmują konfigurację maszyn wirtualnych, monitorowanie, zabezpieczenia i aplikacje narzędziowe. Wydawcy przyjmują aplikację, a następnie zawijają ją w rozszerzenie i upraszczają instalację, więc wszystko, co musisz zrobić, to podać obowiązkowe parametry. 

 Istnieje duży wybór rozszerzeń pierwszej i innej firmy, jeśli aplikacja w repozytorium rozszerzeń nie istnieje, można użyć rozszerzenia skrypt niestandardowy i skonfigurować maszynę wirtualną za pomocą własnych skryptów i poleceń.

Przykłady kluczowych scenariuszy, dla których używane są rozszerzenia:
* Konfiguracji maszyny Wirtualnej, można użyć powershell DSC (konfiguracja żądanego stanu), chef, marionetkowe i niestandardowe rozszerzenia skryptów, aby zainstalować agentów konfiguracji maszyny Wirtualnej i skonfigurować maszynę wirtualną. 
* produktów AV, takich jak Symantec, ESET.
* Narzędzie do luk w zabezpieczeniach maszyn wirtualnych, takie jak Qualys, Rapid7, HPE.
* Narzędzia do monitorowania maszyn wirtualnych i aplikacji, takie jak DynaTrace, Azure Network Watcher, Site24x7 i Stackify.

Rozszerzenia mogą być dołączone do nowego wdrożenia maszyny Wirtualnej. Na przykład mogą one być częścią większego wdrożenia, konfigurowanie aplikacji na aprowizowaniu maszyn wirtualnych lub uruchamiane z dowolnymi obsługiwanymi systemami obsługiwanymi przez rozszerzenie po wdrożeniu.

## <a name="how-can-i-find-what-extensions-are-available"></a>Jak znaleźć dostępne rozszerzenia?
Dostępne rozszerzenia można wyświetlić w bloku maszyn wirtualnych w portalu, w obszarze rozszerzenia, stanowi to tylko niewielką ilość, aby uzyskać pełną listę, można użyć narzędzi interfejsu wiersza polecenia, zobacz [Odnajdowanie rozszerzeń maszyn wirtualnych dla systemu Linux](features-linux.md) i [odnajdowanie rozszerzeń maszyn wirtualnych dla systemu Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Jak zainstalować rozszerzenie?
Rozszerzenia maszyn wirtualnych platformy Azure można zarządzać za pomocą interfejsu wiersza polecenia platformy Azure, usługi Azure PowerShell, szablonów usługi Azure Resource Manager i witryny Azure portal. Aby wypróbować rozszerzenie, możesz przejść do witryny Azure portal, wybrać rozszerzenie skryptu niestandardowego, a następnie przekazać polecenie / skrypt i uruchomić rozszerzenia.

Jeśli chcesz dodać to samo rozszerzenie w portalu przez szablon interfejsu wiersza polecenia lub Menedżera zasobów, zobacz inną dokumentację rozszerzenia, taką jak [Niestandardowe rozszerzenie skryptu systemu Windows](custom-script-windows.md) i [niestandardowe rozszerzenie skryptu systemu Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Jak zarządzać cyklem życia aplikacji rozszerzania?
Nie trzeba łączyć się bezpośrednio z maszyną wirtualną, aby zainstalować lub usunąć rozszerzenie. Ponieważ cykl życia aplikacji rozszerzenia platformy Azure jest zarządzany poza maszyną wirtualną i zintegrowany z platformą Azure, otrzymujesz również zintegrowany stan rozszerzenia.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Coś jeszcze powinienem myśleć o rozszerzeniach?
Rozszerzenia zainstalować aplikacje, podobnie jak wszystkie aplikacje istnieją pewne wymagania, dla rozszerzeń istnieje lista obsługiwanych systemów operacyjnych Windows i Linux i trzeba mieć agentów maszyn wirtualnych platformy Azure zainstalowany. Niektóre poszczególne aplikacje rozszerzenia maszyny Wirtualnej mogą mieć własne wymagania środowiskowe, takie jak dostęp do punktu końcowego.

## <a name="troubleshoot-extensions"></a>Rozwiązywanie problemów z rozszerzeniami

Informacje dotyczące rozwiązywania problemów z każdym rozszerzeniem można znaleźć w sekcji **Rozwiązywanie problemów i pomoc techniczna** w omówieniu rozszerzenia. Oto lista dostępnych informacji dotyczących rozwiązywania problemów:

| Przestrzeń nazw | Rozwiązywanie problemów |
|-----------|-----------------|
| microsoft.azure.monitoring.dependencyagent.dependencyagentlinux | [Zależność usługi Azure Monitor dla systemu Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| microsoft.azure.monitoring.dependencyagent.dependencyagentwindows | [Zależność monitora platformy Azure dla systemu Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryptionforlinux | [Szyfrowanie dysków platformy Azure dla systemu Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryption | [Szyfrowanie dysków platformy Azure dla systemu Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft.compute.customscriptextension | [Skrypt niestandardowy dla systemu Windows](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux | [Konfiguracja żądanego stanu dla systemu Linux](dsc-linux.md#troubleshoot-and-support) |
| plik microsoft.powershell.dsc | [Konfiguracja żądanego stanu dla systemu Windows](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [Rozszerzenie sterownika GPU NVIDIA dla systemu Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows | [Rozszerzenie sterownika procesora graficznego NVIDIA dla systemu Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [Rozszerzenie ochrony przed złośliwym oprogramowaniem dla systemu Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [Usługa Azure Monitor dla systemu Linux](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring.microsoftmonitoringagent | [Monitor Azure dla systemu Windows](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension | [Stackify Retrace dla systemu Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux.microsoft.ostcextensions | [Resetowanie hasła (VMAccess) dla systemu Linux](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Migawka dla systemu Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Migawka dla systemu Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Następne kroki
* Aby uzyskać więcej informacji na temat działania agenta i rozszerzeń systemu Linux, zobacz [rozszerzenia i funkcje maszyn wirtualnych platformy Azure dla systemu Linux](features-linux.md).
* Aby uzyskać więcej informacji na temat działania agenta i rozszerzeń gościa systemu Windows, zobacz [rozszerzenia i funkcje maszyn wirtualnych platformy Azure dla systemu Windows](features-windows.md).  
* Aby zainstalować Agenta gościa systemu Windows, zobacz [Omówienie agenta maszyny wirtualnej systemu Azure Systemu Windows](agent-windows.md).  
* Aby zainstalować agenta systemu Linux, zobacz [Omówienie agenta maszyny wirtualnej systemu Azure Linux](agent-linux.md).  

