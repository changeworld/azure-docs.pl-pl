---
title: Windows zdalne zarządzanie przy użyciu protokołu HTTPS dla platformy Azure | Dokumentacja firmy Microsoft
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: pbutlerm
ms.openlocfilehash: 99c41dc010ae589cc43c093646fd1c05c1333f7e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60342818"
---
# <a name="windows-remote-management-over-https"></a>Windows zdalne zarządzanie przy użyciu protokołu HTTPS

W tej sekcji opisano sposób konfigurowania maszyn wirtualnych hostowanych na platformie Azure, systemem Windows, dzięki czemu mogą być zarządzane i wdrożony zdalnie przy użyciu programu PowerShell.  Aby włączyć komunikację zdalną programu PowerShell, docelowa maszyna wirtualna musi ujawniać punkt końcowy HTTPS Windows Remote Management (WinRM).  Aby uzyskać więcej informacji na temat komunikacji zdalnej programu PowerShell, zobacz [uruchamianie poleceń zdalnych](https://docs.microsoft.com/powershell/scripting/core-powershell/running-remote-commands?view=powershell-6).  Aby uzyskać więcej informacji dotyczących usługi WinRM, zobacz [Windows Remote Management](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Jeśli utworzono Maszynę wirtualną przy użyciu jednej z metod "klasyczny" platformy Azure — Azure Portal programu Service Manager lub przestarzałe [interfejsu API zarządzania usługami Azure](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))—, a następnie jest automatycznie konfigurowany z punktem końcowym usługi WinRM.  Jednak jeśli tworzysz Maszynę wirtualną podejścia "nowoczesnych" Azure przy użyciu dowolnej z następujących czynności, a następnie maszyna wirtualna będzie *nie* można skonfigurować w przypadku usługi WinRM przy użyciu protokołu HTTPS.  

- Za pomocą [witryny Azure portal](https://portal.azure.com/), zazwyczaj z zatwierdzonych base zgodnie z opisem w sekcji [utworzyć wirtualny dysk twardy zgodnych z platformą Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Za pomocą szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Przy użyciu powłoki poleceń programu Azure PowerShell lub wiersza polecenia platformy Azure.  Aby uzyskać przykłady, zobacz [Szybki Start: Utwórz maszynę wirtualną Windows na platformie Azure przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) i [Szybki Start: Utwórz maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Ten punkt końcowy usługi WinRM jest również wymagane do uruchomienia zestawu narzędzie certyfikacji do dołączenia do maszyny Wirtualnej, zgodnie z opisem w [certyfikować swój obraz maszyny Wirtualnej](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Z kolei zwykle maszyn wirtualnych systemu Linux zdalnie odbywa się za pomocą [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure) lub polecenia systemu Linux z poziomu konsoli SSH.  Platforma Azure udostępnia kilka metod alternatywnych [uruchamianie skryptów w maszynie Wirtualnej systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  W przypadku bardziej złożonych scenariuszy istnieje szereg rozwiązań usługi automation i integracji dostępne dla maszyn wirtualnych z systemem Windows lub Linux.


## <a name="configure-and-deploy-with-winrm"></a>Konfigurowanie i wdrażanie za pomocą usługi WinRM

Punkt końcowy usługi WinRM dla maszyn wirtualnych opartych na systemie windows można skonfigurować w dwóch różnych etapach projektowania:

- Podczas tworzenia — podczas wdrażania maszyny Wirtualnej do istniejącego wirtualnego dysku twardego.  Jest to preferowana metoda dla nowych ofert.  Takie podejście wymaga utworzenia certyfikatu platformy Azure przy użyciu podanej szablonów usługi Azure Resource Manager, i uruchamianie dostosowane skrypty programu PowerShell. 
- Po wdrożeniu - istniejącej maszyny wirtualnej hostowanej na platformie Azure.  Tej metody należy użyć, jeśli już masz rozwiązanie maszyn wirtualnych wdrożonych na platformie Azure, a konieczne włączenie zdalnego zarządzania systemem Windows dla niego.  Takie podejście wymaga ręcznego wprowadzenia zmian w witrynie Azure portal i wykonywanie skryptu na docelowej maszynie Wirtualnej. 


## <a name="next-steps"></a>Kolejne kroki
Jeśli tworzysz nową maszynę Wirtualną można włączyć usługę WinRM podczas [wdrożenia maszyny wirtualnej z jej wirtualne dyski twarde](./cpp-deploy-vm-vhd.md).  W przeciwnym razie być włączona usługa WinRM w istniejącej maszyny Wirtualnej  
