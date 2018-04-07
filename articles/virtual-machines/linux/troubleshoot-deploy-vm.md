---
title: Rozwiązywanie problemów z wdrażanie problemy dotyczące maszyny wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z wdrażanie problemy dotyczące maszyny wirtualnej systemu Linux w modelu wdrażania Menedżera zasobów Azurethe.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 7e8db2683c301de8d41f7b7276e6290532e647d1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Rozwiązywanie problemów z wdrażanie problemy dotyczące maszyny wirtualnej systemu Linux na platformie Azure

Aby rozwiązać problemy z wdrażaniem maszyny wirtualnej (VM) na platformie Azure, przejrzyj [Najważniejsze problemy](#top-issues) dla typowych błędów i rozwiązania.

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskać obsługuje**.

## <a name="top-issues"></a>Najważniejsze problemy
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Klaster nie może obsługiwać żądany rozmiar maszyny Wirtualnej
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Ponów żądanie przy użyciu mniejszego rozmiaru maszyny Wirtualnej.
- Jeśli nie można zmienić rozmiar żądanej maszyny wirtualnej:
    - Zatrzymanie wszystkich maszyn wirtualnych w zestawie dostępności. Kliknij przycisk **grup zasobów** > grupy zasobów > **zasobów** > zestawu dostępności > **maszyn wirtualnych** > maszyny wirtualnej > **zatrzymać**.
    - Po zatrzymanie wszystkich maszyn wirtualnych, należy utworzyć maszynę Wirtualną w wymagany rozmiar.
    - Najpierw należy uruchomić nową maszynę Wirtualną i wybierz poszczególne zatrzymania maszyn wirtualnych, a następnie kliknij przycisk Uruchom.


## <a name="the-cluster-does-not-have-free-resources"></a>Klaster nie ma wolnego zasobów
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Ponów żądanie później.
- Jeśli nowa maszyna wirtualna może być częścią zestawu dostępności różnych
    - Utwórz maszynę Wirtualną w różnych dostępności, ustawić (w tym samym regionie).
    - Dodaj nową maszynę Wirtualną do tej samej sieci wirtualnej.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Jak aktywować Moje miesięczny kredyt dla programu Visual studio Enterprise (BizSpark)

Aby aktywować Twój miesięczny kredyt, zobacz [artykułu](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Dlaczego I nie można zainstalować sterownik procesora GPU dla maszyny Wirtualnej systemu Ubuntu wirtualizacją sieci?

Obecnie Obsługa Linux GPU jest dostępna tylko na maszynach wirtualnych NC Azure systemem Ubuntu Server 16.04 LTS. Aby uzyskać więcej informacji, zobacz [Konfigurowanie wersji sterowników procesora GPU dla maszyn wirtualnych N-series systemem Linux](n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Brak sterowników, dla mojej wirtualna N-Series systemu Linux

Sterowniki dla maszyn wirtualnych w systemie Linux znajdują się [tutaj](n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nie można znaleźć wystąpienia procesora GPU w mojej wirtualna N serii

Aby skorzystać z możliwości procesora GPU Azure N-series maszyny wirtualne z systemami Windows Server 2016 lub Windows Server 2012 R2, należy zainstalować sterowniki grafiki NVIDIA na każdej maszynie Wirtualnej po wdrożeniu. Sterownik instalacji informacje są dostępne dla [maszyn wirtualnych systemu Windows](../windows/n-series-driver-setup.md) i [maszyn wirtualnych systemu Linux](n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>N-Series maszyn wirtualnych jest dostępny w moim regionie?

Możesz sprawdzić dostępność z [produkty dostępne przez tabelę region](https://azure.microsoft.com/regions/services)i cenach [tutaj](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nie mogę wyświetlić rodziny rozmiar maszyny Wirtualnej, która przy zmianie rozmiaru Moja maszyna wirtualna.

Maszyna wirtualna jest uruchomiona, jest wdrożona na serwerze fizycznym. Serwery fizyczne w regionach platformy Azure są pogrupowane w klastrach wspólnej sprzętu fizycznego. Zmiana rozmiaru maszyny Wirtualnej, który wymaga maszyny Wirtualnej można przenieść do innego sprzętu klastrów różni się w zależności od modelu wdrażania, które zostało użyte do wdrożenia maszyny Wirtualnej.

- Maszyn wirtualnych wdrożonych w klasycznym modelu wdrożenia, wdrożenia usługi w chmurze, należy usunąć i wdrożone, aby zmienić rozmiar innej rodziny maszyn wirtualnych.

- Maszyn wirtualnych wdrożonych w modelu wdrażania usługi Resource Manager, należy zatrzymać wszystkie maszyny wirtualne w zestaw dostępności przed zmieniania rozmiaru żadnej maszyny Wirtualnej w zestawie dostępności.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Wymienione rozmiar maszyny Wirtualnej nie jest obsługiwana podczas wdrażania w zestawie dostępności.

Wybierz rozmiar, który jest obsługiwany w zestawie dostępności klastra. Zaleca się podczas tworzenia dostępność zestawu wybrać największy rozmiar maszyny Wirtualnej przez uważasz, że należy i mieć stosowania pierwszego wdrożenia do zestawu dostępności.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Dystrybucje systemu Linux/wersji są obsługiwane na platformie Azure?

Listę w systemie Linux można znaleźć na [dystrybucje Azure-Endorsed](endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Można dodać istniejącej klasyczne maszyny Wirtualnej do zestawu dostępności?

Tak. Można dodać istniejącej klasyczne maszyny Wirtualnej do nowego lub istniejącego zestawu dostępności. Aby uzyskać więcej informacji, zobacz [Dodaj istniejącą maszynę wirtualną do zestawu dostępności](../windows/classic/configure-availability-classic.md#addmachine).


## <a name="next-steps"></a>Kolejne kroki
Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/support/forums/).

Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskać obsługuje**.
