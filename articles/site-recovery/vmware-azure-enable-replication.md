---
title: Włączanie replikacji maszyn wirtualnych programu VMware na potrzeby odzyskiwania po awarii na platformie Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób włączania maszyn wirtualnych VMware replikacji do platformy Azure na potrzeby odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 4/18/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: ba55afbd62bbbc2290d1daaebf77becc249c1d8b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004741"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Włącz replikację na platformę Azure dla maszyn wirtualnych VMware

W tym artykule opisano sposób włączania replikacji lokalnych maszyn wirtualnych programu VMware do platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz:

- [Konfigurowanie środowiska źródłowego w środowisku lokalnym](vmware-azure-set-up-source.md).
- [Konfigurowanie środowiska docelowego, na platformie Azure](vmware-azure-set-up-target.md).

## <a name="before-you-start"></a>Przed rozpoczęciem
Jeśli replikujesz maszyny wirtualne VMware pamiętać o tych informacji:

* Twoje konto platformy Azure użytkownika musi mieć pewne [uprawnienia](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) Aby włączyć replikację nowej maszyny wirtualnej na platformie Azure.
* Maszyny wirtualne VMware są odnajdywane co 15 minut. Może potrwać 15 minut lub dłużej wyświetlane w witrynie Azure portal po odnalezieniu maszyn wirtualnych. Podobnie, odnajdywanie może potrwać 15 minut lub dłużej, po dodaniu nowego vCenter server lub hoście vSphere.
* Może potrwać 15 minut lub dłużej, aby zmiany środowiska na maszynie wirtualnej (na przykład instalacji narzędzi VMware) należy zaktualizować w portalu.
* Możesz sprawdzić czas odnalezione ostatnio dla maszyn wirtualnych VMware: Zobacz **ostatni kontakt** na **serwery konfiguracji** strony na serwerze vCenter/hoście vSphere.
* Aby dodać maszyny wirtualne na potrzeby replikacji, nie czekając na zaplanowane odnajdowanie, wyróżnij serwer konfiguracji (ale nie kliknij ją) i wybierz **Odśwież**.
* Po włączeniu replikacji, jeśli maszyna wirtualna jest gotowa, serwer przetwarzania automatycznie instaluje na nim usługa Azure Site Recovery Mobility.

## <a name="enable-replication"></a>Włączanie replikacji

Przed wykonaniem kroków opisanych w tej sekcji, należy zwrócić uwagę na następujące informacje:
* Usługa Azure Site Recovery replikuje teraz bezpośrednio do usługi managed disks dla wszystkich nowych replikacji. Serwer przetwarzania zapisuje dzienników replikacji na konto magazynu pamięci podręcznej w regionie docelowym. Dzienniki te są używane do tworzenia punktów odzyskiwania w zarządzanych dyskach repliki.
* W momencie przejścia w tryb failover punktu odzyskiwania, który wybierzesz służy do utworzenia dysku zarządzanego w docelowym.
* Nie ma wpływu na maszyny wirtualne, które zostały wcześniej skonfigurowane zostać zreplikowana na docelowe konta magazynu.
* Replikacja do konta magazynu dla nowej maszyny wirtualnej jest dostępna tylko za pośrednictwem REST Representational State Transfer () interfejsu API i programu Powershell. Replikacja do konta magazynu, należy użyć interfejsu API REST platformy Azure w wersji 2016-08-10 lub 2018-01-10.

1. Przejdź do **krok 2: Replikowanie aplikacji** > **źródła**. Po włączeniu replikacji po raz pierwszy, wybierz **+ Replikuj** w magazynie, aby włączyć replikację dla dodatkowych maszyn wirtualnych.
1. W **źródła** strony > **źródła**, wybierz serwer konfiguracji.
1. Aby uzyskać **typ maszyny**, wybierz opcję **maszyn wirtualnych** lub **maszyn fizycznych**.
1. W obszarze **vCenter/vSphere Hypervisor** wybierz serwer vCenter zarządzający hostem vSphere lub wybierz tego hosta. To ustawienie nie jest istotne, Jeśli replikujesz komputerów fizycznych.
1. Wybierz serwer przetwarzania, który będzie serwerem konfiguracji, jeśli nie utworzono żadnych dodatkowych serwerów przetwarzania. Następnie wybierz przycisk **OK**.

    ![Włącz okno źródła replikacji](./media/vmware-azure-enable-replication/enable-replication2.png)

1. Aby uzyskać **docelowej**, wybierz grupę subskrypcji i zasobów, której chcesz utworzyć maszyny wirtualne w trybie failed-over. Wybierz model wdrażania, który chcesz użyć na platformie Azure dla maszyn wirtualnych w trybie failed-over.

1. Wybierz sieć platformy Azure i podsieć, którą maszyny wirtualne Azure będą nawiązywać połączenie po włączeniu trybu failover. Sieć musi znajdować się w tym samym regionie co magazyn usługi Site Recovery.

   Wybierz **Konfiguruj teraz dla wybranych maszyn** Aby zastosować ustawienia sieci do wszystkich maszyn wirtualnych, które wybrany do ochrony. Wybierz **później skonfigurować** wybrać sieć platformy Azure na maszynę wirtualną. Jeśli nie masz sieci, należy ją utworzyć. Aby utworzyć sieć przy użyciu usługi Azure Resource Manager, wybierz pozycję **Utwórz nową**. Wybierz podsieć, jeśli ma to zastosowanie, a następnie wybierz **OK**.
   
   ![Włącz okno docelowego replikacji](./media/vmware-azure-enable-replication/enable-rep3.png)

1. Aby uzyskać **maszyn wirtualnych** > **wybierz maszyny wirtualne**, wybierz poszczególne maszyny wirtualne, które mają być replikowane. Można wybrać tylko maszyny wirtualne, dla których można włączyć replikacji. Następnie wybierz przycisk **OK**. Jeśli nie można wyświetlić lub wybrać dowolnego konkretnej maszyny wirtualnej, zobacz [maszyny źródłowej nie ma na liście w witrynie Azure portal](https://aka.ms/doc-plugin-VM-not-showing) Aby rozwiązać ten problem.

    ![Włączanie replikacji maszyn wirtualnych wybierz okno](./media/vmware-azure-enable-replication/enable-replication5.png)

1. Aby uzyskać **właściwości** > **skonfigurować właściwości**, wybierz konto, którego używa serwer przetwarzania w celu automatycznego zainstalowania usługi Site Recovery Mobility na maszynie wirtualnej. Ponadto należy wybrać typ docelowego dysku zarządzanego można replikować do na podstawie danych wzorce zmian.
10. Domyślnie wszystkie dyski źródłowej maszyny wirtualnej są replikowane. Aby wykluczyć dyski z replikacji, wyczyść **Include** pole wyboru dla wszystkich dysków, które nie mają być replikowane. Następnie wybierz przycisk **OK**. Później możesz skonfigurować dodatkowe właściwości. Dowiedz się więcej o [wykluczenie dysków](vmware-azure-exclude-disk.md).

    ![Włączanie replikacji skonfigurować okno właściwości](./media/vmware-azure-enable-replication/enable-replication6.png)

1. W **ustawień replikacji** > **Konfigurowanie ustawień replikacji**, sprawdź, czy wybrano właściwe zasady replikacji. Można zmodyfikować ustawień zasad replikacji w **ustawienia** > **zasady replikacji** > ***nazwa_zasad***  >  **Edytować ustawienia**. Zmiany, które są stosowane do zasad wymagających używania również dotyczyć replikowanych i nowych maszyn wirtualnych.
1. Włącz **spójność wielu maszyn wirtualnych** jeśli mają być zbierane z maszyn wirtualnych do grupy replikacji. Określ nazwę grupy, a następnie wybierz pozycję **OK**.

    > [!NOTE]
    >    * Maszyny wirtualne w grupie replikacji replikowane wspólnie i będą mieć wspólne punkty odzyskiwania spójne na poziomie awarii i spójny na poziomie aplikacji, podczas ich pracy awaryjnej.
    >    * Grupowania maszyn wirtualnych i serwerów fizycznych, aby odzwierciedlały obciążeń. Włączenie spójności wielu maszyn wirtualnych może wpłynąć na wydajność obciążenia. W tym tylko wtedy, gdy maszyny wirtualne są uruchomione to samo obciążenie i jest wymagana spójność.

    ![Włącz okno replikacji](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Wybierz pozycję **Włącz replikację**. Możesz śledzić postęp **włączania ochrony** zadania na **ustawienia** > **zadania** > **zadania usługi Site Recovery**. Po **Finalizuj ochronę** zadanie jest uruchamiane, maszyna wirtualna jest gotowa do trybu failover.

## <a name="view-and-manage-vm-properties"></a>Wyświetlanie właściwości maszyny wirtualnej i zarządzanie nimi

Następnie sprawdź właściwości źródłowej maszyny wirtualnej. Należy pamiętać, że nazwa maszyny Wirtualnej platformy Azure musi być zgodna z [wymagania maszyny wirtualnej platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Przejdź do **ustawienia** > **zreplikowane elementy**, a następnie wybierz maszynę wirtualną. **Essentials** strona zawiera informacje o ustawieniach i stan maszyny Wirtualnej.
1. W obszarze **Właściwości** możesz wyświetlić informacje dotyczące replikacji i trybu failover dla danej maszyny wirtualnej.
1. W **obliczenia i sieć** > **właściwości obliczania**, można zmienić wiele właściwości maszyny Wirtualnej. 

    ![Okno właściwości obliczenia i sieć](./media/vmware-azure-enable-replication/vmproperties.png)

    * Nazwa maszyny Wirtualnej platformy Azure: W razie potrzeby zmodyfikować nazwę aby spełnić wymagania platformy Azure.
    * Rozmiar docelowej maszyny Wirtualnej lub typ maszyny Wirtualnej: Domyślny rozmiar maszyny Wirtualnej jest wybierany w oparciu o kilku parametrów, które obejmują liczba dysków, liczba kart Sieciowych, liczba rdzeni procesora CPU, pamięci i dostępnych rozmiarów ról maszyn wirtualnych w miejscu docelowym regionem świadczenia usługi Azure. Usługa Azure Site Recovery wybiera pierwszy dostępny rozmiar maszyny Wirtualnej, który spełnia wszystkie kryteria. Możesz wybrać innego rozmiaru maszyny Wirtualnej, w zależności od potrzeb w dowolnym momencie przed włączeniem trybu failover. Należy pamiętać, że rozmiar dysku maszyny Wirtualnej również zależy od rozmiaru dysku źródłowego i można ją zmienić tylko po włączeniu trybu failover. Dowiedz się więcej na temat rozmiarów dysków i szybkości operacji We/Wy na [cele dotyczące skalowalności i wydajności dla dysków maszyn wirtualnych w Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Grupa zasobów: Możesz wybrać [grupy zasobów](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines), z której maszyna wirtualna staje się częścią po przejściu w tryb failover. Można zmienić to ustawienie w dowolnym momencie przed włączeniem trybu failover. Po przejściu w tryb failover w przypadku migrowania maszyny wirtualnej do innej grupy zasobów, ustawienia ochrony dla tej maszyny wirtualnej przerwanie.
    * Zestaw dostępności: Możesz wybrać [zestaw dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) Jeśli maszyna wirtualna musi być częścią po przejściu w tryb failover. Po wybraniu zestawu dostępności, pamiętać o następujących informacji:

        * Wyświetlane są tylko zestawy dostępności, które należą do określonej grupy zasobów.  
        * Maszyny wirtualne znajdujące się w różnych sieciach wirtualnych nie może być częścią tego samego zestawu dostępności.
        * Tylko maszyny wirtualne w tej samej wielkości może być częścią zestawu dostępności.
1. Można również dodać informacje dotyczące sieci docelowej, podsieci i adresu IP, która jest przypisana do maszyny Wirtualnej platformy Azure.
2. W **dysków**, można wyświetlić systemu operacyjnego i dysków z danymi, które znajdują się na maszynie Wirtualnej, która będzie replikowana.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurowanie sieci i adresy IP

Możesz ustawić docelowy adres IP. Jeśli nie podasz adresu, maszyna wirtualna w trybie failed-over używa protokołu DHCP. Jeśli ustawisz adres który nie jest dostępna w trybie failover, pracy w trybie failover nie działa. Jeśli adres jest dostępny w testowej sieci trybu failover, można użyć tego samego adresu IP docelowej do testowania trybu failover.

Liczba kart sieciowych jest zależna od rozmiaru określonego w następujący sposób określić dla docelowej maszyny wirtualnej:

- Jeśli liczba kart sieciowych na źródłowej maszynie wirtualnej jest mniejsza lub równa liczbie kart, które są dozwolone dla rozmiaru docelowej maszyny Wirtualnej, element docelowy ma taką samą liczbę kart sieciowych jako źródło.
- Jeśli liczba kart sieciowych dla źródłowej maszyny wirtualnej przekracza liczbę, która jest dozwolona dla rozmiaru docelowej maszyny Wirtualnej, używana jest maksymalny rozmiar docelowy. Na przykład jeśli źródłowa maszyna wirtualna ma dwie karty sieciowe, a rozmiar maszyny docelowej obsługuje cztery, docelowej maszyny wirtualnej ma dwie karty. Jeśli źródłowa maszyna wirtualna ma dwie karty sieciowe, ale rozmiar docelowej obsługuje tylko jedną, docelowa maszyna wirtualna ma tylko jedną kartę sieciową.
- Jeśli maszyna wirtualna ma wiele kart sieciowych, wszystkie łączą się z tą samą siecią. Ponadto staje się pierwsza karta, która jest wyświetlana na liście *domyślne* karty sieciowej na maszynie wirtualnej platformy Azure. 

### <a name="azure-hybrid-benefit"></a>Korzyść użycia hybrydowego platformy Azure

Microsoft Software Assurance klienci mogą używać korzyści użycia hybrydowego platformy Azure, aby zmniejszyć koszty licencjonowania dla komputerów z systemu Windows Server, które są migrowane na platformie Azure. Dotyczy również korzyści odzyskiwania po awarii platformy Azure. Jeśli masz uprawnienia, korzyści można przypisać do maszyny wirtualnej, która usługa Site Recovery tworzy w przypadku przejścia w tryb failover. W tym celu wykonaj następujące kroki:
1. Przejdź do **właściwości komputera i sieci** zreplikowanej maszyny wirtualnej.
2. Odpowiedzieć na pytanie, jeśli masz licencję systemu Windows Server, dzięki któremu pracujesz objęte korzyścią użycia hybrydowego platformy Azure.
3. Upewnij się, że masz licencję uprawniającą systemu Windows Server z pakietem Software Assurance, które można zastosować korzyść z maszyną wirtualną, która zostanie utworzona w trybie failover.
4. Zapisz ustawienia dla zreplikowanej maszyny wirtualnej.

Dowiedz się więcej o [korzyść użycia hybrydowego platformy Azure](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="resolve-common-issues"></a>Rozwiązywanie typowych problemów

* Każdy dysk powinien być mniejszy niż 4 TB.
* Dysk systemu operacyjnego powinien być dyskiem podstawowym, a nie dyskiem dynamicznym.
* Generacja 2/UEFI — włączone maszyn wirtualnych Rodzina systemów operacyjnych powinny być Windows, a dysk rozruchowy powinien być mniejszy niż 300 GB.

## <a name="next-steps"></a>Kolejne kroki

Po maszyny wirtualnej osiągnie stan chronionych, spróbuj [trybu failover](site-recovery-failover.md) do sprawdzenia, czy aplikacja pojawi się na platformie Azure.

* Dowiedz się, jak [wyczyścić ustawienia rejestracją i ochroną](site-recovery-manage-registration-and-protection.md) można wyłączyć replikacji.
* Dowiedz się, jak [Automatyzowanie replikacji maszyn wirtualnych przy użyciu programu Powershell](vmware-azure-disaster-recovery-powershell.md).
