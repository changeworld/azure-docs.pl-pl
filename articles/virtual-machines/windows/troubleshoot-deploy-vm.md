---
title: Rozwiązywanie problemów z wdrażanie problemy dotyczące maszyny wirtualnej systemu Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Wdrażanie systemu Windows maszyny wirtualnej Rozwiązywanie problemów z w modelu wdrażania Azurethe Resource Manager.
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
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: fda249295cf3bbf6f9577eb90a147f46524b95ff
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Rozwiązywanie problemów z wdrażanie problemy dotyczące maszyny wirtualnej systemu Windows na platformie Azure

Aby rozwiązać problemy z wdrażaniem maszyny wirtualnej (VM) na platformie Azure, przejrzyj [Najważniejsze problemy](#top-issues) dla typowych błędów i rozwiązania.

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskać obsługuje**.

## <a name="top-issues"></a>Najważniejsze problemy
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Jak używać i wdrażanie obrazu klienta systemu windows na platformie Azure?

Służy Windows 7, Windows 8 lub Windows 10 w usłudze Azure scenariusze tworzenia/testowania Jeśli masz odpowiednie subskrypcji programu Visual Studio (dawniej MSDN). To [artykułu](client-images.md) opisano wymagania kwalifikujących się usług dla uruchomionego klienta systemu Windows na platformie Azure i korzysta z obrazów w galerii Azure.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Jak można wdrożyć maszyny wirtualnej z wykorzystaniem korzyści Użyj hybrydowych (KONCENTRATOR)

Istnieje kilka różnych sposobów wdrażania maszyn wirtualnych systemu Windows z asysty Użyj hybrydowe platformy Azure.

Dla subskrypcji Enterprise Agreement:

• Wdrażanie maszyn wirtualnych z określonych obrazów Marketplace, które są wstępnie skonfigurowane z korzyści Użyj hybrydowe platformy Azure.

Dla umowy Enterprise agreement:

• Przekazywanie niestandardowych maszyny Wirtualnej i wdrażanie za pomocą szablonu usługi Resource Manager lub programu Azure PowerShell.

Więcej informacji zawierają następujące zasoby:

 - [Omówienie usługi Azure korzyści Użyj hybrydowego ](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Do pobrania — często zadawane pytania](http://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Korzyści Użyj hybrydowe platformy Azure dla systemu Windows Server i klienta systemu Windows](hybrid-use-benefit-licensing.md).

 - [Jak używać korzyści Użyj hybrydowe na platformie Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Jak aktywować Moje miesięczny kredyt dla programu Visual studio Enterprise (BizSpark)

Aby aktywować Twój miesięczny kredyt, zobacz [artykułu](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Jak dodać przedsiębiorstwa i testowania do mojego Enterprise Agreement (EA) Aby uzyskać dostęp do okna obrazów klientów?

Możliwość tworzenia subskrypcje oparte na ofertę przedsiębiorstwa i testowania jest ograniczone do właścicieli konta, które zostały przypisane uprawnienia, aby to zrobić przez administratora przedsiębiorstwa. Właściciel konta tworzy subskrypcji za pośrednictwem portalu konta usługi Azure, a następnie należy Dodaj aktywnych subskrybentów usługi Visual Studio jako współadministratorów. Aby mogli zarządzać i użycia zasobów niezbędnych do projektowania i testowania. Aby uzyskać więcej informacji, zobacz [przedsiębiorstwa: tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Brak sterowników, dla moich wirtualna N-Series systemu Windows

Sterowniki dla maszyn wirtualnych z systemem Windows znajdują się [tutaj](n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nie można znaleźć wystąpienia procesora GPU w mojej wirtualna N serii

Aby skorzystać z możliwości procesora GPU Azure N-series maszyny wirtualne z systemami Windows Server 2016 lub Windows Server 2012 R2, należy zainstalować sterowniki grafiki NVIDIA na każdej maszynie Wirtualnej po wdrożeniu. Sterownik instalacji informacje są dostępne dla [maszyn wirtualnych systemu Windows](n-series-driver-setup.md) i [maszyn wirtualnych systemu Linux](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>N-Series maszyn wirtualnych jest dostępny w moim regionie?

Możesz sprawdzić dostępność z [produkty dostępne przez tabelę region](https://azure.microsoft.com/regions/services)i cenach [tutaj](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Jakie obrazy klienta I użycia i wdrożyć w Azure i jak można uzyskać je?

Można użyć systemu Windows 7, Windows 8 lub Windows 10 w usłudze Azure scenariusze tworzenia/testowania, pod warunkiem, że masz odpowiednie subskrypcji programu Visual Studio (dawniej MSDN). 

- Obrazy systemu Windows 10 są dostępne w galerii Azure w ramach [oferuje kwalifikujących się tworzenie/testowanie](client-images.md#eligible-offers). 
- Subskrybentów usługi Visual Studio w obrębie dowolnego typu oferty może również [odpowiednio przygotować i utworzyć](prepare-for-upload-vhd-image.md) 64-bitowego obrazu systemu Windows 7, Windows 8 lub Windows 10, a następnie [przekazać Azure](upload-generalized-managed.md). Użycie pozostaje ograniczone do: tworzenie i testowanie przez aktywnych subskrybentów usługi Visual Studio.

To [artykułu](client-images.md) opisano wymagania kwalifikuje uruchomionej klienta systemu Windows Azure i używanie obrazów w galerii Azure.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nie mogę wyświetlić rodziny rozmiar maszyny Wirtualnej, która przy zmianie rozmiaru Moja maszyna wirtualna.

Maszyna wirtualna jest uruchomiona, jest wdrożona na serwerze fizycznym. Serwery fizyczne w regionach platformy Azure są pogrupowane w klastrach wspólnej sprzętu fizycznego. Zmiana rozmiaru maszyny Wirtualnej, który wymaga maszyny Wirtualnej można przenieść do innego sprzętu klastrów różni się w zależności od modelu wdrażania, które zostało użyte do wdrożenia maszyny Wirtualnej.

- Maszyn wirtualnych wdrożonych w klasycznym modelu wdrożenia, wdrożenia usługi w chmurze, należy usunąć i wdrożone, aby zmienić rozmiar innej rodziny maszyn wirtualnych.

- Maszyn wirtualnych wdrożonych w modelu wdrażania usługi Resource Manager, należy zatrzymać wszystkie maszyny wirtualne w zestaw dostępności przed zmieniania rozmiaru żadnej maszyny Wirtualnej w zestawie dostępności.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Wymienione rozmiar maszyny Wirtualnej nie jest obsługiwana podczas wdrażania w zestawie dostępności.

Wybierz rozmiar, który jest obsługiwany w zestawie dostępności klastra. Zaleca się podczas tworzenia dostępność zestawu wybrać największy rozmiar maszyny Wirtualnej przez uważasz, że należy i mieć stosowania pierwszego wdrożenia do zestawu dostępności.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Można dodać istniejącej klasyczne maszyny Wirtualnej do zestawu dostępności?

Tak. Można dodać istniejącej klasyczne maszyny Wirtualnej do nowego lub istniejącego zestawu dostępności. Aby uzyskać więcej informacji, zobacz [Dodaj istniejącą maszynę wirtualną do zestawu dostępności](classic/configure-availability-classic.md#addmachine).


## <a name="next-steps"></a>Kolejne kroki
Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/support/forums/).

Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskać obsługuje**.
