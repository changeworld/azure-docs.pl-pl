---
title: Rozwiązywanie problemów z wdrażaniem problemów z maszyną wirtualną systemu Windows na platformie Azure | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z wdrażaniem problemów z maszyną wirtualną systemu Windows w modelu wdrażania usługi Azure Resource Manager.
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
ms.openlocfilehash: cdbaeb5a97beba342bc471e75d1b07be0d0141ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921415"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Rozwiązywanie problemów z wdrażaniem maszyny wirtualnej z systemem Windows na platformie Azure

Aby rozwiązać problemy z wdrażaniem maszyny wirtualnej na platformie Azure, przejrzyj [najważniejsze problemy](#top-issues) dotyczące typowych błędów i rozwiązań.

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.

## <a name="top-issues"></a>Najważniejsze problemy
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Jak używać i wdrażać obraz klienta systemu Windows na platformie Azure?

Można użyć systemu Windows 7, Windows 8 lub Windows 10 na platformie Azure dla scenariuszy deweloperskich/testowych, jeśli masz odpowiednią subskrypcję programu Visual Studio (dawniej MSDN). W tym [artykule](../windows/client-images.md) opisano wymagania dotyczące uprawnień do uruchamiania klienta systemu Windows na platformie Azure i użycia obrazów galerii platformy Azure.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Jak wdrożyć maszynę wirtualną przy użyciu korzyści użycia hybrydowego (HUB)?

Istnieje kilka różnych sposobów wdrażania maszyn wirtualnych z systemem Windows z korzyścią użycia hybrydowego platformy Azure.

W przypadku subskrypcji umowy Enterprise Agreement:

• Wdrażanie maszyn wirtualnych z określonych obrazów portalu Marketplace, które są wstępnie skonfigurowane z korzyścią użycia hybrydowego platformy Azure.

Umowa Enterprise:

• Przekaż niestandardową maszynę wirtualną i wdrażaj przy użyciu szablonu Usługi Resource Manager lub programu Azure PowerShell.

Więcej informacji zawierają następujące zasoby:

 - [Omówienie korzyści z użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Często zadawane pytania dotyczące pobierania](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Korzyści użycia hybrydowego platformy Azure dla systemu Windows Server i klienta systemu Windows](../windows/hybrid-use-benefit-licensing.md).

 - [Jak korzystać z korzyści z użycia hybrydowego na platformie Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Jak aktywować miesięczne środki na program Visual Studio Enterprise (BizSpark)

Aby aktywować miesięczny kredyt, zobacz ten [artykuł](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Jak dodać dewelopera/test przedsiębiorstwa do umowy Enterprise Agreement (EA), aby uzyskać dostęp do obrazów klientów window?

Możliwość tworzenia subskrypcji na podstawie oferty deweloper/test przedsiębiorstwa jest ograniczona do właścicieli kont, którzy otrzymali do tego uprawnienia administratora przedsiębiorstwa. Właściciel konta tworzy subskrypcje za pośrednictwem portalu konta azure, a następnie należy dodać aktywnych subskrybentów programu Visual Studio jako współadministratorów. Aby mogli zarządzać zasobami potrzebnymi do tworzenia i testowania i korzystać z niego. Aby uzyskać więcej informacji, zobacz [Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Brakuje moich sterowników dla mojej maszyny Wirtualnej z systemem Windows N

Sterowniki dla maszyn wirtualnych z systemem Windows znajdują się [tutaj](../windows/n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nie mogę znaleźć wystąpienia procesora GPU w mojej maszynie wirtualnej z serii N

Aby korzystać z możliwości procesorów GPU maszyn wirtualnych z serii N platformy Azure z systemem Windows Server 2016 lub Windows Server 2012 R2, należy zainstalować sterowniki graficzne NVIDIA na każdej maszynie wirtualnej po wdrożeniu. Informacje o konfiguracji [sterowników](../windows/n-series-driver-setup.md) są dostępne dla maszyn wirtualnych z systemem Windows i maszyn [wirtualnych z systemem Linux](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Czy maszyny wirtualne z serii N są dostępne w moim regionie?

Dostępność można sprawdzić w [tabeli Produkty dostępne według regionów](https://azure.microsoft.com/regions/services)i cenach [tutaj.](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Jakich obrazów klienta mogę używać i wdrażać na platformie Azure oraz jak je uzyskać?

Systemu Windows 7, Windows 8 lub Windows 10 można użyć do scenariuszy deweloperskich/testowych, pod warunkiem że masz odpowiednią subskrypcję programu Visual Studio (dawniej MSDN). 

- Obrazy systemu Windows 10 są dostępne w Galerii Azure w ramach [kwalifikujących się ofert deweloperskich/testowych.](../windows/client-images.md#eligible-offers) 
- Subskrybenci programu Visual Studio w ramach dowolnej oferty mogą również [odpowiednio przygotować i utworzyć](../windows/prepare-for-upload-vhd-image.md) 64-bitowy obraz systemu Windows 7, Windows 8 lub Windows 10, a następnie [przesłać go na platformę Azure.](../windows/upload-generalized-managed.md) Użycie pozostaje ograniczone do dev/test przez aktywnych subskrybentów programu Visual Studio.

W tym [artykule](../windows/client-images.md) opisano wymagania dotyczące uprawnień do uruchamiania klienta systemu Windows na platformie Azure i korzystania z obrazów galerii platformy Azure.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nie widzę rodziny rozmiarów maszyn wirtualnych, która ma zostać wyświetleń podczas zmiany rozmiaru maszyny Wirtualnej.

Gdy maszyna wirtualna jest uruchomiona, jest wdrażana na serwerze fizycznym. Serwery fizyczne w regionach platformy Azure są grupowane w klastry wspólnego sprzętu fizycznego. Zmiana rozmiaru maszyny Wirtualnej, która wymaga przeniesienia maszyny Wirtualnej do różnych klastrów sprzętowych różni się w zależności od modelu wdrażania, który został użyty do wdrożenia maszyny Wirtualnej.

- Maszyny wirtualne wdrożone w klasycznym modelu wdrażania, wdrożenie usługi w chmurze musi zostać usunięte i ponownie wdrożone, aby zmienić maszyny wirtualne na rozmiar w innej rodzinie rozmiarów.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

- Maszyny wirtualne wdrożone w modelu wdrażania Menedżera zasobów, należy zatrzymać wszystkie maszyny wirtualne w zestawie dostępności przed zmianą rozmiaru dowolnej maszyny wirtualnej w zestawie dostępności.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Podany rozmiar maszyny Wirtualnej nie jest obsługiwany podczas wdrażania w zestawie dostępności.

Wybierz rozmiar, który jest obsługiwany w klastrze zestawu dostępności. Jest zalecane podczas tworzenia zestawu dostępności, aby wybrać największy rozmiar maszyny Wirtualnej, które uważasz za potrzebne, i mieć to pierwsze wdrożenie do zestawu dostępności.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Czy mogę dodać istniejącą klasyczną maszynę wirtualną do zestawu dostępności?

Tak. Można dodać istniejącą klasyczną maszynę wirtualną do nowego lub istniejącego zestawu dostępności. Aby uzyskać więcej informacji, zobacz [Dodawanie istniejącej maszyny wirtualnej do zestawu dostępności](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
