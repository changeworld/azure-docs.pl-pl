---
title: Włączanie maszyn wirtualnych VMware do odzyskiwania po awarii przy użyciu usługi Azure Site Recovery
description: W tym artykule opisano, jak włączyć replikację maszyn wirtualnych VMware do odzyskiwania po awarii przy użyciu usługi Azure Site Recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 04/01/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 6547bcf2061213cd01550367171d432900693ea5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584144"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Włącz replikację na platformie Azure dla maszyn wirtualnych VMware

W tym artykule opisano sposób włączania replikacji lokalnych maszyn wirtualnych VMware (VM) na platformę Azure.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że system spełnia następujące kryteria:

- [Skonfiguruj lokalne środowisko źródłowe](vmware-azure-set-up-source.md).
- [Skonfiguruj środowisko docelowe na platformie Azure](vmware-azure-set-up-target.md).
- Przed rozpoczęciem [sprawdź wymagania i wymagania wstępne.](vmware-physical-azure-support-matrix.md) Ważne rzeczy, na które należy zwrócić uwagę, to:
  - [Obsługiwane systemy operacyjne](vmware-physical-azure-support-matrix.md#replicated-machines) dla maszyn replikowanych.
  - [Obsługa magazynu/dysku.](vmware-physical-azure-support-matrix.md#storage)
  - [Wymagania dotyczące platformy Azure,](vmware-physical-azure-support-matrix.md#azure-vm-requirements) które powinny być zgodne z komputerami lokalnymi.

### <a name="resolve-common-issues"></a>Rozwiązywanie typowych problemów

- Każdy dysk powinien być mniejszy niż 4 TB.
- Dysk systemu operacyjnego powinien być dyskiem podstawowym, a nie dynamicznym.
- W przypadku maszyn wirtualnych obsługujących UEFI generacji 2 rodzina systemów operacyjnych powinna być systemem Windows, a dysk rozruchowy powinien być mniejszy niż 300 GB.

## <a name="before-you-start"></a>Przed rozpoczęciem

Podczas replikowania maszyn wirtualnych VMware należy pamiętać o tych informacjach:

- Twoje konto użytkownika platformy Azure musi mieć pewne [uprawnienia,](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) aby włączyć replikację nowej maszyny wirtualnej na platformie Azure.
- Maszyny wirtualne VMware są odnajdyjne co 15 minut. Może upłynąć 15 minut lub więcej dla maszyn wirtualnych do wyświetlenia w witrynie Azure portal po odnajdowaniu. Po dodaniu nowego serwera vCenter lub hosta vSphere odnajdowanie może potrwać 15 minut lub dłużej.
- Może upłynąć 15 minut lub więcej dla zmian w środowisku na maszynie wirtualnej, które mają być aktualizowane w portalu. Na przykład instalacja narzędzi VMware.
- Można sprawdzić czas ostatniego odnalezionego dla maszyn wirtualnych VMware: Zobacz pole **Ostatni kontakt na** stronie **Serwery konfiguracji** dla hosta serwera vCenter/vSphere.
- Aby dodać maszyny wirtualne do replikacji bez oczekiwania na zaplanowane odnajdowanie, zaznacz serwer konfiguracji (ale go nie klikaj) i wybierz pozycję **Odśwież**.
- Po włączeniu replikacji, jeśli maszyna wirtualna jest przygotowana, serwer przetwarzania automatycznie instaluje usługę Azure Site Recovery Mobility na maszynie wirtualnej.

## <a name="enable-replication"></a>Włączanie replikacji

Przed podjęciem czynności w tej sekcji zapoznaj się z następującymi informacjami:

- Usługa Azure Site Recovery replikuje teraz bezpośrednio do dysków zarządzanych dla wszystkich nowych replikacji. Serwer przetwarzania zapisuje dzienniki replikacji na koncie magazynu pamięci podręcznej w regionie docelowym. Te dzienniki są używane do tworzenia punktów odzyskiwania w replikach dysków zarządzanych, które mają konwencję `asrseeddisk`nazewnictwa .
- Obsługa replikacji na dyskach zarządzanych programu PowerShell jest dostępna począwszy od [modułu Az.RecoveryServices w wersji 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)
- W czasie pracy awaryjnej wybrany punkt odzyskiwania jest używany do tworzenia dysku zarządzanego przez obiekt docelowy.
- Maszyny wirtualne, które zostały wcześniej skonfigurowane do replikacji do kont magazynu docelowego nie ma wpływu.
- Replikacja do kont magazynu dla nowej maszyny wirtualnej jest dostępna tylko za pośrednictwem interfejsu API reprezentacyjnego transferu stanu (REST) i programu PowerShell. Użyj interfejsu Azure REST API w wersji 2016-08-10 lub 2018-01-10 dla replikacji na konta magazynu.

Aby włączyć replikację, wykonaj następujące czynności:

1. Przejdź do **kroku 2: Replikuj źródło aplikacji** > **Source**. Po włączeniu replikacji po raz pierwszy wybierz **+Replikuj** w przechowalni, aby włączyć replikację dla dodatkowych maszyn wirtualnych.
1. Na stronie **Źródło** > **Source**wybierz serwer konfiguracji.
1. W przypadku **typu maszyny**wybierz opcję **Maszyny wirtualne** lub **Maszyny fizyczne**.
1. W obszarze **vCenter/vSphere Hypervisor** wybierz serwer vCenter zarządzający hostem vSphere lub wybierz tego hosta. To ustawienie nie jest istotne w przypadku replikowania komputerów fizycznych.
1. Wybierz serwer przetwarzania. Jeśli nie utworzono żadnych dodatkowych serwerów przetwarzania, wbudowany serwer przetwarzania serwera konfiguracji będzie dostępny w menu rozwijanym. Stan kondycji każdego serwera przetwarzania jest wskazywany zgodnie z zalecanymi limitami i innymi parametrami. Wybierz serwer procesów w dobrej kondycji. Nie [critical](vmware-physical-azure-monitor-process-server.md#process-server-alerts) można wybrać krytycznego serwera procesów. Można [rozwiązywać problemy i rozwiązywać](vmware-physical-azure-troubleshoot-process-server.md) problemy **lub** figurować [serwer procesów skalowanych w poziomie](vmware-azure-set-up-process-server-scale.md).

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="Włączanie okna źródła replikacji":::

   > [!NOTE]
   > Począwszy od [wersji 9.24,](site-recovery-whats-new.md)wprowadzane są dodatkowe alerty w celu zwiększenia alertów kondycji serwera procesów. Uaktualnij składniki odzyskiwania witryny do wersji 9.24 lub wyższej, aby wszystkie alerty zostały wygenerowane.

1. W przypadku opcji **Docelowej**wybierz grupę subskrypcji i zasobów, w której chcesz utworzyć maszyny wirtualne w trybie failed. Wybierz model wdrażania, który ma być używany na platformie Azure dla maszyn wirtualnych po awarii.
1. Wybierz sieć platformy Azure i podsieć, z którymi maszyny wirtualne platformy Azure będą łączyć się po przełączeniu awaryjnym. Sieć musi znajdować się w tym samym regionie co magazyn usługi odzyskiwania witryny.

   Wybierz **pozycję Konfiguruj teraz dla wybranych maszyn,** aby zastosować ustawienie sieciowe do wszystkich maszyn wirtualnych wybranych do ochrony. Wybierz **pozycję Konfiguruj później,** aby wybrać sieć platformy Azure na maszynę wirtualną. Jeśli nie masz sieci, musisz go utworzyć. Aby utworzyć sieć przy użyciu usługi Azure Resource Manager, wybierz pozycję **Utwórz nowy**. Wybierz podsieć, jeśli ma to zastosowanie, a następnie wybierz **przycisk OK**.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="Włącz okno docelowe replikacji":::

1. Dla **maszyn wirtualnych** > **Wybierz maszyny wirtualne**wybierz każdą maszynę wirtualną, którą chcesz replikować. Można wybrać tylko maszyny wirtualne, dla których można włączyć replikację. Następnie wybierz przycisk **OK**. Jeśli nie widzisz lub nie wybierzesz żadnej konkretnej maszyny wirtualnej, zobacz [Maszyna źródło nie jest wymieniona w witrynie Azure portal,](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) aby rozwiązać problem.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="Włącz replikację Wybierz maszyny wirtualne okno":::

1. W przypadku**właściwości Konfigurowanie** **właściwości** > wybierz konto używane przez serwer przetwarzania do automatycznego instalowania usługi Mobilności odzyskiwania lokacji na maszynie wirtualnej. Ponadto wybierz typ docelowego dysku zarządzanego do użycia do replikacji na podstawie wzorców zmian danych.
1. Domyślnie wszystkie dyski źródłowej maszyny Wirtualnej są replikowane. Aby wykluczyć dyski z replikacji, wyczyść pole wyboru **Dołącz** dla wszystkich dysków, których nie chcesz replikować. Następnie wybierz przycisk **OK**. Później możesz skonfigurować dodatkowe właściwości. [Dowiedz się więcej](vmware-azure-exclude-disk.md) o wykluczaniu dysków.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="Włącz okno właściwości konfigurowania replikacji":::

1. W **obszarze Ustawienia** > replikacji**Konfigurowanie ustawień replikacji**sprawdź, czy zaznaczono prawidłowe zasady replikacji. Ustawienia zasad replikacji można zmodyfikować w **obszarze**Zasady > **replikacji** > ustawień_Nazwa_ > zasad**Edytowania ustawień**. Zmiany zastosowane do zasad dotyczą również replikacji i nowych maszyn wirtualnych.
1. Jeśli chcesz zebrać maszyny wirtualne do grupy replikacji, włącz **spójność wielu maszyn wirtualnych**. Określ nazwę grupy, a następnie wybierz przycisk **OK**.

   > [!NOTE]
   > - Maszyny wirtualne w grupie replikacji replikują się razem i mają wspólne punkty odzyskiwania spójne z awariami i spójne z aplikacjami po przełączeniu w tryb fail over.
   > - Zbieraj maszyny wirtualne i serwery fizyczne razem, aby dublować obciążenia. Włączenie spójności wielu maszyn wirtualnych może mieć wpływ na wydajność obciążenia. Należy to zrobić tylko wtedy, gdy maszyny wirtualne są uruchomione tego samego obciążenia i potrzebujesz spójności.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="Włącz okno replikacji":::

1. Wybierz pozycję **Włącz replikację**. Postęp zadania **Włącz ochronę** można śledzić w obszarze Zadania**odzyskiwania witryny**Zadania**zadań zadań** >  **zadań zadań.** >  Po uruchomieniu zadania **Finalize Protection** maszyna wirtualna jest gotowa do pracy awaryjnej.

## <a name="view-and-manage-vm-properties"></a>Wyświetlanie właściwości maszyny wirtualnej i zarządzanie nimi

Następnie sprawdź właściwości źródłowej maszyny wirtualnej. Należy pamiętać, że nazwa maszyny wirtualnej platformy Azure musi być zgodna z [wymaganiami maszyny wirtualnej platformy Azure.](vmware-physical-azure-support-matrix.md#replicated-machines)

1. Przejdź do **pozycji Ustawienia** > **replikowane elementy**, a następnie wybierz maszynę wirtualną. Na stronie **Essentials** są wyświetlane informacje o ustawieniach i stanie maszyny Wirtualnej.
1. W **właściwościach**można wyświetlić informacje o replikacji i pracy awaryjnej dla maszyny Wirtualnej.
1. We**właściwościach obliczeń obliczeniowych** **i sieciowych** > można zmienić wiele właściwości maszyny Wirtualnej.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="Okno Właściwości obliczeniowe i sieciowe":::

   - **Nazwa maszyny Wirtualnej platformy Azure:** W razie potrzeby zmodyfikuj nazwę, aby spełnić wymagania platformy Azure.
   - **Docelowy rozmiar maszyny Wirtualnej lub typ maszyny Wirtualnej:** Domyślny rozmiar maszyny Wirtualnej jest wybierany na podstawie parametrów, które obejmują liczbę dysków, liczbę kart sieciowych, liczbę rdzeni procesora CPU, pamięć i dostępne rozmiary ról maszyny Wirtualnej w docelowym regionie platformy Azure. Usługa Azure Site Recovery wybiera pierwszy dostępny rozmiar maszyny Wirtualnej, który spełnia wszystkie kryteria. Możesz wybrać inny rozmiar maszyny Wirtualnej w zależności od potrzeb w dowolnym momencie przed przejściem awaryjnym. Rozmiar dysku maszyny Wirtualnej jest również oparty na rozmiarze dysku źródłowego i można go zmienić tylko po przemiń awaryjnych. Dowiedz się więcej o rozmiarach dysków i szybkościach we/wy w przypadku [obiektów skalowalności i wskaźników wydajności dysków maszyn wirtualnych w systemie Windows](/azure/virtual-machines/windows/disk-scalability-targets).
   - **Grupa zasobów:** Można wybrać [grupę zasobów,](/azure/azure-resource-manager/management/overview#resource-groups)z której maszyna wirtualna staje się częścią posta trybu failover. To ustawienie można zmienić w dowolnym momencie przed przejściem awaryjnym. Po przełączeniu w tryb failover, jeśli zmigrujesz maszynę wirtualną do innej grupy zasobów, ustawienia ochrony dla tej maszyny wirtualnej zostaną przerwane.
   - **Zestaw dostępności:** Można wybrać [zestaw dostępności,](/azure/virtual-machines/windows/tutorial-availability-sets) jeśli maszyna wirtualna musi być częścią pracy awaryjnej postu. Po wybraniu zestawu dostępności należy pamiętać o następujących informacjach:
     - Wymienione są tylko zestawy dostępności, które należą do określonej grupy zasobów.
     - Maszyny wirtualne, które znajdują się w różnych sieciach wirtualnych nie może być częścią tego samego zestawu dostępności.
     - Tylko maszyny wirtualne o tym samym rozmiarze mogą być częścią zestawu dostępności.

1. Można również dodać informacje o sieci docelowej, podsieci i adres ip, który jest przypisany do maszyny Wirtualnej platformy Azure.
1. W **programach Dyski**można wyświetlić system operacyjny i dyski danych na maszynie wirtualnej, które zostaną zreplikowane.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurowanie sieci i adresów IP

Docelowy adres IP można ustawić:

- Jeśli adres nie zostanie podasz, maszyna wirtualna po awarii użyje dhcp.
- Jeśli ustawisz adres, który nie jest dostępny w pracy awaryjnej, praca awaryjna nie działa.
- Jeśli adres jest dostępny w sieci trybu failover testu, można użyć tego samego docelowego adresu IP do testowania pracy awaryjnej.

Liczba kart sieciowych jest podyktowana rozmiarem określonym dla docelowej maszyny wirtualnej w następujący sposób:

- Jeśli liczba kart sieciowych na źródłowej maszynie wirtualnej jest mniejsza lub równa liczbie kart, które są dozwolone dla rozmiaru docelowej maszyny Wirtualnej, obiekt docelowy ma taką samą liczbę kart jak źródło.
- Jeśli liczba kart dla źródłowej maszyny wirtualnej przekracza liczbę dozwoloną dla rozmiaru docelowej maszyny Wirtualnej, używana jest maksymalna wartość docelowa rozmiaru. Na przykład jeśli źródłowa maszyna wirtualna ma dwie karty sieciowe, a rozmiar docelowej maszyny wirtualnej obsługuje cztery, docelowa maszyna wirtualna ma dwie karty. Jeśli źródłowa maszyna wirtualna ma dwie karty, ale rozmiar docelowy obsługuje tylko jedną, docelowa maszyna wirtualna ma tylko jedną kartę.
- Jeśli maszyna wirtualna ma wiele kart sieciowych, wszystkie łączą się z tą samą siecią. Ponadto pierwsza karta, która jest wyświetlana na liście staje się domyślną kartą sieciową na maszynie wirtualnej platformy Azure.

### <a name="azure-hybrid-benefit"></a>Korzyść użycia hybrydowego platformy Azure

Klienci programu Microsoft Software Assurance mogą korzystać z korzyści hybrydowych platformy Azure, aby zaoszczędzić na kosztach licencjonowania komputerów z systemem Windows Server, które są migrowane na platformę Azure. Korzyści dotyczą również odzyskiwania po awarii platformy Azure. Jeśli kwalifikujesz się, możesz przypisać korzyść do maszyny wirtualnej, którą program Site Recovery tworzy, jeśli nastąpi przeinaczenie awaryjne.

1. Przejdź do **właściwości Komputer i sieć** replikowanej maszyny wirtualnej.
1. Odpowiedz na pytanie, czy masz licencję systemu Windows Server, która sprawia, że kwalifikujesz się do korzystania z usługi Azure Hybrid Benefit.
1. Upewnij się, że masz kwalifikującą się licencję systemu Windows Server z pakietem Software Assurance, której można użyć do zastosowania korzyści do maszyny wirtualnej, która zostanie utworzona przy pracy awaryjnej.
1. Zapisz ustawienia replikowanej maszyny wirtualnej.

[Dowiedz się więcej](https://azure.microsoft.com/pricing/hybrid-benefit/) o korzyściach hybrydowych platformy Azure.

## <a name="next-steps"></a>Następne kroki

Gdy maszyna wirtualna osiągnie stan chroniony, spróbuj pracy [awaryjnej,](site-recovery-failover.md) aby sprawdzić, czy aplikacja jest wyświetlana na platformie Azure.

- [Dowiedz się więcej](site-recovery-manage-registration-and-protection.md) o czyszczeniu ustawień rejestracji i ochrony w celu wyłączenia replikacji.
- [Dowiedz się więcej](vmware-azure-disaster-recovery-powershell.md) o automatyzacji replikacji maszyn wirtualnych przy użyciu programu PowerShell.
