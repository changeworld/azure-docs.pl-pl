---
title: Zdalne zarządzanie systemem Windows za pośrednictwem protokołu HTTPS dla platformy Azure | Azure Marketplace
description: W tym artykule wyjaśniono, jak skonfigurować maszynę wirtualną hostowane na platformie Azure, opartą na systemie Windows, tak aby można było nią zarządzać zdalnie za pomocą programu PowerShell.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dsindona
ms.openlocfilehash: b2a4bb107309894a7180e0a4585cdba6f04d1bee
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273039"
---
# <a name="windows-remote-management-over-https"></a>Zdalne zarządzanie systemem Windows przez protokół HTTPS

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami maszyny wirtualnej platformy Azure do centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [tworzenie oferty maszyny wirtualnej platformy Azure,](https://aka.ms/CreateAzureVMoffer) aby zarządzać zmigrowanymi ofertami.

W tej sekcji wyjaśniono, jak skonfigurować maszynę wirtualną hostowane na platformie Azure, opartą na systemie Windows, tak aby można ją było zarządzać i wdrażać zdalnie za pomocą programu PowerShell.  Aby włączyć zdalne sterowanie programem PowerShell, docelowa maszyna wirtualna musi udostępnić punkt końcowy HTTPS https zdalnego zarządzania systemem Windows (WinRM).  Aby uzyskać więcej informacji na temat komunikacji zdalnej programu PowerShell, zobacz [Uruchamianie poleceń zdalnych](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands).  Aby uzyskać więcej informacji na temat usługi WinRM, zobacz [Zdalne zarządzanie systemem Windows](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Jeśli utworzono maszynę wirtualną przy użyciu jednego z "klasycznych" metod platformy Azure — portalu usługi Azure Service Manager lub przestarzałego [interfejsu API usługi Azure Service Management](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))— jest ona automatycznie konfigurowana przy użyciu punktu końcowego usługi WinRM.  Jednak jeśli utworzysz maszynę wirtualną przy użyciu dowolnego z następujących "nowoczesnych" metod platformy Azure, maszyna wirtualna *nie* zostanie skonfigurowana dla usługi WinRM za pośrednictwem protokołu HTTPS.

- Korzystanie z [witryny Azure portal](https://portal.azure.com/), zazwyczaj z zatwierdzonej bazy, zgodnie z opisem w sekcji [Tworzenie dysku VHD zgodnego z platformą Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Korzystanie z szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Przy użyciu powłoki poleceń programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.  Zobacz przykłady szybki [start: tworzenie maszyny wirtualnej systemu Windows na platformie Azure za pomocą programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) i [przewodnika Szybki start: tworzenie maszyny wirtualnej systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Ten punkt końcowy usługi WinRM jest również wymagany do uruchomienia zestawu narzędzi certyfikacji do dołączania maszyny Wirtualnej, zgodnie z opisem w [certyfikuj obraz maszyny Wirtualnej.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm)

Z kolei zazwyczaj maszyny wirtualne z systemem Linux są zarządzane zdalnie przy użyciu poleceń [interfejsu wiersza polecenia platformy Azure lub](https://docs.microsoft.com/cli/azure) systemu Linux z konsoli SSH.  Platforma Azure udostępnia również kilka alternatywnych metod [uruchamiania skryptów na maszynie Wirtualnej systemu Linux.](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm)  W przypadku bardziej złożonych scenariuszy dostępnych jest wiele rozwiązań automatyzacji i integracji dla maszyn wirtualnych z systemem Windows lub Linux.


## <a name="configure-and-deploy-with-winrm"></a>Konfigurowanie i wdrażanie za pomocą usługi WinRM

Punkt końcowy usługi WinRM dla maszyny wirtualnej opartej na systemie Windows można skonfigurować na dwóch różnych etapach jej rozwoju:

- Podczas tworzenia — podczas wdrażania maszyny Wirtualnej do istniejącego dysku wirtualnego.  Jest to preferowane podejście do nowych ofert.  Takie podejście wymaga utworzenia certyfikatu platformy Azure przy użyciu dostarczonych szablonów usługi Azure Resource Manager i uruchamiania niestandardowych skryptów programu PowerShell.
- Po wdrożeniu — na istniejącej maszynie wirtualnej hostowanej na platformie Azure.  Użyj tej metody, jeśli masz już rozwiązanie maszyny Wirtualnej wdrożone na platformie Azure i musisz włączyć dla niego zdalne zarządzanie oknami.  Takie podejście wymaga ręcznych zmian w witrynie Azure portal i wykonywania skryptu na docelowej maszynie Wirtualnej.


## <a name="next-steps"></a>Następne kroki
Jeśli tworzysz nową maszynę wirtualną, możesz włączyć usługę WinRM podczas [wdrażania maszyny wirtualnej z jej wirtualnych maszyn wirtualnych.](./cpp-deploy-vm-vhd.md)  W przeciwnym razie program WinRM można włączyć na istniejącej maszynie wirtualnej
