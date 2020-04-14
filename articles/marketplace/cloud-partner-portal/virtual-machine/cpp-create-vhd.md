---
title: Tworzenie dysku twardego zgodnego z platformą Azure dla portalu Azure Marketplace
description: W tym artykule wyjaśniono, jak utworzyć dysk VHD dla oferty maszyny wirtualnej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 99d2bc95c1dd837bfc3bcabcead28777b7e6f746
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273940"
---
# <a name="create-an-azure-compatible-vhd"></a>Tworzenie dysku VHD zgodnego z platformą Azure

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami maszyny wirtualnej platformy Azure do centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [Tworzenie zasobów technicznych maszyny wirtualnej platformy Azure](https://aka.ms/AzureVMTechAsset) do zarządzania zmigrowanych ofert.

W tym artykule opisano kroki wymagane do utworzenia wirtualnego dysku twardego (VHD) dla oferty maszyny wirtualnej (VM) w portalu Azure Marketplace.  Zawiera również najlepsze rozwiązania dotyczące różnych aspektów, takich jak używanie protokołu RDP (Remote Desktop Protocol), wybieranie rozmiaru maszyny Wirtualnej, instalowanie najnowszych aktualizacji systemu Windows i uogólnianie obrazu VHD.  W poniższych sekcjach skupiono się głównie na veds opartych na systemie Windows; Aby uzyskać więcej informacji na temat tworzenia vhd opartych na systemie Linux, zobacz [Linux w sprawie dystrybucji zatwierdzonych przez platformę Azure](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Zdecydowanie zaleca się, aby postępować zgodnie ze wskazówkami w tym temacie, aby użyć platformy Azure do utworzenia maszyny Wirtualnej zawierającej wstępnie skonfigurowany, zatwierdzony system operacyjny.  Jeśli nie jest to zgodne z rozwiązaniem, możliwe jest utworzenie i skonfigurowanie lokalnej maszyny Wirtualnej przy użyciu zatwierdzonego systemu operacyjnego.  Następnie można skonfigurować i przygotować go do przesłania zgodnie z opisem w [temacie Przygotowanie dysku VHD lub VHDX systemu Windows do przekazania na platformę Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Wybierz zatwierdzoną bazę
Dysk VHD systemu operacyjnego dla obrazu maszyny Wirtualnej musi być oparty na obrazie podstawowym zatwierdzonym przez platformę Azure, który zawiera system Windows Server lub SQL Server.
Aby rozpocząć, utwórz maszynę wirtualną z jednego z następujących obrazów, znajdującego się w witrynie Microsoft Azure portal:

-    Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-    [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
-    [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!TIP]
> Jeśli używasz bieżącego portalu Azure lub programu PowerShell, obrazy systemu Windows Server opublikowane 8 września 2014 r. i nowsze zostaną zatwierdzone.

Alternatywnie platforma Azure oferuje szereg zatwierdzonych dystrybucji systemu Linux.  Aby uzyskać aktualną listę, zobacz [Linux w sprawie dystrybucji zatwierdzonych przez platformę Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Tworzenie maszyny wirtualnej w witrynie Azure portal 

W [portalu Microsoft Azure](https://ms.portal.azure.com/)utwórz obraz podstawowy, wykonując następujące kroki.

1. Zaloguj się do portalu za pomocą konta Microsoft dla subskrypcji platformy Azure, którą chcesz opublikować ofertę maszyny Wirtualnej.
2. Utwórz nową grupę zasobów i podaj **nazwę grupy zasobów,** **subskrypcję**i **lokalizację grupy zasobów**.  Aby uzyskać więcej wskazówek, zobacz [Zarządzanie grupami zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Kliknij maszyny **wirtualne** na lewym pasku menu, aby wyświetlić stronę szczegóły maszyn wirtualnych. 
4. Na tej nowej stronie kliknij **+Dodaj,** aby wyświetlić **obliczeń** bloku.  Jeśli nie widzisz typu maszyny Wirtualnej na ekranie początkowym, możesz wyszukać nazwę podstawowej maszyny Wirtualnej, na przykład:

    ![Oblicz ostrza nowej maszyny Wirtualnej](./media/publishvm_014.png)

5. Po wybraniu odpowiedniego obrazu wirtualnego podaj następujące wartości:
   * W bloku **Podstawy** wprowadź **nazwę** maszyny wirtualnej między 1-15 znakami alfanumerycznymi. (W tym `DemoVm009`przykładzie użyto .)
   * Wprowadź **nazwę użytkownika** i silne **hasło**, które są używane do tworzenia konta lokalnego na maszynie wirtualnej.  (Tutaj `adminUser` jest używany.)  Hasło musi mieć długość od 8 do 123 znaków i spełniać trzy z czterech następujących wymagań dotyczących złożoności: jeden znak o mniejszej litery, jeden znak wielkie litery, jedna liczba i jeden znak specjalny. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące nazwy użytkownika i hasła](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Wybierz utworzoną grupę `DemoResourceGroup`Zasobów (tutaj ).
   * Wybierz **lokalizację** centrum danych platformy `West US`Azure (tutaj).
   * Kliknij **przycisk OK,** aby zapisać te wartości. 

6. Wybierz rozmiar maszyny Wirtualnej do wdrożenia, korzystając z następujących zaleceń:
   * Jeśli planujesz rozwijać VHD lokalnie, rozmiar nie ma znaczenia. Należy rozważyć użycie jednej z mniejszych maszyn wirtualnych.
   * Jeśli planujesz opracować obraz na platformie Azure, należy rozważyć użycie jednego z zalecanych rozmiarów maszyn wirtualnych dla wybranego obrazu.
   * Informacje o cenach można znaleźć w selektorze **Zalecane warstwy cenowe** wyświetlane w portalu. Wyświetli trzy zalecane rozmiary dostarczone przez wydawcę. (W tym miejscu wydawcą jest firma Microsoft).

   ![Rozmiar ostrza nowej maszyny Wirtualnej](./media/publishvm_015.png)

7. W bloku **Ustawienia** ustaw opcję **Użyj dysku zarządzanego** na **Nie**.  Dzięki temu można ręcznie zarządzać nowym dyskiem VHD. (Blok **Ustawienia** umożliwia również zmianę innych opcji przechowywania i sieci, na przykład wybranie **opcji Premium (SSD)** w **typie dysku).**  Kliknij **przycisk OK,** aby kontynuować.

    ![Ostrza ustawień nowej maszyny Wirtualnej](./media/publishvm_016.png)

8. Kliknij pozycję **Podsumowanie**, aby przejrzeć wybrane opcje. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **OK**.

    ![Podsumowanie ostrza nowej maszyny wirtualnej](./media/publishvm_017.png)

Platforma Azure rozpoczyna inicjowanie obsługi administracyjnej określonej maszyny wirtualnej.  Możesz śledzić jego postęp, klikając kartę **Maszyny wirtualne** po lewej stronie.  Po jego utworzeniu stan zmieni się na **Uruchomiony**.  W tym momencie można [połączyć się z maszyną wirtualną](./cpp-connect-vm.md).


## <a name="next-steps"></a>Następne kroki

Jeśli napotkano trudności z utworzeniem nowego dysku VHD opartego na platformie Azure, zobacz [Typowe problemy podczas tworzenia dysków VHD.](./cpp-common-vhd-creation-issues.md)  W przeciwnym razie następnie należy [połączyć się z maszynami wirtualnymi utworzonymi](./cpp-connect-vm.md) na platformie Azure. 
