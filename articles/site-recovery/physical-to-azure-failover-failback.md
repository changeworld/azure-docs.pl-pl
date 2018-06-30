---
title: Tryb failover i się nie powieść serwery ponownie fizyczne replikowane do platformy Azure z usługą Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak działają serwerów fizycznych do platformy Azure i powrót po awarii do lokacji lokalnej, z usługą Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: raynew
ms.openlocfilehash: 2610a331ef8ef17b319da99eb89288b59532be9f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111502"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Tryb failover i niepowodzeniem ponownie fizycznych serwerów zreplikowanej w systemie Azure

Ten przewodnik opisuje sposób do trybu failover serwera fizycznego na platformie Azure. Po przez Ciebie nie powiodła się, możesz powrót po awarii serwera do lokacji sieci lokalnej, gdy jest ona dostępna. 

## <a name="preparing-for-failover-and-failback"></a>Przygotowanie do przełączenia w tryb failover i powrotu po awarii

Serwery fizyczne replikowane do platformy Azure przy użyciu usługi Site Recovery mogą nie tylko jako maszyny wirtualne VMware. Aby można było powrócić należy infrastruktury VMware. 

Przełączanie do trybu failover i powrót po awarii odbywa się w czterech etapach:

1. **Przełączenie do trybu failover na platformie Azure**: wprowadzenie maszyn w tryb failover z lokacji lokalnej do platformy Azure.
2. **Włącz ponownie ochronę maszyny wirtualne Azure**: Włącz ponownie ochronę maszyn wirtualnych platformy Azure, aby rozpoczynały się replikację z powrotem do lokalnej maszyny wirtualne VMware.
3. **Przełączenie do trybu failover w lokacji lokalnej**: uruchomienie trybu failover w celu powrotu po awarii z platformy Azure.
4. **Ponownej ochrony maszyn wirtualnych lokalnymi**: po danych nie powiodło się ponownie, Wznów zakończonych niepowodzeniem powrót do maszyn wirtualnych VMware lokalnego, aby rozpoczynały się replikację do platformy Azure.

## <a name="verify-server-properties"></a>Sprawdź właściwości serwera

Sprawdź właściwości serwera i upewnij się, że spełnia on [wymagania dotyczące usługi Azure](vmware-physical-azure-support-matrix.md#replicated-machines) dla maszyn wirtualnych platformy Azure.

1. W **chronione elementy**, kliknij przycisk **elementy replikowane**i wybierz maszynę.

2. W **elementu zreplikowane** okienku znajduje się podsumowanie informacji o maszynie, stan kondycji i najnowsze dostępne punkty odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.
3. W obszarze**Obliczenia i sieć** można zmodyfikować nazwę platformy Azure, grupę zasobów, rozmiar docelowy, [zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) i [ustawienia dysku zarządzanego](#managed-disk-considerations)
4. Możesz wyświetlać i modyfikować ustawienia sieciowe, w tym sieć/podsieć, w której zlokalizowana będzie maszyna wirtualna na platformie Azure po wejściu w tryb failover, oraz adres IP, który będzie do niej przypisany.
5. W **dysków**, znajdują się informacje dotyczące systemu operacyjnego maszyny i dysków z danymi.

## <a name="run-a-failover-to-azure"></a>Przełączanie do trybu failover na platformie Azure

1. W obszarze **Ustawienia** > **Zreplikowane elementy** kliknij maszynę wirtualną > **Tryb failover**.
2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, którego chcesz użyć do przełączenia do trybu failover. Możesz użyć jednej z następujących opcji:
   - **Najnowszy** (domyślna): ta opcja najpierw przetwarza wszystkie dane wysyłane do usługi Site Recovery. Zapewnia najniższą wartość celu puntu odzyskiwania, ponieważ maszyna wirtualna platformy Azure utworzona po przejściu do trybu failover zawiera wszystkie dane, które zostały zreplikowane w usłudze Site Recovery do momentu włączenia trybu failover.
   - **Najnowsze przetworzone**: Ta opcja nie powiedzie się za pośrednictwem maszyny do ostatniego punktu odzyskiwania przetworzone przez usługę Site Recovery. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
   - **Najnowsza wersja aplikacji spójne**: Ta opcja nie powiedzie się za pośrednictwem komputera punktu najnowszych odzyskiwania zapewniających spójność aplikacji przetworzone przez usługę Site Recovery.
   - **Niestandardowy**: umożliwia określenie punktu odzyskiwania.

3. Wybierz **Zamknij maszynę przed rozpoczęciem pracy awaryjnej** Jeśli chcesz, aby usługi Site Recovery, aby spróbować zamknąć maszyny źródłowej, aby mogło nastąpić wyzwolenie pracy awaryjnej. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.
4. Jeśli przeprowadzono przygotowanie do nawiązania połączenia z maszyną wirtualną platformy Azure, należy po przełączeniu w tryb failover nawiązać połączenie w celu weryfikacji.
5. Po weryfikacji należy **Zatwierdzić** tryb failover. To działanie usuwa wszystkie dostępne punkty odzyskiwania.

> [!WARNING]
> Nie Anuluj trybu failover w toku. Przed rozpoczęciem pracy awaryjnej, zatrzymuje replikację maszyny. Jeśli anulujesz pracy awaryjnej, zatrzymuje, ale maszyny będą replikowane ponownie.
> W przypadku fizycznych serwerów przetwarzania dodatkowe trybu failover może potrwać około 8-10 minut do wykonania. 

## <a name="create-a-process-server-in-azure"></a>Tworzenie serwera przetwarzania na platformie Azure

Serwer przetwarzania otrzymuje dane z maszyny wirtualnej Azure i wysyła je do lokacji lokalnej. Sieciach z małym opóźnieniami jest wymagany między serwerem przetwarzania i chronionej maszyny.

- Jeśli masz połączenie Azure ExpressRoute, możesz użyć do celów testowych lokalnego serwera przetwarzania, który jest automatycznie instalowany na serwerze konfiguracji.
- Jeśli masz połączenie VPN lub jeśli przeprowadzasz powrót po awarii w środowisku produkcyjnym, musisz na potrzeby powrotu po awarii skonfigurować maszynę wirtualną Azure jako serwer przetwarzania na platformie Azure.
- Postępuj zgodnie z instrukcjami [w tym artykule](vmware-azure-set-up-process-server-azure.md) Aby skonfigurować serwer przetwarzania na platformie Azure.

## <a name="configure-the-master-target-server"></a>Konfigurowanie głównego serwera docelowego

Domyślnie główny serwer docelowy odbiera dane powrotu po awarii. Jest uruchamiany na serwerze konfiguracji lokalnej.

- W przypadku maszyny Wirtualnej VMware, do którego należy powrócić na hoście ESXi, który jest zarządzany przez program VMware vCenter Server, główny serwer docelowy musi mieć dostęp do magazynu danych maszyny Wirtualnej (VMDK), można zapisać danych replikowanych do dysków maszyny Wirtualnej. Upewnij się, że magazyn maszyny wirtualnej jest zainstalowany na hoście głównego serwera docelowego z dostępem do odczytu/zapisu.
- Jeśli podczas przełączonej hosta ESXi, który nie jest zarządzana przez serwer vCenter, a usługa Site Recovery tworzy nową maszynę Wirtualną. Maszyna wirtualna jest tworzony na hoście ESX utworzenia główny cel maszyny Wirtualnej. Dysk twardy maszyny wirtualnej musi znajdować się w magazynie danych dostępnym dla hosta, na którym uruchomiony jest główny serwer docelowy.
- Dla komputerów fizycznych, które nie spełniają ponownie należy wykonać odnajdywania hosta, na którym jest uruchomiona główny serwer docelowy, zanim zostanie Włącz ponownie ochronę maszyny.
- Innym rozwiązaniem, jeśli lokalnej maszyny Wirtualnej już istnieje dla powrotu po awarii, jest go usunąć przed wykonaniem powrotu po awarii. W takim przypadku podczas powrotu po awarii zostanie utworzona nowa maszyna wirtualna na tym samym hoście ESX, na którym znajduje się główny serwer docelowy. W przypadku powrotu po awarii do innej lokalizacji dane są odzyskiwane do tego samego magazynu danych i tego samego hosta ESX, który jest używany przez lokalny główny serwer docelowy.
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

Dane powinno być teraz Wstecz w swojej witrynie lokalnej, ale nie przeprowadza replikacji do platformy Azure. Możesz ponownie rozpocząć replikowanie na platformie Azure w następujący sposób:

1. W magazynie w obszarze **Ustawienia** > **Zreplikowane elementy** wybierz maszyny wirtualne, które przywrócono po awarii, i kliknij przycisk **Włącz ponownie ochronę**.
2. Wybierz serwer przetwarzania używany do wysyłania replikowanych danych do platformy Azure, a następnie kliknij przycisk **OK**.

Po zakończeniu ponownego włączania ochrony maszyna wirtualna ponownie będzie replikowana na platformie Azure, a w razie potrzeby będzie można przełączyć ją do trybu failover.

