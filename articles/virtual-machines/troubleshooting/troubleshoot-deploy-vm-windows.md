---
title: Rozwiązywanie wdrażanie Windows maszyny wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Wdrażanie Windows maszyny wirtualnej rozwiązywania problemów z w modelu wdrażania usługi Azure Resource Manager.
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
ms.openlocfilehash: 5752731f08a7dc9ae8661e698aef9655837c6220
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60593261"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Rozwiązywanie wdrażanie Windows maszyny wirtualnej na platformie Azure

Aby rozwiązać problemy z wdrażaniem maszynę wirtualną (VM) na platformie Azure, zapoznaj się z [Najważniejsze problemy](#top-issues) typowe błędy i ich rozwiązania.

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.

## <a name="top-issues"></a>Najczęstsze problemy
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Jak używać i wdrażanie obrazów klientów systemu windows na platformie Azure?

Używając Windows 7, Windows 8 lub Windows 10 na platformie Azure w scenariuszach tworzenia i testowania w przypadku posiadania odpowiednich subskrypcji programu Visual Studio (dawniej MSDN). To [artykułu](../windows/client-images.md) opisano wymagania kwalifikacyjne dla uruchomionego klienta Windows na platformie Azure i korzysta z obrazów w galerii platformy Azure.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Jak wdrożyć maszynę wirtualną za pomocą korzyści użycia hybrydowego (HUB)

Istnieje kilka różnych sposobów wdrażania maszyn wirtualnych Windows dzięki korzyściom z używania hybrydowej platformy Azure.

Dla subskrypcji Enterprise Agreement:

• Wdrażanie maszyn wirtualnych z określonych obrazów z portalu Marketplace, które zostały wstępnie skonfigurowane z używania hybrydowej platformy Azure.

Dla umowy Enterprise agreement:

• Przekazać niestandardową maszynę Wirtualną i wdrażanie jej za pomocą szablonu usługi Resource Manager lub programu Azure PowerShell.

Więcej informacji zawierają następujące zasoby:

 - [Omówienie korzyści z używania hybrydowej platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Do pobrania — często zadawane pytania](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Korzyści z używania hybrydowej platformy Azure dla systemu Windows Server i klienta Windows](../windows/hybrid-use-benefit-licensing.md).

 - [Jak używać korzyści z używania hybrydowej platformy Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Jak uaktywnić miesięcznych środków dla programu Visual studio Enterprise (BizSpark)

Aby aktywować swoje miesięczne środki, zobacz ten [artykułu](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Jak dodać do mojego Enterprise Agreement (EA) Aby uzyskać dostęp do okna obrazów klientów przedsiębiorstwa i testowania?

Możliwość tworzenia subskrypcji w ramach oferty „Enterprise — tworzenie i testowanie” jest zarezerwowana dla właścicieli kont z odpowiednimi uprawnieniami udzielonymi przez administratora przedsiębiorstwa. Właściciel konta tworzy subskrypcje za pośrednictwem portalu konta platformy Azure i czym powinien dodać aktywnych subskrybentów programu Visual Studio jako współadministratorów. Tak, aby umożliwić im zarządzanie oraz korzystać z zasobów potrzebnych do tworzenia i testowania aplikacji. Aby uzyskać więcej informacji, zobacz [przedsiębiorstwa i testowania](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Moje sterowniki są spełnione dla maszyną Wirtualną serii N z systemem Windows

Sterowniki dla maszyn wirtualnych z systemem Windows znajdują się [tutaj](../windows/n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nie można znaleźć wystąpienia procesora GPU w moich maszyn wirtualnych serii N

Aby skorzystać z możliwości procesora GPU z serii N maszyn wirtualnych platformy Azure z systemu Windows Server 2016 lub Windows Server 2012 R2, należy zainstalować sterowniki grafiki NVIDIA na każdej maszynie Wirtualnej po wdrożeniu. Informacje o konfiguracji sterownika jest dostępna dla [maszyn wirtualnych Windows](../windows/n-series-driver-setup.md) i [maszyn wirtualnych systemu Linux](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Maszyny wirtualne z serii N jest dostępna w moim regionie?

Możesz sprawdzić dostępność z [dostępność produktów według regionów tabeli](https://azure.microsoft.com/regions/services)i ceny [tutaj](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Jakie obrazy klienta I użycia i Wdróż na platformie Azure i jak można uzyskać je?

Możesz użyć Windows 7, Windows 8 lub Windows 10 na platformie Azure na potrzeby scenariuszy deweloperskich/testowych, pod warunkiem, że masz odpowiednią subskrypcję programu Visual Studio (dawniej MSDN). 

- Obrazy systemu Windows 10 są dostępne w galerii platformy Azure w ramach [kwalifikujących się ofertach tworzenia i testowania](../windows/client-images.md#eligible-offers). 
- Subskrybenci programu Visual Studio w ramach dowolnego typu oferty, może również [odpowiednio przygotowywanie i tworzenie](../windows/prepare-for-upload-vhd-image.md) 64-bitowy obraz Windows 7, Windows 8 lub Windows 10 i następnie [przekazać na platformę Azure](../windows/upload-generalized-managed.md). Użycie pozostaje ograniczony do tworzenia i testowania aktywnych subskrybentów programu Visual Studio.

To [artykułu](../windows/client-images.md) opisano wymagania kwalifikacyjne dla uruchomionego klienta Windows na platformie Azure i korzystanie z obrazów w galerii platformy Azure.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nie mogę wyświetlić rodziny rozmiaru maszyny Wirtualnej, która ma podczas zmiany rozmiaru Moja maszyna wirtualna.

Gdy maszyna wirtualna jest uruchomiona, jest wdrażana serwera fizycznego. Serwerów fizycznych w regionach platformy Azure są grupowane w klastrach typowego sprzętu fizycznego. Zmiana rozmiaru maszyny Wirtualnej, która wymaga maszyny Wirtualnej zostanie przeniesiony na inny sprzęt w klastrach różni się w zależności od modelu wdrażania, który został użyty do wdrożenia tej maszyny Wirtualnej.

- Maszyny wirtualne wdrożone w klasycznym modelu wdrażania przy użyciu wdrażania usługi w chmurze, należy usunąć i ponownie wdrożyć, aby zmienić rozmiar w innej rodziny rozmiaru maszyny wirtualne.

- Maszyn wirtualnych wdrożonych w modelu wdrażania usługi Resource Manager, należy zatrzymać wszystkie maszyny wirtualne w zestawie przed zmianą rozmiaru każdej maszyny Wirtualnej w zestawie dostępności dostępności.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Uwzględnione na liście rozmiar maszyny Wirtualnej nie jest obsługiwany podczas wdrażania w zestawie dostępności.

Wybierz rozmiar, który jest obsługiwany na zestaw dostępności klastra. Zalecane jest, gdy tworzenie dostępności zestawu, aby wybrać największy rozmiar maszyny Wirtualnej, o jakich myślisz, gdy potrzebujesz i być pierwszego wdrożenia do zestawu dostępności.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Do zestawu dostępności można dodać istniejącej klasycznej maszyny Wirtualnej?

Tak. Możesz dodać istniejące klasycznej maszyny Wirtualnej do nowego lub istniejącego zestawu dostępności. Aby uzyskać więcej informacji, zobacz [Dodaj istniejącą maszynę wirtualną do zestawu dostępności](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Kolejne kroki
Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.
