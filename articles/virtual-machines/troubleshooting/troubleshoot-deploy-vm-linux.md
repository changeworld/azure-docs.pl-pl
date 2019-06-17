---
title: Rozwiązywanie problemów z wdrażanie problemy z maszyny wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Wdrażanie systemu Linux maszyny wirtualnej rozwiązywania problemów z w modelu wdrażania usługi Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 1317a4731d3598c5fba317167ba4a45d95823ca2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60593272"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Rozwiązywanie problemów z wdrażanie problemy z maszyny wirtualnej systemu Linux na platformie Azure

Aby rozwiązać problemy z wdrażaniem maszynę wirtualną (VM) na platformie Azure, zapoznaj się z [Najważniejsze problemy](#top-issues) typowe błędy i ich rozwiązania.

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.

## <a name="top-issues"></a>Najczęstsze problemy
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Klaster nie obsługuje żądanego rozmiaru maszyny Wirtualnej
\<properties supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Ponów żądanie, używając mniejszego rozmiaru maszyny Wirtualnej.
- Jeśli nie można zmienić rozmiar żądanej maszyny Wirtualnej:
    - Zatrzymaj wszystkie maszyny wirtualne w zestawie dostępności. Kliknij przycisk **grup zasobów** > Grupa zasobów > **zasobów** > zestawie dostępności > **maszyn wirtualnych** > Maszyna wirtualna >  **Zatrzymaj**.
    - Po zatrzymania wszystkich maszyn wirtualnych, należy utworzyć maszynę Wirtualną w żądany rozmiar.
    - Najpierw należy uruchomić nową maszynę Wirtualną i wybierz poszczególne zatrzymanych maszyn wirtualnych, a następnie kliknij przycisk Uruchom.


## <a name="the-cluster-does-not-have-free-resources"></a>Klaster ma bezpłatne zasoby
\<properties supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Ponów żądanie później.
- Jeśli nowa maszyna wirtualna może być częścią zestawu dostępności różnych
    - Utwórz Maszynę wirtualną w innej zestaw dostępności (w tym samym regionie).
    - Dodaj nową maszynę Wirtualną do tej samej sieci wirtualnej.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Jak uaktywnić miesięcznych środków dla programu Visual studio Enterprise (BizSpark)

Aby aktywować swoje miesięczne środki, zobacz ten [artykułu](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Dlaczego I nie można zainstalować sterownik procesora GPU dla maszyny Wirtualnej systemu Ubuntu NV?

Obecnie wsparciem procesora GPU w systemie Linux jest dostępna tylko na maszynach wirtualnych kontrolera sieci Azure systemem Ubuntu Server 16.04 LTS. Aby uzyskać więcej informacji, zobacz [skonfigurować sterowniki procesora GPU dla maszyn wirtualnych serii N z systemem Linux](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Moje sterowniki są spełnione dla moich maszyn wirtualnych serii N Linux

Sterowniki dla maszyn wirtualnych opartych na systemie Linux znajdują się [tutaj](../linux/n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nie można znaleźć wystąpienia procesora GPU w moich maszyn wirtualnych serii N

Aby skorzystać z możliwości procesora GPU z serii N maszyn wirtualnych platformy Azure z systemu Windows Server 2016 lub Windows Server 2012 R2, należy zainstalować sterowniki grafiki NVIDIA na każdej maszynie Wirtualnej po wdrożeniu. Informacje o konfiguracji sterownika jest dostępna dla [maszyn wirtualnych Windows](../windows/n-series-driver-setup.md) i [maszyn wirtualnych systemu Linux](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Maszyny wirtualne z serii N jest dostępna w moim regionie?

Możesz sprawdzić dostępność z [dostępność produktów według regionów tabeli](https://azure.microsoft.com/regions/services)i ceny [tutaj](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nie mogę wyświetlić rodziny rozmiaru maszyny Wirtualnej, która ma podczas zmiany rozmiaru Moja maszyna wirtualna.

Gdy maszyna wirtualna jest uruchomiona, jest wdrażana serwera fizycznego. Serwerów fizycznych w regionach platformy Azure są grupowane w klastrach typowego sprzętu fizycznego. Zmiana rozmiaru maszyny Wirtualnej, która wymaga maszyny Wirtualnej zostanie przeniesiony na inny sprzęt w klastrach różni się w zależności od modelu wdrażania, który został użyty do wdrożenia tej maszyny Wirtualnej.

- Maszyny wirtualne wdrożone w klasycznym modelu wdrażania przy użyciu wdrażania usługi w chmurze, należy usunąć i ponownie wdrożyć, aby zmienić rozmiar w innej rodziny rozmiaru maszyny wirtualne.

- Maszyn wirtualnych wdrożonych w modelu wdrażania usługi Resource Manager, należy zatrzymać wszystkie maszyny wirtualne w zestawie przed zmianą rozmiaru każdej maszyny Wirtualnej w zestawie dostępności dostępności.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Uwzględnione na liście rozmiar maszyny Wirtualnej nie jest obsługiwany podczas wdrażania w zestawie dostępności.

Wybierz rozmiar, który jest obsługiwany na zestaw dostępności klastra. Zalecane jest, gdy tworzenie dostępności zestawu, aby wybrać największy rozmiar maszyny Wirtualnej, o jakich myślisz, gdy potrzebujesz i być pierwszego wdrożenia do zestawu dostępności.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Jakie są dystrybucje systemu Linux/wersje są obsługiwane na platformie Azure?

Listy w systemie Linux można znaleźć na [dystrybucje Azure-Endorsed](../linux/endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Do zestawu dostępności można dodać istniejącej klasycznej maszyny Wirtualnej?

Tak. Możesz dodać istniejące klasycznej maszyny Wirtualnej do nowego lub istniejącego zestawu dostępności. Aby uzyskać więcej informacji, zobacz [Dodaj istniejącą maszynę wirtualną do zestawu dostępności](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Kolejne kroki
Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.
