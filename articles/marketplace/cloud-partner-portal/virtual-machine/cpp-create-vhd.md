---
title: Utwórz wirtualny dysk twardy zgodnych z platformą Azure w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Wyjaśnia sposób tworzenia wirtualnego dysku twardego dla oferty maszyny wirtualnej w witrynie Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/27/2018
ms.author: pbutlerm
ms.openlocfilehash: 04a1741bbe4e60567a22445c5674ec03b232640c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744434"
---
# <a name="create-an-azure-compatible-vhd"></a>Utwórz wirtualny dysk twardy zgodnych z platformą Azure

Ten artykuł zawiera kroki wymagane do utworzenia wirtualnego dysku twardego (VHD) dla maszyny wirtualnej (VM) z tej oferty w portalu Azure Marketplace.  Zawiera także najlepsze rozwiązania dotyczące różnych aspektów, takich jak przy użyciu protokołu RDP (Remote Desktop), wybierając rozmiar maszyny wirtualnej, zainstalowanie najnowszych aktualizacji Windows i uogólnianie obrazu wirtualnego dysku twardego.  Poniższe sekcje głównie na wirtualnych dyskach twardych opartych na systemie windows; Aby uzyskać więcej informacji na temat tworzenia opartych na systemie Linux wirtualne dyski twarde, zobacz [systemu Linux na dystrybucje zalecane dla platformy Azure](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Zdecydowanie zaleca się, postępuj zgodnie ze wskazówkami w tym temacie, Utwórz Maszynę wirtualną zawierającą wstępnie skonfigurowane, zalecane dla systemu operacyjnego za pomocą platformy Azure.  Jeśli to nie jest zgodna z rozwiązaniem, następnie istnieje możliwość utworzyć i skonfigurować lokalną maszynę Wirtualną przy użyciu zatwierdzonego systemu operacyjnego.  Można skonfigurować i przygotować go do przekazywania, zgodnie z opisem w [przygotowanie Windows dysku VHD lub VHDX można przekazać na platformę Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Wybierz podstawowy zatwierdzone
Wirtualny dysk twardy systemu operacyjnego obrazu maszyny Wirtualnej musi być oparta na zatwierdzone Azure podstawowy obraz, który zawiera systemu Windows Server lub SQL Server.
Aby rozpocząć, Utwórz Maszynę wirtualną z jednego z następujących obrazów znajdujących się w portalu Microsoft Azure:

-   System Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/), [2012 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/), [2008 R2 z dodatkiem SP1](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/))
-   [Program SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, sieci Web)
-   [SQL Server 2012 z dodatkiem SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, sieci Web)

> [!TIP]
> Jeśli używasz aktualnej wersji portalu Azure lub programu PowerShell obrazy systemu Windows Server opublikowane 8 września 2014 r. i nowszych są zatwierdzane.

Alternatywnie platforma Azure oferuje szeroką gamę zatwierdzone dystrybucje systemu Linux.  Bieżącą listę, zobacz [systemu Linux na dystrybucje zalecane dla platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Tworzenie maszyny Wirtualnej w witrynie Azure portal 

W programie Microsoft [witryny Azure portal](https://ms.portal.azure.com/), utworzyć obraz podstawowy wykonując następujące kroki.

1. Zaloguj się do portalu przy użyciu konta Microsoft subskrypcji platformy Azure, czy chcesz opublikować ofertę maszyny Wirtualnej.
2. Utwórz nową grupę zasobów i podaj swoje **nazwy grupy zasobów**, **subskrypcji**, i **lokalizację grupy zasobów**.  Aby uzyskać więcej wskazówek, zobacz [Zarządzanie grupami zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Kliknij pozycję **maszyn wirtualnych** na pasku menu po lewej stronie, aby wyświetlić stronę szczegółów maszyn wirtualnych. 
4. W tej nowej strony kliknij **+ Dodaj** do wyświetlenia **obliczenia** bloku.  Jeśli nie ma typu maszyny Wirtualnej na ekranie początkowa, nazwa podstawowej maszyny Wirtualnej, można wyszukać na przykład:

    ![Blok nowej maszyny Wirtualnej obliczenia](./media/publishvm_014.png)

5. Po wybraniu odpowiedniego obrazu wirtualnej, podaj następujące wartości:
   * Na **podstawy** bloku wprowadź **nazwa** dla maszyny wirtualnej, od 1 do 15 znaków alfanumerycznych. (W tym przykładzie użyto `DemoVm009`.)
   * Wprowadź **nazwa_użytkownika** i silne **hasło**, które są używane do utworzenia konta lokalnego na maszynie Wirtualnej.  (W tym miejscu `adminUser` jest używana.)  Hasło musi mieć długość od 8 do 123 znaków i spełniać trzy z czterech następujących wymagań dotyczących złożoności: mała litera, wielka litera, cyfra i znak specjalny. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące nazwy użytkownika i hasła](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Wybierz utworzoną grupę zasobów (w tym miejscu `DemoResourceGroup`).
   * Wybierz centrum danych Azure **lokalizacji** (w tym miejscu `West US`).
   * Kliknij przycisk **OK** można zapisać tych wartości. 

6. Wybierz rozmiar maszyny Wirtualnej, aby wdrożyć przy użyciu następujące zalecenia:
   * Jeśli zamierzasz utworzyć wirtualny dysk twardy w środowisku lokalnym, rozmiar nie ma znaczenia. Zastanów się nad użyciem maszyny wirtualnej o mniejszym rozmiarze.
   * Jeśli zamierzasz utworzyć obraz na platformie Azure, warto wybrać jeden z zalecanych rozmiarów maszyny wirtualnej dla wybranego obrazu.
   * Aby uzyskać informacje o cenach, zobacz **zalecane warstwy cenowe** selektor wyświetlany w portalu. Zostaną wyświetlone trzy zalecane rozmiary udostępniane przez wydawcę. (W tym miejscu wydawca jest firmy Microsoft).

   ![Rozmiar bloku nowej maszyny Wirtualnej](./media/publishvm_015.png)

7. W **ustawienia** ustawić bloku **użycie dysku zarządzanego** opcję **nie**.  Dzięki temu można ręcznie zarządzać nowego wirtualnego dysku twardego. ( **Ustawienia** bloku pozwala również na innych zmian, zmienić opcje magazynu i sieci, na przykład wybranie **Premium (SSD)** w **typ dysku**.)  Kliknij przycisk **OK** aby kontynuować.

    ![Bloku ustawienia nowej maszyny Wirtualnej](./media/publishvm_016.png)

8. Kliknij pozycję **Podsumowanie**, aby przejrzeć wybrane opcje. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **OK**.

    ![Blok podsumowania dla nowej maszyny Wirtualnej](./media/publishvm_017.png)

Azure rozpoczyna inicjowanie obsługi administracyjnej maszyny wirtualnej, wskazana.  Możesz śledzić postęp, klikając **maszyn wirtualnych** karty po lewej stronie.  Po jego utworzeniu stan zmieni się na **systemem**.  W tym momencie możesz [łączenie z maszyną wirtualną](./cpp-connect-vm.md).


## <a name="next-steps"></a>Kolejne kroki

Jeśli pojawił się trudności, tworzenie nowych VHD opartych na platformie Azure, zobacz [typowe problemy występujące podczas tworzenia wirtualnego dysku twardego](./cpp-common-vhd-creation-issues.md).  W przeciwnym razie, następnie należy najpierw [nawiązać połączenie z maszynami wirtualnymi](./cpp-connect-vm.md) utworzone na platformie Azure. 
