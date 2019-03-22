---
title: Przechodzenie do trybu failover i powrót po awarii maszyn wirtualnych VMware i serwerów fizycznych podczas odzyskiwania po awarii na platformie Azure za pomocą usługi Site Recovery | Microsoft Docs
description: Dowiedz się, jak wprowadzić maszyny wirtualne VMware i fizyczne serwery w tryb failover na platformie Azure i przywrócić je po awarii do lokacji lokalnej podczas odzyskiwania po awarii na platformie Azure za pomocą usługi Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 68f1c5156f4c12af33e6088d862fc12d98021fd4
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310221"
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>Przechodzenie do trybu failover i powrót po awarii maszyn wirtualnych VMware i fizycznych serwerów replikowanych na platformie Azure

W tym samouczku omówiono sposób wprowadzania maszyny wirtualnej VMware w tryb failover na platformie Azure. Po wprowadzeniu maszyny w tryb failover można przywrócić ją po awarii do lokacji lokalnej, gdy ta będzie znów dostępna. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdzanie właściwości maszyny VMware pod kątem zgodności z wymaganiami platformy Azure
> * Przełączanie do trybu failover na platformie Azure
> * Tworzenie serwera przetwarzania i głównego serwera docelowego na potrzeby powrotu po awarii
> * Ponowne włączanie ochrony maszyn wirtualnych platformy Azure w lokacji lokalnej
> * Przechodzenie w tryb failover z platformy Azure do lokacji lokalnej
> * Ponowne włączanie ochrony lokalnych maszyn wirtualnych w celu ponownego rozpoczęcia replikacji na platformie Azure

>[!NOTE]
>Samouczki mają za zadanie przedstawić najprostszą ścieżkę wdrożenia dla scenariusza. Jeśli to możliwe, używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Jeśli chcesz lepiej poznać kroki testowego przełączania w tryb failover, przeczytaj [How To Guide](site-recovery-failover.md) (Przewodnik z instrukcjami).

Jest to piąty samouczek z tej serii. Założono w nim, że zostały już wykonane zadania z poprzednich samouczków.

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnego wdrożenia oprogramowania VMware](vmware-azure-tutorial-prepare-on-premises.md)
3. [Konfigurowanie odzyskiwania po awarii](vmware-azure-tutorial.md)
4. [Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)
5. Oprócz powyższych kroków, warto [zapoznać się z architekturą](vmware-azure-architecture.md) scenariusza odzyskiwania po awarii.

## <a name="failover-and-failback"></a>Praca w trybie failover i powrót po awarii

Przełączanie do trybu failover i powrót po awarii odbywa się w czterech etapach:

1. **Przełączenie do trybu failover na platformie Azure**: wprowadzenie maszyn w tryb failover z lokacji lokalnej do platformy Azure.
2. **Ponowne włączanie ochrony maszyn wirtualnych na platformie Azure**: ponowne włączenie ochrony maszyn wirtualnych platformy Azure w celu rozpoczęcia replikacji do lokalnych maszyn wirtualnych VMware. Lokalna maszyna wirtualna jest wyłączana na czas ponownego włączania ochrony. Pomaga to zapewnić spójność danych podczas replikacji.
3. **Przełączenie do trybu failover w lokacji lokalnej**: uruchomienie trybu failover w celu powrotu po awarii z platformy Azure.
4. **Ponowne włączanie ochrony lokalnych maszyn wirtualnych**: po powrocie danych po awarii należy ponownie włączyć ochronę lokalnych maszyn wirtualnych, do których powrócono po awarii, aby rozpocząć replikowanie ich do platformy Azure.

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Sprawdź właściwości maszyny wirtualnej i upewnij się, że maszyna wirtualna spełnia [wymagania platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. W obszarze **Chronione elementy** kliknij kolejno pozycje **Zreplikowane elementy** > Maszyna wirtualna.

2. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.

3. W obszarze **Obliczenia i sieć** można zmodyfikować nazwę platformy Azure, grupę zasobów, rozmiar docelowy, [zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) i ustawienia dysku zarządzanego

4. Możesz wyświetlać i modyfikować ustawienia sieciowe, w tym sieć/podsieć, w której zlokalizowana będzie maszyna wirtualna na platformie Azure po wejściu w tryb failover, oraz adres IP, który będzie do niej przypisany.

5. W obszarze **Dyski** można znaleźć informacje dotyczące systemu operacyjnego i dysków danych maszyny wirtualnej.

## <a name="run-a-failover-to-azure"></a>Przełączanie do trybu failover na platformie Azure

1. W obszarze **Ustawienia** > **Zreplikowane elementy** kliknij kolejno pozycje maszyna wirtualna > **Tryb failover**.

2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, do którego przełączenie w tryb failover ma zostać wykonane. Możesz użyć jednej z następujących opcji:
   - **Najnowszy**: ta opcja najpierw przetwarza wszystkie dane wysyłane do usługi Site Recovery. Zapewnia najniższą wartość celu puntu odzyskiwania, ponieważ maszyna wirtualna platformy Azure utworzona po przejściu do trybu failover zawiera wszystkie dane, które zostały zreplikowane w usłudze Site Recovery do momentu włączenia trybu failover.
   - **Najnowszy przetworzony**: ta opcja wprowadza maszynę wirtualną w tryb failover z użyciem najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza maszynę wirtualną w tryb failover z użyciem najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery.
   - **Niestandardowy**: umożliwia określenie punktu odzyskiwania.

3. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**, aby spróbować przeprowadzić zamknięcie źródłowych maszyn wirtualnych przed włączeniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, którego przeprowadzenie zajmuje około 8–10 minut. **Dłuższy czas testowego przełączania w tryb failover** może występować w przypadku maszyn wirtualnych VMware korzystających z usługi mobilności w wersji starszej niż 9,8, serwerów fizycznych, maszyn wirtualnych VMware z systemem Linux, maszyn wirtualnych funkcji Hyper-V chronionych jako serwery fizyczne, maszyn wirtualnych VMware, które nie mają włączonej usługi DHCP, oraz maszyn wirtualnych VMware, które nie mają następujących sterowników rozruchowych: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **Nie anuluj trybu failover, który jest w toku**: Przed rozpoczęciem pracy w trybie failover zatrzymywana jest replikacja maszyny wirtualnej.
> Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

## <a name="connect-to-failed-over-virtual-machine-in-azure"></a>Nawiązywanie połączenia z maszyną wirtualną przełączoną w tryb failover na platformie Azure

1. Jeśli chcesz nawiązać połączenie z maszynami wirtualnymi platformy Azure przy użyciu protokołu RDP/SSH po przejściu do trybu failover, upewnij się, że zostały spełnione wymagania podsumowane w [tej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) tabeli.
2. Po przełączeniu do trybu failover przejdź do maszyny wirtualnej, a następnie zweryfikuj ją, nawiązując z nią [połączenie](../virtual-machines/windows/connect-logon.md).
3. Po weryfikacji kliknij pozycję **Zatwierdź**, aby sfinalizować punkt odzyskiwania maszyny wirtualnej po przełączeniu do trybu failover. Po zatwierdzeniu wszystkie inne dostępne punkty odzyskiwania zostaną usunięte. Na tym kończy się działanie trybu failover.

>[!TIP]
> Opcja **Zmień punkt odzyskiwania** pomaga wybrać inny punkt odzyskiwania po przełączeniu do trybu failover, jeśli maszyna wirtualna przełączona w tryb failover nie jest zadowalająca. Po **zatwierdzeniu** ta opcja nie będzie już dostępna.

Wykonaj czynności opisane [tutaj](site-recovery-failover-to-azure-troubleshoot.md), aby rozwiązać wszystkie problemy z łącznością po przejściu do trybu failover.

## <a name="preparing-for-reprotection-of-azure-vm"></a>Przygotowywanie do ponownego włączenia ochrony maszyny wirtualnej platformy Azure

- W tym celu możesz użyć lokalnego serwera przetwarzania (wbudowanego serwera przetwarzania), który jest automatycznie instalowany na serwerze konfiguracji, **jeśli masz połączenie Azure ExpressRoute**.

> [!IMPORTANT]
> Jeśli masz połączenie sieci VPN między środowiskiem lokalnym i platformą Azure, musisz skonfigurować maszynę wirtualną platformy Azure jako serwer przetwarzania na potrzeby ponownego włączania ochrony i powrotu po awarii. Aby skonfigurować serwer przetwarzania na platformie Azure, postępuj według instrukcji opisanych w [tym artykule](vmware-azure-set-up-process-server-azure.md).

Więcej informacji na temat wymagań wstępnych dotyczących ponownego włączania ochrony i powrotu po awarii zawiera ta [sekcja](vmware-azure-reprotect.md##before-you-begin). 

### <a name="configure-the-master-target-server"></a>Konfigurowanie głównego serwera docelowego

Główny serwer docelowy służy do odbierania i obsługi danych replikacji podczas powrotu po awarii z platformy Azure. Domyślnie jest dostępny na lokalnym serwerze konfiguracji. W tym samouczku użyjemy domyślnego głównego serwera docelowego.

>[!NOTE]
>Ochrona maszyny wirtualnej z systemem Linux wymaga utworzenia oddzielnego głównego serwera docelowego. [Kliknij tutaj](vmware-azure-install-linux-master-target.md), aby dowiedzieć się więcej.

Jeśli maszyna wirtualna jest na **hoście ESXi zarządzanym przez serwer vCenter**, główny serwer docelowy musi mieć dostęp do magazynu danych maszyny wirtualnej, aby zapisać zreplikowane dane na dyskach maszyny wirtualnej. Upewnij się, że magazyn maszyny wirtualnej jest zainstalowany na hoście głównego serwera docelowego z dostępem do odczytu/zapisu.

Jeśli maszyna wirtualna jest na **hoście ESXi, który nie jest zarządzany przez serwer vCenter**, usługa Site Recovery utworzy nową maszynę wirtualną podczas ponownego włączania ochrony. Maszyna wirtualna jest tworzona na tym hoście ESX, na którym utworzono główny serwer docelowy.
Dysk twardy maszyny wirtualnej musi znajdować się w magazynie danych dostępnym dla hosta, na którym uruchomiony jest główny serwer docelowy.

Jeśli maszyna wirtualna **nie używa serwera vCenter**, należy najpierw ukończyć odnajdowanie hosta, na którym jest uruchomiony główny serwer docelowy, a dopiero potem ponownie włączyć ochronę maszyny. Dotyczy to również powrotu po awarii fizycznych serwerów. Innym rozwiązaniem, jeśli istnieje lokalna maszyna wirtualna, jest usunięcie jej przed powrotem po awarii. W takim przypadku podczas powrotu po awarii zostanie utworzona nowa maszyna wirtualna na tym samym hoście ESX, na którym znajduje się główny serwer docelowy. W przypadku powrotu po awarii do innej lokalizacji dane są odzyskiwane do tego samego magazynu danych i tego samego hosta ESX, który jest używany przez lokalny główny serwer docelowy.

Na głównym serwerze docelowym nie można używać narzędzia Storage vMotion. W przypadku jego użycia powrót po awarii nie będzie możliwy, ponieważ dyski nie będą dostępne. Wyklucz główne serwery docelowe z listy vMotion.

>[!Warning]
>Użycie innego głównego serwera docelowego do ponownego włączenia ochrony grupy replikacji spowoduje, że serwer nie będzie mógł zapewnić wspólnego punktu w czasie.

## <a name="reprotect-azure-vms"></a>Ponowne włączanie ochrony maszyn wirtualnych Azure

Ponowne włączanie ochrony maszyny wirtualnej platformy Azure prowadzi do replikacji danych do maszyny wirtualnej w środowisku lokalnym. Jest to obowiązkowy krok przed wykonaniem przełączenia do trybu failover z platformy Azure do lokalnych maszyn wirtualnych. Wykonaj poniższe otrzymane instrukcje, aby ponownie włączyć ochronę.

1. W obszarze **Ustawienia** > **Zreplikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną przełączoną w tryb failover, a następnie kliknij pozycję **Włącz ponownie ochronę**.
2. W obszarze **Włącz ponownie ochronę** sprawdź, czy wybrano opcję **Z platformy Azure do serwera lokalnego**.
3. Określ lokalny główny serwer docelowy oraz serwer przetwarzania.
4. W obszarze **Magazyn danych** wybierz główny docelowy magazyn danych, do którego chcesz odzyskać dyski w środowisku lokalnym. Jeśli maszyna wirtualna została usunięta, nowe dyski zostaną utworzone w tym magazynie danych. To ustawienie jest ignorowane, jeśli dyski już istnieją, ale określenie wartości jest wymagane.
5. Wybierz główny docelowy dysk przechowywania. Zasady powrotu po awarii są wybierane automatycznie.
6. Kliknij przycisk **OK**, aby rozpocząć ponowne włączanie ochrony. Zadanie zaczyna replikować maszynę wirtualną z platformy Azure do lokacji lokalnej. Na karcie **Zadania** można śledzić postęp.
7. Kiedy stan maszyny wirtualnej w elemencie **Zreplikowane elementy** zmieni się na **Chronione**, maszyna jest gotowa do trybu failover na serwerze lokalnym.

> [!NOTE]
> Maszyna wirtualna platformy Azure może zostać odzyskana do istniejącej maszyny wirtualnej w środowisku lokalnym lub w innej lokalizacji. Przeczytaj [ten artykuł](concepts-types-of-failback.md), aby dowiedzieć się więcej.

## <a name="run-a-failover-from-azure-to-on-premises"></a>Przechodzenie w tryb failover z platformy Azure do lokacji lokalnej

Aby zreplikować maszynę z powrotem do serwera lokalnego, używane są zasady powrotu po awarii. Są one tworzone automatycznie podczas tworzenia zasad replikacji na platformie Azure:

- Zasady zostaną automatycznie skojarzone z serwerem konfiguracji.
- Nie można ich modyfikować.
- Wartości zasad to:
    - Próg celu punktu odzyskiwania = 15 minut
    - Przechowywanie punktów odzyskiwania = 24 godziny
    - Częstotliwość wykonywania migawek na poziomie aplikacji = 60 minut

Uruchom tryb failover w następujący sposób:

1. Na stronie **Zreplikowane elementy** kliknij prawym przyciskiem myszy maszynę, a następnie wybierz opcję **Praca w trybie failover**.
2. W obszarze **Potwierdź tryb failover** sprawdź, czy wybrano kierunek trybu failover Z platformy Azure.
    ![kierunek trybu failover](media/vmware-azure-tutorial-failover-failback/failover-direction.PNG)
3. Wybierz punkt odzyskiwania, którego chcesz użyć podczas pracy w trybie failover. Punkt odzyskiwania spójny dla aplikacji jest wcześniejszy niż najnowszy punkt i spowoduje utratę niektórych danych.

    >[!WARNING]
    >Podczas przełączania do trybu failover usługa Site Recovery wyłącza maszyny wirtualne platformy Azure i uruchamia lokalną maszynę wirtualną. Wiąże się to z przestojem, należy więc wybrać odpowiedni moment.

4. Postęp zadania można śledzić na stronie **Magazyn usługi Recovery Services** > **Monitorowanie i raporty** > **Zadania usługi Site Recovery**.
5. Po zakończeniu przejścia w tryb failover, kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie kliknij pozycję**Zatwierdź**. Spowoduje to wyzwolenie zadania, które usunie maszyny wirtualne platformy Azure.
6. Sprawdź, czy maszyna wirtualna platformy Azure została prawidłowo wyłączona.

## <a name="reprotect-on-premises-machines-to-azure"></a>Ponowne włączanie ochrony lokalnych maszyn na platformie Azure

Dane powinny teraz ponownie znajdować się w lokacji lokalnej, ale nie są replikowane na platformie Azure. Możesz ponownie rozpocząć replikowanie na platformie Azure w następujący sposób:

1. W obszarze magazyn > **Chronione elementy** >**Zreplikowane elementy** wybierz maszynę wirtualną po powrocie po powrocie po awarii, a następnie kliknij pozycję **Włącz ponownie ochronę**.
2. Wybierz serwer przetwarzania, który ma być używany do wysyłania replikowanych danych do platformy Azure, a następnie kliknij przycisk **OK**.

Po zakończeniu ponownego włączania ochrony maszyna wirtualna ponownie będzie replikowana na platformie Azure, a w razie potrzeby będzie można przełączyć ją do trybu failover.
