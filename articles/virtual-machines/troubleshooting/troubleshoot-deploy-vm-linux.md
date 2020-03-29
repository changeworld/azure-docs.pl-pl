---
title: Rozwiązywanie problemów z wdrażaniem problemów z maszyną wirtualną systemu Linux na platformie Azure | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z wdrażaniem problemów z maszyną wirtualną systemu Linux w modelu wdrażania usługi Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 5a8a58a3935b7cd5efb8565f7e3278ccaae4f4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921440"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Rozwiązywanie problemów z wdrażaniem maszyny wirtualnej z systemem Linux na platformie Azure

Aby rozwiązać problemy z wdrażaniem maszyny wirtualnej na platformie Azure, przejrzyj [najważniejsze problemy](#top-issues) dotyczące typowych błędów i rozwiązań.

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.

## <a name="top-issues"></a>Najważniejsze problemy
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Klaster nie może obsługiwać żądanego rozmiaru maszyny Wirtualnej
\<właściwości supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Ponów próbę żądania przy użyciu mniejszego rozmiaru maszyny Wirtualnej.
- Jeśli nie można zmienić rozmiaru żądanej maszyny Wirtualnej:
    - Zatrzymaj wszystkie maszyny wirtualne w zestawie dostępności. Kliknij **pozycję Grupy zasobów** > grupy zasobów > **zasoby** > zestaw dostępności > **maszyny wirtualne** > maszynie wirtualnej > **zatrzymania**.
    - Po zatrzymaniu wszystkich maszyn wirtualnych należy utworzyć maszynę wirtualną w żądanym rozmiarze.
    - Najpierw uruchom nową maszynę wirtualną, a następnie wybierz każdą z zatrzymanych maszyn wirtualnych i kliknij przycisk Start.


## <a name="the-cluster-does-not-have-free-resources"></a>Klaster nie ma wolnych zasobów
\<właściwości supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Ponów próbę żądania później.
- Jeśli nowa maszyna wirtualna może być częścią innego zestawu dostępności
    - Utwórz maszynę wirtualną w innym zestawie dostępności (w tym samym regionie).
    - Dodaj nową maszynę wirtualną do tej samej sieci wirtualnej.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Jak aktywować miesięczne środki na program Visual Studio Enterprise (BizSpark)

Aby aktywować miesięczny kredyt, zobacz ten [artykuł](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Dlaczego nie mogę zainstalować sterownika GPU dla maszyny wirtualnej Ubuntu NV?

Obecnie obsługa procesora GPU systemu Linux jest dostępna tylko na maszynach wirtualnych platformy Azure z systemem Ubuntu Server 16.04 LTS. Aby uzyskać więcej informacji, zobacz [Konfigurowanie sterowników GPU dla maszyn wirtualnych z serii N z systemem Linux](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Brakuje moich sterowników dla mojej maszyny wirtualnej z systemem Linux z serii N

Sterowniki dla maszyn wirtualnych opartych na systemie Linux znajdują się [tutaj](../linux/n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nie mogę znaleźć wystąpienia procesora GPU w mojej maszynie wirtualnej z serii N

Aby korzystać z możliwości procesorów GPU maszyn wirtualnych z serii N platformy Azure z systemem Windows Server 2016 lub Windows Server 2012 R2, należy zainstalować sterowniki graficzne NVIDIA na każdej maszynie wirtualnej po wdrożeniu. Informacje o konfiguracji [sterowników](../windows/n-series-driver-setup.md) są dostępne dla maszyn wirtualnych z systemem Windows i maszyn [wirtualnych z systemem Linux](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Czy maszyny wirtualne z serii N są dostępne w moim regionie?

Dostępność można sprawdzić w [tabeli Produkty dostępne według regionów](https://azure.microsoft.com/regions/services)i cenach [tutaj.](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nie widzę rodziny rozmiarów maszyn wirtualnych, która ma zostać wyświetleń podczas zmiany rozmiaru maszyny Wirtualnej.

Gdy maszyna wirtualna jest uruchomiona, jest wdrażana na serwerze fizycznym. Serwery fizyczne w regionach platformy Azure są grupowane w klastry wspólnego sprzętu fizycznego. Zmiana rozmiaru maszyny Wirtualnej, która wymaga przeniesienia maszyny Wirtualnej do różnych klastrów sprzętowych różni się w zależności od modelu wdrażania, który został użyty do wdrożenia maszyny Wirtualnej.

- Maszyny wirtualne wdrożone w klasycznym modelu wdrażania, wdrożenie usługi w chmurze musi zostać usunięte i ponownie wdrożone, aby zmienić maszyny wirtualne na rozmiar w innej rodzinie rozmiarów.

- Maszyny wirtualne wdrożone w modelu wdrażania Menedżera zasobów, należy zatrzymać wszystkie maszyny wirtualne w zestawie dostępności przed zmianą rozmiaru dowolnej maszyny wirtualnej w zestawie dostępności.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Podany rozmiar maszyny Wirtualnej nie jest obsługiwany podczas wdrażania w zestawie dostępności.

Wybierz rozmiar, który jest obsługiwany w klastrze zestawu dostępności. Jest zalecane podczas tworzenia zestawu dostępności, aby wybrać największy rozmiar maszyny Wirtualnej, które uważasz za potrzebne, i mieć to pierwsze wdrożenie do zestawu dostępności.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Jakie dystrybucje/wersje systemu Linux są obsługiwane na platformie Azure?

Listę można znaleźć w systemie Linux w systemie [Dystrybucja zatwierdzona przez platformę Azure.](../linux/endorsed-distros.md)

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Czy mogę dodać istniejącą klasyczną maszynę wirtualną do zestawu dostępności?

Tak. Można dodać istniejącą klasyczną maszynę wirtualną do nowego lub istniejącego zestawu dostępności. Aby uzyskać więcej informacji, zobacz [Dodawanie istniejącej maszyny wirtualnej do zestawu dostępności](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
