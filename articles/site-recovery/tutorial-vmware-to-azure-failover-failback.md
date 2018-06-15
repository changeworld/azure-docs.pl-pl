---
title: Przechodzenie do trybu failover i powrót po awarii maszyn wirtualnych VMware i fizycznych serwerów replikowanych na platformie Azure za pomocą usługi Site Recovery | Microsoft Docs
description: Dowiedz się, jak wprowadzić maszyny wirtualne VMware i fizyczne serwery w tryb failover na platformie Azure i przywrócić je po awarii do lokacji lokalnej za pomocą usługi Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: f074312ecee39d4b3022df64b51aadd2bb8f968c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677638"
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

Jest to piąty samouczek z tej serii. Założono w nim, że zostały już wykonane zadania z poprzednich samouczków.

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnego wdrożenia oprogramowania VMware](tutorial-prepare-on-premises-vmware.md)
3. [Konfigurowanie odzyskiwania po awarii](tutorial-vmware-to-azure.md)
4. [Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)

## <a name="preparing-for-failover-and-failback"></a>Przygotowanie do przełączenia w tryb failover i powrotu po awarii

Należy upewnić się, że na maszynie wirtualnej nie ma żadnych migawek. Lokalna maszyna wirtualna jest wyłączana na czas ponownego włączania ochrony.
Pomaga to zapewnić spójność danych podczas replikacji. Nie należy włączać maszyny wirtualnej po zakończeniu ponownego włączania ochrony. Użyj tego samego głównego serwera docelowego do ponownego włączenia ochrony grupy replikacji. Użycie innego głównego serwera docelowego do ponownego włączenia ochrony grupy replikacji spowoduje, że serwer nie będzie mógł zapewnić wspólnego punktu w czasie.

Przełączanie do trybu failover i powrót po awarii odbywa się w czterech etapach:

1. **Przełączenie do trybu failover na platformie Azure**: wprowadzenie maszyn w tryb failover z lokacji lokalnej do platformy Azure.
2. **Ponowne włączanie ochrony maszyn wirtualnych na platformie Azure**: ponowne włączenie ochrony maszyn wirtualnych platformy Azure w celu rozpoczęcia replikacji do lokalnych maszyn wirtualnych VMware.
3. **Przełączenie do trybu failover w lokacji lokalnej**: uruchomienie trybu failover w celu powrotu po awarii z platformy Azure.
4. **Ponowne włączanie ochrony lokalnych maszyn wirtualnych**: po powrocie danych po awarii należy ponownie włączyć ochronę lokalnych maszyn wirtualnych, do których powrócono po awarii, aby rozpocząć replikowanie ich do platformy Azure.

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Sprawdź właściwości maszyny wirtualnej i upewnij się, że maszyna wirtualna spełnia [wymagania platformy Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. W obszarze **Chronione elementy** kliknij kolejno pozycje **Zreplikowane elementy** > Maszyna wirtualna.

2. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.

3. W obszarze**Obliczenia i sieć** można zmodyfikować nazwę platformy Azure, grupę zasobów, rozmiar docelowy, [zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) i [ustawienia dysku zarządzanego](#managed-disk-considerations)

4. Możesz wyświetlać i modyfikować ustawienia sieciowe, w tym sieć/podsieć, w której zlokalizowana będzie maszyna wirtualna na platformie Azure po wejściu w tryb failover, oraz adres IP, który będzie do niej przypisany.

5. W obszarze **Dyski** można znaleźć informacje dotyczące systemu operacyjnego i dysków danych maszyny wirtualnej.

## <a name="run-a-failover-to-azure"></a>Przełączanie do trybu failover na platformie Azure

1. W obszarze **Ustawienia** > **Zreplikowane elementy** kliknij kolejno pozycje maszyna wirtualna > **Tryb failover**.

2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, którego chcesz użyć do przełączenia do trybu failover. Możesz użyć jednej z następujących opcji:
   - **Najnowszy** (domyślna): ta opcja najpierw przetwarza wszystkie dane wysyłane do usługi Site Recovery. Zapewnia najniższą wartość celu puntu odzyskiwania, ponieważ maszyna wirtualna platformy Azure utworzona po przejściu do trybu failover zawiera wszystkie dane, które zostały zreplikowane w usłudze Site Recovery do momentu włączenia trybu failover.
   - **Najnowszy przetworzony**: ta opcja wprowadza maszynę wirtualną w tryb failover z użyciem najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza maszynę wirtualną w tryb failover z użyciem najnowszego spójnego na poziomie aplikacji punktu odzyskiwania przetworzonego przez usługę Site Recovery.
   - **Niestandardowy**: umożliwia określenie punktu odzyskiwania.

3. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**, aby spróbować przeprowadzić zamknięcie źródłowych maszyn wirtualnych przed włączeniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.

4. Jeśli przeprowadzono przygotowanie do nawiązania połączenia z maszyną wirtualną platformy Azure, należy po przełączeniu w tryb failover nawiązać połączenie w celu weryfikacji.

5. Po weryfikacji należy **Zatwierdzić** tryb failover. To działanie usuwa wszystkie dostępne punkty odzyskiwania.

> [!WARNING]
> **Nie anuluj trybu failover po rozpoczęciu przełączania**: przed uruchomieniem trybu failover replikacja maszyny wirtualnej zostanie zatrzymana.
> Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, którego przeprowadzenie zajmuje około 8–10 minut. Dłuższy czas testu trybu failover może występować w przypadku serwerów fizycznych; maszyn VMware z systemem Linux; maszyn wirtualnych VMware, które nie mają włączonej usługi DHCP oraz maszyn wirtualnych VMware, które nie mają następujących sterowników rozruchowych: storvsc, vmbus, storflt, intelide, atapi.

## <a name="create-a-process-server-in-azure"></a>Tworzenie serwera przetwarzania na platformie Azure

Serwer przetwarzania otrzymuje dane z maszyny wirtualnej Azure i wysyła je do lokacji lokalnej. Pomiędzy serwerem przetwarzania a chronioną maszyną wirtualną wymagana jest sieć z małym opóźnieniem.

- Jeśli masz połączenie Azure ExpressRoute, możesz użyć do celów testowych lokalnego serwera przetwarzania, który jest automatycznie instalowany na serwerze konfiguracji.
- Jeśli masz połączenie VPN lub jeśli przeprowadzasz powrót po awarii w środowisku produkcyjnym, musisz na potrzeby powrotu po awarii skonfigurować maszynę wirtualną Azure jako serwer przetwarzania na platformie Azure.
- Aby skonfigurować serwer przetwarzania na platformie Azure, postępuj według instrukcji opisanych w [tym artykule](site-recovery-vmware-setup-azure-ps-resource-manager.md).

## <a name="configure-the-master-target-server"></a>Konfigurowanie głównego serwera docelowego

Domyślnie główny serwer docelowy jest uruchamiany na lokalnym serwerze konfiguracji. Na potrzeby tego samouczka użyj domyślnego serwera głównego. Główny serwer docelowy odbiera dane podczas powrotu po awarii.

Jeśli maszyna wirtualna jest na hoście ESXi zarządzanym przez serwer vCenter, główny serwer docelowy musi mieć dostęp do magazynu danych maszyny wirtualnej, aby zapisać zreplikowane dane na dyskach maszyny wirtualnej. Upewnij się, że magazyn maszyny wirtualnej jest zainstalowany na hoście głównego serwera docelowego z dostępem do odczytu/zapisu.

Jeśli maszyna wirtualna jest na goście ESXi, który nie jest zarządzany przez serwer vCenter, usługa Site Recovery utworzy nową maszynę wirtualną podczas ponownego włączania ochrony. Maszyna wirtualna jest tworzona na tym hoście ESX, na którym utworzono główny serwer docelowy.
Dysk twardy maszyny wirtualnej musi znajdować się w magazynie danych dostępnym dla hosta, na którym uruchomiony jest główny serwer docelowy.

Jeśli maszyna wirtualna nie używa serwera vCenter, należy najpierw ukończyć odnajdowanie hosta, na którym jest uruchomiony główny serwer docelowy, a dopiero potem ponownie włączyć ochronę maszyny. Dotyczy to również powrotu po awarii fizycznych serwerów. Innym rozwiązaniem, jeśli istnieje lokalna maszyna wirtualna, jest usunięcie jej przed powrotem po awarii. W takim przypadku podczas powrotu po awarii zostanie utworzona nowa maszyna wirtualna na tym samym hoście ESX, na którym znajduje się główny serwer docelowy. W przypadku powrotu po awarii do innej lokalizacji dane są odzyskiwane do tego samego magazynu danych i tego samego hosta ESX, który jest używany przez lokalny główny serwer docelowy.

Na głównym serwerze docelowym nie można używać narzędzia Storage vMotion. W przypadku jego użycia powrót po awarii nie będzie możliwy, ponieważ dyski nie będą dostępne. Wyklucz główne serwery docelowe z listy vMotion.

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
