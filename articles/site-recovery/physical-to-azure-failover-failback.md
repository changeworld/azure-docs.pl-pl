---
title: Konfigurowanie trybu failover i powrotu po awarii dla serwerów fizycznych za pomocą Site Recovery
description: Dowiedz się, jak przełączyć serwery fizyczne do trybu failover na platformie Azure i powrócić po awarii do lokacji lokalnej w celu odzyskiwania po awarii za pomocą Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 2c0d2e57a34286f65be45a95403a32de42c51908
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084568"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Przełączenie w tryb failover i powrót po awarii serwerów fizycznych replikowanych do platformy Azure

W tym samouczku opisano sposób awaryjnego przełączania serwera fizycznego na platformę Azure. Po przełączeniu w tryb failover serwer nie zostanie przywrócony do lokacji lokalnej, jeśli jest dostępny.

## <a name="preparing-for-failover-and-failback"></a>Przygotowanie do przełączenia w tryb failover i powrotu po awarii

Serwery fizyczne replikowane do platformy Azure za pomocą Site Recovery mogą kończyć się niepowodzeniem tylko jako maszyny wirtualne VMware. Do powrotu po awarii potrzebna jest infrastruktura VMware.

Przełączanie do trybu failover i powrót po awarii odbywa się w czterech etapach:

1. **Przełączenie do trybu failover na platformie Azure**: wprowadzenie maszyn w tryb failover z lokacji lokalnej do platformy Azure.
2. Ponowne **Włączanie ochrony maszyn wirtualnych platformy Azure**: Włącz ochronę maszyn wirtualnych platformy Azure, aby umożliwić ich replikację z powrotem do lokalnych maszyn wirtualnych VMware.
3. **Przełączenie do trybu failover w lokacji lokalnej**: uruchomienie trybu failover w celu powrotu po awarii z platformy Azure.
4. Ponowne **Włączanie ochrony lokalnych maszyn wirtualnych**: po awarii danych ponownie Włącz ochronę lokalnych maszyn wirtualnych programu VMware, do których nie udało się powrócić, aby rozpocząć replikację do platformy Azure.

## <a name="verify-server-properties"></a>Sprawdź właściwości serwera

Sprawdź właściwości serwera i upewnij się, że jest ono zgodne z [wymaganiami platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines) dla maszyn wirtualnych platformy Azure.

1. W obszarze **chronione elementy**kliknij pozycję **zreplikowane elementy**, a następnie wybierz maszynę.

2. W okienku **replikowany element** znajduje się podsumowanie informacji o maszynie, kondycji i najnowszych dostępnych punktów odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.
3. W obszarze **Obliczenia i sieć** można zmodyfikować nazwę platformy Azure, grupę zasobów, rozmiar docelowy, [zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) i ustawienia dysku zarządzanego
4. Możesz wyświetlać i modyfikować ustawienia sieciowe, w tym sieć/podsieć, w której zlokalizowana będzie maszyna wirtualna na platformie Azure po wejściu w tryb failover, oraz adres IP, który będzie do niej przypisany.
5. W obszarze **dyski**można wyświetlić informacje o systemie operacyjnym komputera i dyskach z danymi.

## <a name="run-a-failover-to-azure"></a>Przełączanie do trybu failover na platformie Azure

1. W obszarze **Ustawienia** > **Zreplikowane elementy** kliknij maszynę wirtualną > **Tryb failover**.
2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, którego chcesz użyć do przełączenia do trybu failover. Możesz użyć jednej z następujących opcji:
   - **Najnowszy**: ta opcja najpierw przetwarza wszystkie dane wysyłane do usługi Site Recovery. Zapewnia najniższą wartość celu puntu odzyskiwania, ponieważ maszyna wirtualna platformy Azure utworzona po przejściu do trybu failover zawiera wszystkie dane, które zostały zreplikowane w usłudze Site Recovery do momentu włączenia trybu failover.
   - **Najnowsza przetworzony**: Ta opcja powoduje przełączenie komputera w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez Site Recovery. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
   - **Najnowsza spójna dla aplikacji**: Ta opcja powoduje przełączenie maszyny w tryb failover do najnowszego punktu odzyskiwania spójnego na poziomie aplikacji przetworzonego przez Site Recovery.
   - **Niestandardowy**: umożliwia określenie punktu odzyskiwania.

3. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover** , jeśli chcesz, aby Site Recovery próbuje zamknąć maszynę źródłową przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.
4. Jeśli przeprowadzono przygotowanie do nawiązania połączenia z maszyną wirtualną platformy Azure, należy po przełączeniu w tryb failover nawiązać połączenie w celu weryfikacji.
5. Po weryfikacji należy **Zatwierdzić** tryb failover. To działanie usuwa wszystkie dostępne punkty odzyskiwania.

> [!WARNING]
> Nie Anuluj trybu failover w toku. Przed rozpoczęciem pracy w trybie failover replikacja maszyny zostaje zatrzymana. Jeśli anulujesz tryb failover, zatrzyma się, ale maszyna nie zostanie ponownie zreplikowana.
> W przypadku serwerów fizycznych dodatkowe przetwarzanie trybu failover może potrwać około ośmiu do dziesięciu minut.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Jeśli chcesz nawiązać połączenie z maszynami wirtualnymi platformy Azure przy użyciu protokołu RDP/SSH po przejściu do trybu failover, upewnij się, że zostały spełnione wymagania podsumowane w [tej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) tabeli.

Wykonaj czynności opisane [tutaj](site-recovery-failover-to-azure-troubleshoot.md), aby rozwiązać wszystkie problemy z łącznością po przejściu do trybu failover.

## <a name="create-a-process-server-in-azure"></a>Tworzenie serwera przetwarzania na platformie Azure

Serwer przetwarzania otrzymuje dane z maszyny wirtualnej Azure i wysyła je do lokacji lokalnej. Między serwerem przetwarzania a chronioną maszyną wymagana jest sieć o małym opóźnieniu.

- Jeśli masz połączenie Azure ExpressRoute, możesz użyć do celów testowych lokalnego serwera przetwarzania, który jest automatycznie instalowany na serwerze konfiguracji.
- Jeśli masz połączenie VPN lub jeśli przeprowadzasz powrót po awarii w środowisku produkcyjnym, musisz na potrzeby powrotu po awarii skonfigurować maszynę wirtualną Azure jako serwer przetwarzania na platformie Azure.
- Postępuj zgodnie z instrukcjami w [tym artykule](vmware-azure-set-up-process-server-azure.md) , aby skonfigurować serwer przetwarzania na platformie Azure.

## <a name="configure-the-master-target-server"></a>Konfigurowanie głównego serwera docelowego

Domyślnie główny serwer docelowy otrzymuje dane powrotu po awarii. Jest on uruchamiany na lokalnym serwerze konfiguracji.

- Jeśli maszyna wirtualna VMware, do której nastąpi powrót po awarii, znajduje się na hoście ESXi zarządzanym przez VMware vCenter Server, główny serwer docelowy musi mieć dostęp do magazynu danych maszyny wirtualnej (VMDK), aby zapisać zreplikowane dane na dyskach maszyn wirtualnych. Upewnij się, że magazyn maszyny wirtualnej jest zainstalowany na hoście głównego serwera docelowego z dostępem do odczytu/zapisu.
- Jeśli host ESXi, który nie jest zarządzany przez serwer vCenter, Usługa Site Recovery tworzy nową maszynę wirtualną podczas jej ochrony. Maszyna wirtualna jest tworzona na hoście ESX, na którym tworzysz główną docelową maszynę wirtualną. Dysk twardy maszyny wirtualnej musi znajdować się w magazynie danych dostępnym dla hosta, na którym uruchomiony jest główny serwer docelowy.
- W przypadku maszyn fizycznych, które zostały przywrócone po awarii, należy przeprowadzić odnajdywanie hosta, na którym jest uruchomiony główny serwer docelowy, zanim będzie można ponownie włączyć ochronę maszyny.
- Inna opcja, jeśli lokalna maszyna wirtualna już istnieje dla powrotu po awarii, jest usuwana przed powrotem po awarii. W takim przypadku podczas powrotu po awarii zostanie utworzona nowa maszyna wirtualna na tym samym hoście ESX, na którym znajduje się główny serwer docelowy. W przypadku powrotu po awarii do innej lokalizacji dane są odzyskiwane do tego samego magazynu danych i tego samego hosta ESX, który jest używany przez lokalny główny serwer docelowy.
- Na głównym serwerze docelowym nie można używać narzędzia Storage vMotion. W przypadku jego użycia powrót po awarii nie będzie możliwy, ponieważ dyski nie będą dostępne. Wyklucz główne serwery docelowe z listy vMotion.

## <a name="reprotect-azure-vms"></a>Ponowne włączanie ochrony maszyn wirtualnych Azure

Ta procedura zakłada, że lokalna maszyna wirtualna nie jest dostępna i włączasz ochronę do alternatywnej lokalizacji.

1. W obszarze **Ustawienia** > **Zreplikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną, przełączoną w tryb failover, a następnie kliknij pozycję **Włącz ponownie ochronę**.
2. W obszarze **Włącz ponownie ochronę** sprawdź, czy wybrano opcję **Z platformy Azure do serwera lokalnego**.
3. Określ lokalny główny serwer docelowy oraz serwer przetwarzania.

4. W obszarze **Magazyn danych** wybierz główny docelowy magazyn danych, do którego chcesz odzyskać dyski w środowisku lokalnym. Użyj tej opcji, jeśli lokalna maszyna wirtualna została usunięta i musisz utworzyć nowe dyski. To ustawienie jest ignorowane, jeśli dyski już istnieją, ale określenie wartości jest wymagane.
5. Wybierz główny docelowy dysk przechowywania. Zasady powrotu po awarii są wybierane automatycznie.
6. Kliknij przycisk **OK**, aby rozpocząć ponowne włączanie ochrony. Zadanie zaczyna replikować maszynę wirtualną z platformy Azure do lokacji lokalnej. Na karcie **Zadania** można śledzić postęp.

> [!NOTE]
> Jeśli chcesz odzyskać maszynę wirtualną platformy Azure do istniejącej lokalnej maszyny wirtualnej, zainstaluj magazyn danych lokalnej maszyny wirtualnej z dostępem odczytu/zapisu na hoście ESXi głównego serwera docelowego.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Przechodzenie w tryb failover z platformy Azure do lokacji lokalnej

Aby zreplikować maszynę z powrotem do serwera lokalnego, używane są zasady powrotu po awarii. Są one tworzone automatycznie podczas tworzenia zasad replikacji na platformie Azure:

- Zasady zostaną automatycznie skojarzone z serwerem konfiguracji.
- Nie można ich modyfikować.
- Wartości zasad to:
    - Próg celu punktu odzyskiwania = 15 minut
    - Przechowywanie punktów odzyskiwania = 24 godziny
    - Częstotliwość wykonywania migawek na poziomie aplikacji = 60 minut

Uruchom tryb failover w następujący sposób:

1. Na stronie **Zreplikowane elementy** kliknij prawym przyciskiem myszy maszynę, a następnie wybierz opcję **Nieplanowana praca w trybie failover**.
2. W obszarze **Potwierdź tryb failover** sprawdź, czy wybrano kierunek trybu failover Z platformy Azure.

3. Wybierz punkt odzyskiwania, którego chcesz użyć podczas pracy w trybie failover. Punkt odzyskiwania spójny dla aplikacji jest wcześniejszy niż najnowszy punkt i spowoduje utratę niektórych danych. Podczas przełączania do trybu failover usługa Site Recovery wyłącza maszyny wirtualne platformy Azure i uruchamia lokalną maszynę wirtualną. Wiąże się to z przestojem, należy więc wybrać odpowiedni moment.
4. Kliknij prawym przyciskiem myszy maszynę, a następnie kliknij przycisk **Zatwierdź**. Spowoduje to wyzwolenie zadania, które usunie maszyny wirtualne platformy Azure.
5. Sprawdź, czy maszyna wirtualna platformy Azure została prawidłowo wyłączona.


## <a name="reprotect-on-premises-machines-to-azure"></a>Ponowne włączanie ochrony lokalnych maszyn na platformie Azure

Dane powinny teraz ponownie znajdować się w lokacji lokalnej, ale nie są replikowane na platformie Azure. Możesz ponownie rozpocząć replikowanie na platformie Azure w następujący sposób:

1. W magazynie w obszarze **Ustawienia** > **Zreplikowane elementy** wybierz maszyny wirtualne, które przywrócono po awarii, i kliknij przycisk **Włącz ponownie ochronę**.
2. Wybierz serwer przetwarzania używany do wysyłania replikowanych danych do platformy Azure, a następnie kliknij przycisk **OK**.

Po zakończeniu ponownego włączania ochrony maszyna wirtualna ponownie będzie replikowana na platformie Azure, a w razie potrzeby będzie można przełączyć ją do trybu failover.
