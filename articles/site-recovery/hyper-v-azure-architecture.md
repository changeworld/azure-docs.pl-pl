---
title: Funkcja Hyper-V architektura replikacji Azure w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie składników i architektury używanych podczas replikowania lokalnych maszyn wirtualnych funkcji Hyper-V (bez usługi VMM) do platformy Azure za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.openlocfilehash: 11ecc341977efb5c815581a77383d1c8cae51ed6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894334"
---
# <a name="hyper-v-to-azure-replication-architecture"></a>Architektura Azure replikacji funkcji Hyper-V


W tym artykule opisano architektura i procesy stosowane podczas replikacji, pracy awaryjnej i odzyskiwania maszyn wirtualnych funkcji Hyper-V (VM) między lokalnymi hostami funkcji Hyper-V i platformą Azure, przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md) usługi.

Opcjonalnie można zarządzać hostami funkcji Hyper-V w chmurach programu System Center Virtual Machine Manager (VMM).



## <a name="architectural-components---hyper-v-without-vmm"></a>Składniki architektury — funkcji Hyper-V bez programu VMM

Następujące tabeli i grafika Podaj ogólny widok składniki używane do replikacji funkcji Hyper-V do platformy Azure, jeśli hosty funkcji Hyper-V nie są zarządzane przez program VMM.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure, konto magazynu Azure i sieć platformy Azure. | Replikowane dane z lokalnymi obciążeń maszyny Wirtualnej są przechowywane na koncie magazynu. Maszyny wirtualne platformy Azure są tworzone przy użyciu danych replikowanych obciążeń czasie pracy awaryjnej z lokacji lokalnej.<br/><br/> Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Funkcja Hyper-V** | Podczas wdrażania usługi Site Recovery należy zebrać klastry i hosty funkcji Hyper-V w lokacji funkcji Hyper-V. Agent dostawcy usługi Azure Site Recovery i usług odzyskiwania należy zainstalować na każdym hoście funkcji Hyper-V autonomiczne lub w każdym węźle klastra funkcji Hyper-V. | Dostawca koordynuje replikację za pomocą usługi Site Recovery przez Internet. Agent usługi Recovery Services obsługuje replikację danych.<br/><br/> Komunikacja zarówno ze strony dostawcy, jak i agenta, jest bezpieczna i szyfrowana. Zreplikowane dane w usłudze Azure Storage również są szyfrowane.
**Maszyny wirtualne funkcji Hyper-V** | Co najmniej jeden uruchomionych maszyn wirtualnych funkcji Hyper-v. | Nic nie musi być jawnie zainstalowany na maszynach wirtualnych.


**Funkcja Hyper-V architektura platformy Azure (bez VMM)**

![Architektura](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)



## <a name="architectural-components---hyper-v-with-vmm"></a>Składniki architektury — funkcji Hyper-V w programie VMM

Następujące tabeli i grafika Podaj ogólny widok składniki używane do replikacji funkcji Hyper-V do platformy Azure, jeśli hosty funkcji Hyper-V są zarządzane w chmurach programu VMM.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure, konto magazynu Azure i sieć platformy Azure. | Replikowane dane z lokalnymi obciążeń maszyny Wirtualnej są przechowywane na koncie magazynu. Maszyny wirtualne platformy Azure są tworzone przy użyciu zreplikowanych danych podczas pracy awaryjnej z lokacji lokalnej.<br/><br/> Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Serwer VMM** | Serwer VMM ma co najmniej jedną chmurę zawierającą hosty funkcji Hyper-V. | Instalowanie dostawcy usługi Site Recovery na serwerze programu VMM w celu organizowania replikacji za pomocą usługi Site Recovery i Zarejestruj serwer w magazynie usług odzyskiwania.
**Host funkcji Hyper-V** | Co najmniej jeden host/klaster funkcji Hyper-V zarządzany przez program VMM. |  W każdym węźle klastra lub hosta funkcji Hyper-V należy zainstalować agenta usług odzyskiwania.
**Maszyny wirtualne funkcji Hyper-V** | Co najmniej jedna maszyna wirtualna uruchomiona na serwerze hosta funkcji Hyper-V. | Niczego nie trzeba jawnie instalować na maszynach wirtualnych.
**Sieć** | Sieci logiczne i maszyn wirtualnych skonfigurowane na serwerze VMM. Sieć wirtualna powinna być połączona z siecią logiczną skojarzoną z chmurą. | Sieci maszyn wirtualnych są mapowane do sieci wirtualnych platformy Azure. Podczas tworzenia maszyn wirtualnych Azure po pracy awaryjnej, są one dodane do sieci platformy Azure, który jest zamapowany na sieć maszyny Wirtualnej.

**Funkcji Hyper-V do platformy Azure architektury (w programie VMM)**

![Składniki](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Proces replikacji

![Funkcja Hyper-V Azure replikacji](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Proces replikacji i odzyskiwania**


### <a name="enable-protection"></a>Włączanie ochrony

1. Po włączeniu ochrony dla maszyny wirtualnej funkcji Hyper-V, w witrynie Azure Portal lub środowisku lokalnym, zostanie uruchomione zadanie **Włącz ochronę**.
2. To zadanie sprawdza, czy maszyna spełnia wymagania wstępne, a następnie wywołuje metodę [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), aby skonfigurować replikację za pomocą określonych ustawień.
3. Zadanie uruchamia replikację początkową, wywołując metodę [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), aby zainicjować pełną replikację maszyny wirtualnej i wysłać dyski wirtualne maszyny wirtualnej na platformę Azure.
4. To zadanie możesz monitorować na karcie **Zadania**.      ![Lista zadań](media/hyper-v-azure-architecture/image1.png) ![Szczegóły zadania Włącz ochronę](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Początkowej replikacji danych

1. Po wyzwoleniu replikacji początkowej [migawki maszyny Wirtualnej funkcji Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) migawki.
2. Wirtualne dyski twarde na maszynie Wirtualnej są replikowane pojedynczo, dopóki wszystkie nie zostaną skopiowane do platformy Azure. Może to potrwać kilka minut w zależności od rozmiaru maszyny Wirtualnej i przepustowość sieci. [Dowiedz się, jak](https://support.microsoft.com/kb/3056159) zwiększyć przepustowość sieci.
3. Jeśli zmiany dysku są wykonywane, gdy Replikacja początkowa jest w toku, śledzenie replikacji repliki funkcji Hyper-V śledzi zmiany jako dzienniki replikacji funkcji Hyper-V (hrl). Te pliki dziennika znajdują się w tym samym folderze co dyski. Każdy dysk ma skojarzony plik hrl, który jest wysyłany do magazynu pomocniczego. Pliki migawki i dziennika zużywają zasoby dysku w trakcie replikacji początkowej.
4. Po zakończeniu replikacji początkowej migawka maszyny wirtualnej jest usuwana.
5. Różnicowe zmiany dysku w dzienniku są synchronizowane i scalane z dyskiem nadrzędnym.


### <a name="finalize-protection-process"></a>Finalizuj ochronę procesu

1. Po zakończeniu replikacji początkowej, **Finalizuj ochronę na maszynie wirtualnej** zadania działa. Konfiguruje sieciowe i inne ustawienia po replikacji, aby maszyna wirtualna jest chroniona.
2. Na tym etapie można sprawdzić ustawienia maszyny Wirtualnej, aby upewnić się, że jest gotowa do pracy awaryjnej. Możesz uruchomić wyszczególniania odzyskiwania po awarii (test trybu failover) dla maszyny Wirtualnej, aby sprawdzić, czy niepowodzenia ponad zgodnie z oczekiwaniami. 


## <a name="delta-replication"></a>Replikacja różnicowa

1. Po początkowej replikacji różnicowej rozpoczyna się replikacja, zgodnie z zasadami replikacji.
2. Jako pliki hrl śledzący replikacji funkcji Hyper-V Replica śledzi zmiany wirtualnego dysku twardego. Z każdym dyskiem skonfigurowanym pod kątem replikacji jest skojarzony plik hrl.
3. Dziennik są wysyłane do konta magazynu klienta. Jeśli dziennik jest przesyłane na platformie Azure, zmiany dysku podstawowego są śledzone w innym pliku dziennika, w tym samym folderze.
4. Podczas replikacji początkowej i różnicowych można monitorować maszyny Wirtualnej w portalu Azure.

### <a name="resynchronization-process"></a>Proces ponownej synchronizacji

1. Jeśli replikacja różnicowa nie powiedzie się, a pełna replikacja byłaby kosztowna pod względem przepustowości lub czasu, maszyna wirtualna jest oznaczana do ponownej synchronizacji.
    - Jeśli na przykład pliki hrl będą zajmować 50% rozmiaru dysku, to maszyna wirtualna zostanie oznaczona do ponownej synchronizacji.
    -  Domyślnie ponowna synchronizacja jest zaplanowane do automatycznego uruchamiania poza godzinami pracy.
1.  Ponowna synchronizacja wysyła tylko danych różnicowych.
    - Minimalizuje ilość danych przesyłanych przez obliczaniu sum kontrolnych źródłowych i docelowych maszyn wirtualnych.
    - Go używa algorytmu segmentu stałej bloku, w którym pliki źródłowe i docelowe są podzielone na stałe fragmentów.
    - Generowane są sumy kontrolne dla każdego fragmentu. Te są porównywane w celu określenia, które bloki ze źródła, należy zastosować do obiektu docelowego.
2. Po ukończeniu ponownej synchronizacji replikacja różnicowa powinna zostać wznowiona.
3. Jeśli nie chcesz czekać na ponowną synchronizację domyślne poza godzinami, można ponownie zsynchronizować Maszynę wirtualną ręcznie. Na przykład w przypadku wystąpienia awarii. Aby to zrobić, w portalu Azure, wybierz maszynę Wirtualną > **ponownie zsynchronizować**.

    ![Ręczna ponowna synchronizacja](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Ponów próbę wykonania procesu

Jeśli wystąpi błąd replikacji, może zostać użyty wbudowany mechanizm ponawiania. Ponów próbę jest klasyfikowany zgodnie z opisem w tabeli.

**Kategoria** | **Szczegóły**
--- | ---
**Błąd nieodwracalny** | Nie jest podejmowana próba ponowienia. Maszyna wirtualna będzie mieć stan **Krytyczny** i będzie wymagana interwencja administratora.<br/><br/> Błędy te przykłady przerwany łańcuch wirtualnego dysku twardego, nieprawidłowy stan dla repliki maszyny Wirtualnej, sieci błędy uwierzytelniania, autoryzacji błędy, a maszyna wirtualna nie znaleziono błędy (w przypadku autonomicznych serwerów funkcji Hyper-V.
**Błędy odwracalne** | Ponowne próby są wykonywane co interwał replikacji przy użyciu wycofywania wykładniczego zwiększającego interwał ponawiania prób od początku pierwszej próby o 1, 2, 4, 8 i 10 minut. Jeśli błąd będzie się powtarzać, ponowne próby będą wykonywane co 30 minut. Oto kilka przykładów: błędy sieci, błędy małej ilości i warunków braku pamięci.



## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

1. Można uruchomić planowanego lub nieplanowanego trybu failover z maszyn wirtualnych funkcji Hyper-V lokalnego do platformy Azure. Jeśli zostanie uruchomione planowane przejście w tryb failover, źródłowe maszyny wirtualne zostaną wyłączone w celu zapewnienia, że nie będzie miała miejsca utrata danych. Jeśli w lokacji głównej nie jest dostępny, należy uruchomić nieplanowanego trybu failover.
2. W tryb failover jednego komputera lub utworzyć plany odzyskiwania, aby organizować tryb failover wiele maszyn.
3. Możesz uruchomić tryb failover. Po ukończeniu pierwszego etap trybu failover, należy wyświetlić utworzony repliki maszyn wirtualnych na platformie Azure. Jeśli jest to wymagane, do maszyny wirtualnej można przypisać publiczny adres IP.
4. Można następnie przekazać trybu failover, można uruchomić podczas uzyskiwania dostępu do obciążenia z repliki maszyny Wirtualnej platformy Azure.

Po infrastruktury lokalnej jest uruchomione ponownie, może nie powieść ponownie. Awarii w trzech etapach:

1. Rozpocząć poza planowanego trybu failover z platformy Azure do lokacji lokalnej:
    - **Zminimalizować przestoje**: Jeśli używasz tej opcji odzyskiwania lokacji synchronizuje dane przed trybu failover. Sprawdza bloki zmienione dane i pobiera je do lokacji lokalnej, podczas przechowuje maszyny Wirtualnej platformy Azure, uruchamianie, minimalizując przestoju. Ręczne określenie, czy tryb failover należy wykonać, maszyny Wirtualnej Azure jest wyłączona, wszelkie zmiany różnicowe końcowego są kopiowane i uruchamiania trybu failover.
    - **Pełne pobieranie**: po wybraniu tej opcji dane są synchronizowane podczas pracy awaryjnej. Ta opcja pobiera cały dysk. Jest on szybciej, ponieważ nie sum kontrolnych są obliczane, ale ma więcej przestoju. Użyj tej opcji, jeśli działała repliki maszyn wirtualnych platformy Azure przez pewien czas lub lokalnej maszyny Wirtualnej został usunięty.
    - **Tworzenie maszyny Wirtualnej**: można określić, aby zakończyć się niepowodzeniem, wróć do tej samej maszyny Wirtualnej lub alternatywne maszyny Wirtualnej. Można określić usługi Site Recovery należy utworzyć maszynę Wirtualną, jeśli jeszcze nie istnieje.

2. Po zakończeniu wstępnej synchronizacji, wybierz do ukończenia pracy awaryjnej. Po zakończeniu instalacji można rejestrować na lokalnej maszyny Wirtualnej, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami. W portalu Azure widać, że maszyny wirtualne Azure została zatrzymana.
3.  Następnie możesz zatwierdzić tryb failover, aby zakończyć, a następnie uruchom uzyskać dostęp do obciążenia z lokalnej maszyny Wirtualnej ponownie.
4. Po obciążeń powiodły się z powrotem, można włączyć replikacji odwrotnej, dzięki czemu maszyn wirtualnych do lokalnej replikacji do platformy Azure ponownie.



## <a name="next-steps"></a>Kolejne kroki


Postępuj zgodnie z [w tym samouczku](tutorial-prepare-azure.md) aby zacząć korzystać z funkcji Hyper-V do platformy Azure replikacji.


