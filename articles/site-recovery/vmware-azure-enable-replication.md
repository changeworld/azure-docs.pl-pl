---
title: Włączanie replikacji maszyn wirtualnych programu VMware na potrzeby odzyskiwania po awarii programu VMware na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób włączania replikacji maszyn wirtualnych programu VMware do odzyskiwania po awarii na platformie Azure przy użyciu usługi Azure Site Recovery.
author: mayurigupta13
ms.service: site-recovery
ms.date: 1/29/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 85c5947fa32751bd8381d6a1d59e91ba2026f5f0
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55822786"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Włącz replikację na platformę Azure dla maszyn wirtualnych VMware


W tym artykule opisano sposób włączania replikacji lokalnych maszyn wirtualnych programu VMware do platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz:

1.  [Konfigurowanie środowiska źródłowego w środowisku lokalnym](vmware-azure-set-up-source.md).
2.  [Konfigurowanie środowiska docelowego na platformie Azure](vmware-azure-set-up-target.md).


## <a name="before-you-start"></a>Przed rozpoczęciem
Podczas replikowania maszyn wirtualnych programu VMware:

* Twoje konto platformy Azure użytkownika musi mieć pewne [uprawnienia](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) Aby włączyć replikację nowej maszyny wirtualnej na platformie Azure.
* Maszyny wirtualne VMware są odnajdywane co 15 minut. Może potrwać 15 minut lub dłużej, aby były widoczne w witrynie Azure portal po odnalezieniu. Podobnie odnajdywanie może potrwać 15 minut lub dłużej, po dodaniu nowego vCenter server lub hoście vSphere.
* Zmiany środowiska na maszynie wirtualnej (na przykład instalacji narzędzi VMware) może zająć 15 minut lub dłużej, należy zaktualizować w portalu.
* Możesz sprawdzić czas ostatniego odnalezienia maszyn wirtualnych programu VMware w **ostatni kontakt** pole na serwerze vCenter/hoście vSphere, **serwery konfiguracji** strony.
* W celu dodania maszyn do replikacji, nie czekając na zaplanowane odnajdowanie, wyróżnij serwer konfiguracji (nie należy kliknąć ją) i kliknij przycisk **Odśwież** przycisku.
* Po włączeniu replikacji, jeśli maszyna jest przygotowana, serwer przetwarzania automatycznie instaluje usługę mobilności na nim.


## <a name="enable-replication"></a>Włączanie replikacji

1. Kliknij przycisk **krok 2: Replikowanie aplikacji** > **źródła**. Po włączeniu replikacji po raz pierwszy kliknij pozycję **+ Replikuj** w magazynie, aby włączyć replikację dla dodatkowych maszyn.
2. W **źródła** strony > **źródła**, wybierz serwer konfiguracji.
3. W **typ maszyny**, wybierz opcję **maszyn wirtualnych** lub **maszyn fizycznych**.
4. W obszarze **vCenter/vSphere Hypervisor** wybierz serwer vCenter zarządzający hostem vSphere lub wybierz tego hosta. To ustawienie nie jest istotne, Jeśli replikujesz maszyny fizyczne.
5. Wybierz serwer przetwarzania, który będzie to nazwa serwera konfiguracji, jeśli nie utworzono żadnych dodatkowych serwerów przetwarzania. Następnie kliknij przycisk **OK**.

    ![Włączanie źródła replikacji](./media/vmware-azure-enable-replication/enable-replication2.png)

6. W **docelowej**, wybierz subskrypcję i grupę zasobów, w której chcesz utworzyć maszyny wirtualne w trybie failed-over. Wybierz model wdrażania, który chcesz użyć na platformie Azure dla maszyn wirtualnych w trybie failed-over.

7. Wybierz konto usługi Azure Storage, którego chcesz użyć na potrzeby replikacji danych. 

    > [!NOTE]

    >   * Możesz wybrać — wersja premium lub konta magazynu w warstwie standardowa. Jeśli wybierzesz konto usługi premium, należy określić dodatkowe konto magazynu dla dzienników trwającej replikacji. Konta muszą być w tym samym regionie co magazyn usługi Recovery Services.
    >   * Jeśli chcesz użyć innego konta magazynu, możesz to zrobić [utworzyć](../storage/common/storage-create-storage-account.md). Aby utworzyć konto magazynu przy użyciu usługi Resource Manager, kliknij przycisk **Utwórz nową**. 

8. Wybierz sieć platformy Azure i podsieć, z którą nawiążą połączenie maszyny wirtualne Azure, gdy zostaną uruchomione po przejściu do trybu failover. Sieć musi znajdować się w tym samym regionie co magazyn Usług odzyskiwania. Wybierz opcję **Konfiguruj teraz dla wybranych maszyn**, aby zastosować ustawienia sieci do wszystkich maszyn wybranych do ochrony. Wybierz opcję **Konfiguruj później**, aby wybrać sieć platformy Azure dla poszczególnych maszyn. Jeśli nie masz sieci, należy ją utworzyć. Aby utworzyć sieć przy użyciu usługi Resource Manager, kliknij przycisk **Utwórz nową**. Wybierz podsieć, jeśli ma to zastosowanie, a następnie kliknij przycisk **OK**.

    ![Włącz ustawienie obiektu docelowego replikacji](./media/vmware-azure-enable-replication/enable-rep3.png)
9. W pozycji **Maszyny wirtualne** > **Wybierz maszyny wirtualne** wybierz każdą maszynę, którą chcesz replikować. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. Następnie kliknij przycisk **OK**. Jeśli nie możesz wyświetlić/wybrać żadnej konkretnej maszyny wirtualnej, kliknij [tutaj](https://aka.ms/doc-plugin-VM-not-showing), aby rozwiązać ten problem.

    ![Włączanie replikacji wybierz maszyn wirtualnych](./media/vmware-azure-enable-replication/enable-replication5.png)
10. W **właściwości** > **skonfigurować właściwości**, wybierz konto używane przez serwer przetwarzania, aby automatycznie zainstalować usługi mobilności na maszynie.  
11. Domyślnie wszystkie dyski są replikowane. Aby wykluczyć dyski z replikacji, kliknij przycisk **wszystkie dyski** i wyczyść wszystkie dyski, które nie mają być replikowane.  Następnie kliknij przycisk **OK**. Później możesz skonfigurować dodatkowe właściwości. [Dowiedz się więcej](vmware-azure-exclude-disk.md) informacji na temat wykluczania dysków.

    ![Włączanie replikacji, skonfiguruj właściwości](./media/vmware-azure-enable-replication/enable-replication6.png)

12. W obszarze **Ustawienia replikacji** > **Konfigurowanie ustawień replikacji** sprawdź, czy wybrano właściwe zasady replikacji. Można zmodyfikować ustawień zasad replikacji w **ustawienia** > **zasady replikacji** > (nazwa zasad) > **edytowanie ustawień**. Zmiany dotyczą zasady, ale także dotyczą replikowanych i nowych maszyn.
13. Włącz **spójność wielu maszyn wirtualnych** jeśli mają być zbierane z maszyn w grupie replikacji. Określ nazwę grupy, a następnie kliknij przycisk **OK**. 

    > [!NOTE]

    >    * Maszyny w grupie replikacji replikowane wspólnie i będą mieć wspólne punkty odzyskiwania spójne na poziomie awarii i spójny na poziomie aplikacji, podczas ich pracy awaryjnej.
    >    * Grupowania maszyn wirtualnych i serwerów fizycznych, aby odzwierciedlały obciążeń. Włączenie spójności wielu maszyn wirtualnych może wpłynąć na wydajność obciążenia. Użyj tylko wtedy, gdy maszyn są to samo obciążenie i jest wymagana spójność.

    ![Włączanie replikacji](./media/vmware-azure-enable-replication/enable-replication7.png)
14. Kliknij pozycję **Włącz replikację**. Możesz śledzić postęp zadania **Włącz ochronę** w pozycji **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.



## <a name="view-and-manage-vm-properties"></a>Wyświetlanie właściwości maszyny wirtualnej i zarządzanie nimi

Następnie możesz sprawdzić właściwości maszyny źródłowej. Należy pamiętać, że nazwa maszyny Wirtualnej platformy Azure musi być zgodna z [wymagania maszyny wirtualnej platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Kliknij przycisk **ustawienia** > **zreplikowane elementy** >, a następnie wybierz maszynę. **Essentials** strona zawiera informacje o ustawieniach komputera i stanu.
2. W obszarze **Właściwości** możesz wyświetlić informacje dotyczące replikacji i trybu failover dla danej maszyny wirtualnej.
3. W **obliczenia i sieć** > **właściwości obliczania**, można zmienić wiele propoerties maszyny Wirtualnej:
* Maszyna wirtualna platformy Azure name - zmodyfikuj nazwę aby spełniać wymagania dotyczące usługi Azure, jeśli to konieczne
* Rozmiar docelowej maszyny Wirtualnej lub typ — domyślny rozmiar maszyny Wirtualnej zostanie wybrany na podstawie źródła rozmiar maszyny Wirtualnej. Możesz wybrać innego rozmiaru maszyny Wirtualnej, w oparciu o potrzeby dowolnym momencie przed włączeniem trybu failover. Należy pamiętać, że rozmiar dysku maszyny Wirtualnej również zależy od rozmiaru dysku źródłowego i może być tylko zmieniane po pracy awaryjnej. Dowiedz się więcej o [standardowa](../virtual-machines/windows/disks-standard-ssd.md#scalability-and-performance-targets) i [Premium](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets) rozmiarów i operacje We/Wy dysku.

    ![Właściwości sieci i obliczeń](./media/vmware-azure-enable-replication/vmproperties.png)

*  Grupy zasobów — możesz wybrać [grupy zasobów](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) z którym maszynę staje się częścią po przejściu w tryb failover. Można zmienić tego ustawienia, ilekroć przed włączeniem trybu failover. Po przejściu w tryb failover w przypadku migrowania komputera do innej grupy zasobów, ustawienia ochrony dla tego podziału maszyny.
* Zestaw dostępności — możesz wybrać [zestaw dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) Jeśli komputerze musi być częścią po przejściu w tryb failover. Podczas zaznaczania zestawie dostępności, należy pamiętać, że:

    * Wyświetlane są tylko zestawy dostępności należących do określonej grupy zasobów.  
    * Maszyny z różnych sieci wirtualnych nie może być częścią tego samego zestawu dostępności.
    * Tylko maszyny wirtualne w tej samej wielkości może być częścią zestawu dostępności.
4. Można również wyświetlić i dodać informacje dotyczące sieci docelowej, podsieci i adresu IP przypisywanego do maszyny Wirtualnej platformy Azure.
5. W **dysków**, można wyświetlić systemu operacyjnego i dysków z danymi, które znajdują się na maszynę Wirtualną do replikacji.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurowanie sieci i adresy IP

- Możesz ustawić docelowy adres IP. Jeśli nie podasz adresu, maszyna w trybie failed-over używa protokołu DHCP. Jeśli ustawisz adres który nie jest dostępna w trybie failover, pracy w trybie failover nie działa. Jeśli adres jest dostępny w testowej sieci trybu failover, ten sam docelowy adres IP może służyć do testowania trybu failover.
- Liczba kart sieciowych zależy od rozmiaru określonego dla docelowej maszyny wirtualnej, zgodnie z poniższym:
    - Jeśli liczba kart sieciowych na maszynie źródłowej jest mniejsza niż lub równa liczbie kart sieciowych dozwolonych dla rozmiaru maszyny docelowej, następnie element docelowy ma taką samą liczbę kart sieciowych jako źródło.
    - Jeśli liczba kart sieciowych dla źródłowej maszyny wirtualnej przekracza liczbę dozwolonych kart sieciowych dla rozmiaru maszyny docelowej, zostanie użyta maksymalna liczba kart dla rozmiaru maszyny docelowej.
    Na przykład jeśli maszyna źródłowa ma dwie karty sieciowe, a rozmiar maszyny docelowej obsługuje cztery, komputer docelowy ma dwie karty sieciowe. Jeśli maszyna źródłowa ma dwie karty sieciowe, ale rozmiar docelowej obsługuje tylko jedną, komputer docelowy ma tylko jedną kartę sieciową.
    - Jeśli maszyna wirtualna ma wiele kart sieciowych, wszystkie łączą się z tą samą siecią. Ponadto staje się pierwszy z nich wyświetlane na liście *domyślne* karty sieciowej na maszynie wirtualnej platformy Azure.

### <a name="azure-hybrid-benefit"></a>Korzyść użycia hybrydowego platformy Azure

Microsoft Software Assurance klienci mogą używać korzyści użycia hybrydowego platformy Azure, aby zapisać o licencjonowaniu kosztów dla maszyny z systemem Windows Server, które są migrowane do usługi Azure lub korzystanie z systemu Azure w celu odzyskiwania po awarii. Jeśli masz prawo do korzystania z korzyści użycia hybrydowego platformy Azure, można określić, że maszyny wirtualnej przypisane ta korzyść jest tworzonych przez usługę Azure Site Recovery w przypadku przejścia w tryb failover. W tym celu:
- Przejdź do sekcji właściwości obliczenia i sieć zreplikowanej maszyny wirtualnej.
- Odpowiedz na pytanie, z pytaniem, jeśli masz licencję na system Windows Server, dzięki któremu pracujesz objęte korzyścią użycia hybrydowego platformy Azure.
- Zaznacz pole wyboru, aby upewnić się, że masz licencję uprawniającą systemu Windows Server z pakietem Software Assurance, którym można zastosować korzyść użycia hybrydowego platformy Azure na komputerze, który zostanie utworzony w trybie failover.
- Zapisz ustawienia dla replikowanej maszyny.

Dowiedz się więcej o [korzyść użycia hybrydowego platformy Azure](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="common-issues"></a>Typowe problemy

* Każdy dysk powinien być mniejszy niż 4 TB.
* Dysk systemu operacyjnego powinien być dyskiem podstawowym, a nie dyskiem dynamicznym.
* Generacja 2/UEFI — włączone maszyn wirtualnych Rodzina systemów operacyjnych powinny być Windows, a dysk rozruchowy powinien być mniejszy niż 300 GB.

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu ochrony i maszyny osiągnęła stanu chronionego, możesz spróbować [trybu failover](site-recovery-failover.md) do sprawdzenia, czy aplikacja pojawi się na platformie Azure lub nie.

* Dowiedz się, jak [wyczyścić ustawienia rejestracją i ochroną](site-recovery-manage-registration-and-protection.md) można wyłączyć replikacji.
* Dowiedz się, jak [Automatyzowanie replikacji maszyn wirtualnych przy użyciu programu Powershell](vmware-azure-disaster-recovery-powershell.md)
