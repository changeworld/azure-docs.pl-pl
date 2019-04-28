---
title: Tryb failover i powrotu po awarii fizycznych serwerów dla odzyskiwania po awarii na platformie Azure z usługą Site Recovery niepowodzenie | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i serwerów fizycznych na platformę Azure w tryb failover i powrót po awarii do lokacji lokalnej do odzyskiwania po awarii przy użyciu usługi Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: raynew
ms.openlocfilehash: edb169d131aafd045fdf0f670e1dda87677d57ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036352"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Tryb failover i zakończyć się niepowodzeniem powrotu po awarii fizycznych serwerów replikowanych na platformie Azure

W tym samouczku opisano sposób awaryjnego przełączania serwera fizycznego na platformę Azure. Po przełączeniu w tryb failover, powrotu po awarii serwera do lokacji lokalnej po udostępnieniu.

## <a name="preparing-for-failover-and-failback"></a>Przygotowanie do przełączenia w tryb failover i powrotu po awarii

Fizycznych serwerów replikowanych na platformie Azure przy użyciu Site Recovery można przełączać tylko jako maszyny wirtualne VMware. Infrastruktura VMware jest niezbędna, aby wykonać powrotu po awarii.

Przełączanie do trybu failover i powrót po awarii odbywa się w czterech etapach:

1. **Przełączenie do trybu failover na platformie Azure**: wprowadzenie maszyn w tryb failover z lokacji lokalnej do platformy Azure.
2. **Ponowne włączanie ochrony maszyn wirtualnych na platformie Azure**: Ponowne włączanie ochrony maszyn wirtualnych platformy Azure, aby rozpoczynały się replikację maszyn wirtualnych programu VMware w środowisku lokalnym.
3. **Przełączenie do trybu failover w lokacji lokalnej**: uruchomienie trybu failover w celu powrotu po awarii z platformy Azure.
4. **Ponowne włączanie ochrony lokalnych maszyn wirtualnych**: Po danych ma powrót po awarii, ponownie włączyć ochronę maszyn wirtualnych programu VMware w środowisku lokalnym, możesz nie udało się ponownie, w tak, aby rozpoczęcia replikacji do platformy Azure.

## <a name="verify-server-properties"></a>Sprawdź właściwości serwera

Sprawdź właściwości serwera i upewnij się, że spełnia on [wymagania dotyczące usługi Azure](vmware-physical-azure-support-matrix.md#replicated-machines) maszyn wirtualnych platformy Azure.

1. W **chronione elementy**, kliknij przycisk **zreplikowane elementy**i wybierz maszynę.

2. W **zreplikowany element** , znajduje się podsumowanie informacji o maszynie, stan kondycji, a najnowsze dostępne punkty odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.
3. W obszarze **Obliczenia i sieć** można zmodyfikować nazwę platformy Azure, grupę zasobów, rozmiar docelowy, [zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) i ustawienia dysku zarządzanego
4. Możesz wyświetlać i modyfikować ustawienia sieciowe, w tym sieć/podsieć, w której zlokalizowana będzie maszyna wirtualna na platformie Azure po wejściu w tryb failover, oraz adres IP, który będzie do niej przypisany.
5. W **dysków**, można wyświetlić informacje o maszynie systemu operacyjnego i dysków z danymi.

## <a name="run-a-failover-to-azure"></a>Przełączanie do trybu failover na platformie Azure

1. W obszarze **Ustawienia** > **Zreplikowane elementy** kliknij maszynę wirtualną > **Tryb failover**.
2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, którego chcesz użyć do przełączenia do trybu failover. Możesz użyć jednej z następujących opcji:
   - **Najnowszy**: ta opcja najpierw przetwarza wszystkie dane wysyłane do usługi Site Recovery. Zapewnia najniższą wartość celu puntu odzyskiwania, ponieważ maszyna wirtualna platformy Azure utworzona po przejściu do trybu failover zawiera wszystkie dane, które zostały zreplikowane w usłudze Site Recovery do momentu włączenia trybu failover.
   - **Najnowszy przetworzony**: Ta opcja nie powiedzie się za pośrednictwem maszyny do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
   - **Najnowszy spójny na poziomie aplikacji**: Ta opcja nie powiedzie się za pośrednictwem komputera najnowszy spójny na poziomie aplikacji punkt przywracania przetworzone przez usługę Site Recovery.
   - **Niestandardowy**: umożliwia określenie punktu odzyskiwania.

3. Wybierz **Zamknij maszynę przed rozpoczęciem pracy awaryjnej** Jeśli chcesz, aby Usługa Site Recovery, aby spróbować zamknąć maszyny źródłowej przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.
4. Jeśli przeprowadzono przygotowanie do nawiązania połączenia z maszyną wirtualną platformy Azure, należy po przełączeniu w tryb failover nawiązać połączenie w celu weryfikacji.
5. Po weryfikacji należy **Zatwierdzić** tryb failover. To działanie usuwa wszystkie dostępne punkty odzyskiwania.

> [!WARNING]
> Nie Anuluj trybu failover w toku. Przed rozpoczęciem pracy awaryjnej, powoduje zatrzymanie replikacji maszyny. Jeśli anulujesz tryb failover, zostaje zatrzymana, ale komputer nie zostanie ponownie zreplikowana.
> Dla serwerów fizycznych pracy awaryjnej dodatkowe przetwarzanie może zająć około 8 – 10 minut.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Jeśli chcesz nawiązać połączenie z maszynami wirtualnymi platformy Azure przy użyciu protokołu RDP/SSH po przejściu do trybu failover, upewnij się, że zostały spełnione wymagania podsumowane w [tej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) tabeli.

Wykonaj czynności opisane [tutaj](site-recovery-failover-to-azure-troubleshoot.md), aby rozwiązać wszystkie problemy z łącznością po przejściu do trybu failover.

## <a name="create-a-process-server-in-azure"></a>Tworzenie serwera przetwarzania na platformie Azure

Serwer przetwarzania otrzymuje dane z maszyny wirtualnej Azure i wysyła je do lokacji lokalnej. Sieci o małych opóźnieniach jest wymagany między serwerem przetwarzania a chronioną maszynę.

- Jeśli masz połączenie Azure ExpressRoute, możesz użyć do celów testowych lokalnego serwera przetwarzania, który jest automatycznie instalowany na serwerze konfiguracji.
- Jeśli masz połączenie VPN lub jeśli przeprowadzasz powrót po awarii w środowisku produkcyjnym, musisz na potrzeby powrotu po awarii skonfigurować maszynę wirtualną Azure jako serwer przetwarzania na platformie Azure.
- Postępuj zgodnie z instrukcjami w [w tym artykule](vmware-azure-set-up-process-server-azure.md) skonfigurować serwer przetwarzania na platformie Azure.

## <a name="configure-the-master-target-server"></a>Konfigurowanie głównego serwera docelowego

Domyślnie główny serwer docelowy odbiera dane podczas powrotu po awarii. Działa na lokalnym serwerze konfiguracji.

- W przypadku maszyny Wirtualnej VMware, do którego możesz wykonać powrotu po awarii na hoście ESXi zarządzanym przez serwer VMware vCenter, główny serwer docelowy musi mieć dostęp do magazynu danych maszyny Wirtualnej (VMDK), aby zapisać zreplikowane dane na dyskach maszyny Wirtualnej. Upewnij się, że magazyn maszyny wirtualnej jest zainstalowany na hoście głównego serwera docelowego z dostępem do odczytu/zapisu.
- Jeśli host ESXi, który nie jest zarządzany przez serwer vCenter, Usługa Site Recovery utworzy nową maszynę Wirtualną podczas ponownego włączania ochrony. Maszyna wirtualna jest tworzona na hoście ESX, na którym utworzono główny cel maszyny Wirtualnej. Dysk twardy maszyny wirtualnej musi znajdować się w magazynie danych dostępnym dla hosta, na którym uruchomiony jest główny serwer docelowy.
- Dla maszyn fizycznych, które możesz wykonać powrotu po awarii należy najpierw ukończyć odnajdowanie hosta, na którym jest uruchomiona na głównym serwerze docelowym, zanim można ponownie włączyć ochronę maszyny.
- Innym rozwiązaniem, jeśli lokalna maszyna wirtualna już istnieje dla powrotu po awarii jest go usunąć przed wykonaniem powrotu po awarii. W takim przypadku podczas powrotu po awarii zostanie utworzona nowa maszyna wirtualna na tym samym hoście ESX, na którym znajduje się główny serwer docelowy. W przypadku powrotu po awarii do innej lokalizacji dane są odzyskiwane do tego samego magazynu danych i tego samego hosta ESX, który jest używany przez lokalny główny serwer docelowy.
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
