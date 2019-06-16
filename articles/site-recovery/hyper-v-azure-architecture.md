---
title: Funkcji Hyper-V do architektury odzyskiwania po awarii platformy Azure w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie składników i architektury używanych podczas wdrażania odzyskiwania po awarii dla maszyn wirtualnych funkcji Hyper-V w środowisku lokalnym (bez programu VMM) na platformie Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 7a1685622c44666eed6dac328772f6dba1418371
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66398228"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Funkcji Hyper-V do architektury odzyskiwania po awarii platformy Azure


W tym artykule opisano architekturę i procesów związanych z replikacja, tryb failover i odzyskiwanie maszyn wirtualnych funkcji Hyper-V (VM) między hostami funkcji Hyper-V w środowisku lokalnym i platformą Azure za pomocą [usługi Azure Site Recovery](site-recovery-overview.md) usługi.

Opcjonalnie można zarządzać hostami funkcji Hyper-V w chmurach prywatnych programu System Center Virtual Machine Manager (VMM).



## <a name="architectural-components---hyper-v-without-vmm"></a>Składniki architektury — funkcji Hyper-V bez programu VMM

Poniższej tabeli i grafika przedstawia ogólny widok składników używany na potrzeby replikacji funkcji Hyper-V do platformy Azure, gdy hosty funkcji Hyper-V nie są zarządzane przez program VMM.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure, konta usługi Azure storage i sieci platformy Azure. | Replikowane dane z lokalnych obciążeń maszyny Wirtualnej są przechowywane na koncie magazynu. Maszyny wirtualne platformy Azure są tworzone przy użyciu danych replikowanych obciążeń, podczas przejścia do trybu failover z lokacji lokalnej.<br/><br/> Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Funkcja Hyper-V** | Podczas wdrażania usługi Site Recovery możesz zebrać hosty funkcji Hyper-V i klastry do lokacji funkcji Hyper-V. Możesz zainstalować agenta dostawcy usługi Azure Site Recovery i Recovery Services na każdym hoście funkcji Hyper-V autonomicznego lub w każdym węźle klastra funkcji Hyper-V. | Dostawca koordynuje replikację za pomocą usługi Site Recovery przez Internet. Agent usługi Recovery Services obsługuje replikację danych.<br/><br/> Komunikacja zarówno ze strony dostawcy, jak i agenta, jest bezpieczna i szyfrowana. Zreplikowane dane w usłudze Azure Storage również są szyfrowane.
**Maszyny wirtualne funkcji Hyper-V** | Co najmniej jeden uruchomionych maszyn wirtualnych funkcji Hyper-v. | Niczego nie trzeba jawnie można instalować na maszynach wirtualnych.


**Funkcji Hyper-V do architektury platformy Azure (bez programu VMM)**

![Architektura](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)



## <a name="architectural-components---hyper-v-with-vmm"></a>Składniki architektury — funkcji Hyper-V za pomocą programu VMM

Poniższej tabeli i grafika przedstawia ogólny widok składników używany na potrzeby replikacji funkcji Hyper-V do platformy Azure, gdy hosty funkcji Hyper-V są zarządzane w chmurach programu VMM.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure, konta usługi Azure storage i sieci platformy Azure. | Replikowane dane z lokalnych obciążeń maszyny Wirtualnej są przechowywane na koncie magazynu. Maszyny wirtualne platformy Azure są tworzone z replikowanych danych, podczas przejścia do trybu failover z lokacji lokalnej.<br/><br/> Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Serwer VMM** | Serwer VMM ma co najmniej jedną chmurę zawierającą hosty funkcji Hyper-V. | Zainstaluj dostawcę usługi Site Recovery na serwerze programu VMM w celu organizowania replikacji usługa Site Recovery i zarejestrować serwer w magazynie usługi Recovery Services.
**Host funkcji Hyper-V** | Co najmniej jeden host/klaster funkcji Hyper-V zarządzany przez program VMM. |  Możesz zainstalować agenta usługi Recovery Services w każdym węźle hosta lub klastra funkcji Hyper-V.
**Maszyny wirtualne funkcji Hyper-V** | Co najmniej jedna maszyna wirtualna uruchomiona na serwerze hosta funkcji Hyper-V. | Niczego nie trzeba jawnie instalować na maszynach wirtualnych.
**Sieć** | Sieci logiczne i maszyn wirtualnych skonfigurowane na serwerze VMM. Sieć maszyny Wirtualnej powinna być połączona z siecią logiczną skojarzoną z chmurą. | Sieci maszyn wirtualnych są mapowane na sieci wirtualne platformy Azure. Podczas tworzenia maszyn wirtualnych platformy Azure po włączeniu trybu failover, są one dodawane do sieci platformy Azure, która jest mapowana na sieć maszyny Wirtualnej.

**Funkcji Hyper-V do architektury platformy Azure (z programem VMM)**

![Składniki](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Proces replikacji

![Funkcji Hyper-V do platformy Azure replikacji](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Proces replikacji i odzyskiwania**


### <a name="enable-protection"></a>Włączanie ochrony

1. Po włączeniu ochrony dla maszyny wirtualnej funkcji Hyper-V, w witrynie Azure Portal lub środowisku lokalnym, zostanie uruchomione zadanie **Włącz ochronę**.
2. To zadanie sprawdza, czy maszyna spełnia wymagania wstępne, a następnie wywołuje metodę [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), aby skonfigurować replikację za pomocą określonych ustawień.
3. Zadanie uruchamia replikację początkową, wywołując metodę [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), aby zainicjować pełną replikację maszyny wirtualnej i wysłać dyski wirtualne maszyny wirtualnej na platformę Azure.
4. To zadanie możesz monitorować na karcie **Zadania**.      ![Lista zadań](media/hyper-v-azure-architecture/image1.png) ![Szczegóły zadania Włącz ochronę](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Początkową replikację danych

1. Po wyzwoleniu replikacji początkowej [migawki maszyny Wirtualnej funkcji Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) migawki.
2. Wirtualne dyski twarde na maszynie Wirtualnej są replikowane pojedynczo, dopóki nie zostaną wszystkie skopiowane na platformę Azure. Może to potrwać kilka minut w zależności od rozmiaru maszyny Wirtualnej i przepustowości sieci. [Dowiedz się, jak](https://support.microsoft.com/kb/3056159) zwiększyć przepustowość sieci.
3. Jeśli zmiany dysku podczas replikacji początkowej jest w toku, funkcji Hyper-V Replica Replication Tracker śledzi zmiany jako dzienniki replikacji funkcji Hyper-V (hrl). Te pliki dziennika znajdują się w tym samym folderze co dyski. Każdy dysk ma skojarzony plik hrl wysyłanego do magazynu pomocniczego. Pliki migawki i dziennika zużywają zasoby dysku w trakcie replikacji początkowej.
4. Po zakończeniu replikacji początkowej migawka maszyny wirtualnej jest usuwana.
5. Różnicowe zmiany dysku w dzienniku są synchronizowane i scalane z dyskiem nadrzędnym.


### <a name="finalize-protection-process"></a>Zakończ proces ochrony

1. Po zakończeniu replikacji początkowej **Finalizuj ochronę na maszynie wirtualnej** uruchomienia zadania. Jego konfiguruje ustawienia sieciowe i inne ustawienia po replikacji, aby maszyna wirtualna jest chroniona.
2. Na tym etapie możesz sprawdzić ustawienia maszyny Wirtualnej, aby upewnić się, że jest gotowa do trybu failover. Możesz uruchomić odzyskiwanie po awarii (Testowanie trybu failover) dla maszyny Wirtualnej, aby sprawdzić, czy niepowodzenia ponad zgodnie z oczekiwaniami. 


## <a name="delta-replication"></a>Replikacja różnicowa

1. Po przeprowadzeniu replikacji początkowej replikacji różnicowej rozpoczyna się, zgodnie z zasadami replikacji.
2. Funkcji Hyper-V Replica Replication Tracker śledzi zmiany na wirtualnym dysku twardym w plikach hrl. Z każdym dyskiem skonfigurowanym pod kątem replikacji jest skojarzony plik hrl.
3. Dziennik jest wysyłany do konta magazynu klienta. Gdy dziennika jest przesyłany do platformy Azure, zmiany na dysku podstawowym są śledzone w innym pliku dziennika, w tym samym folderze.
4. Podczas replikacji początkowej i różnicowej możesz monitorować maszynę Wirtualną w witrynie Azure portal.

### <a name="resynchronization-process"></a>Proces ponownej synchronizacji

1. Jeśli replikacja różnicowa nie powiedzie się, a pełna replikacja byłaby kosztowna pod względem przepustowości lub czasu, maszyna wirtualna jest oznaczana do ponownej synchronizacji.
    - Jeśli na przykład pliki hrl będą zajmować 50% rozmiaru dysku, to maszyna wirtualna zostanie oznaczona do ponownej synchronizacji.
    -  Domyślnie podczas ponownej synchronizacji jest zaplanowane do automatycznego uruchamiania poza godzinami pracy.
1.  Ponowna synchronizacja wysyła tylko danych różnicowych.
    - Minimalizuje ilość wysyłanych danych dzięki obliczaniu sum kontrolnych źródłowych i docelowych maszyn wirtualnych.
    - Jej używa algorytmu fragmentacji stałym bloku, w której pliki źródłowe i docelowe są dzielone na stałe fragmenty.
    - Sum kontrolnych dla każdego fragmentu są generowane. Te są porównywane w celu określenia, które bloki ze źródła, należy zastosować do obiektu docelowego.
2. Po ukończeniu ponownej synchronizacji replikacja różnicowa powinna zostać wznowiona.
3. Jeśli nie chcesz czekać na domyślne podczas ponownej synchronizacji poza godzinami, należy ponownie zsynchronizować Maszynę wirtualną ręcznie. Na przykład, jeśli wystąpi awaria. W tym celu w witrynie Azure portal wybierz maszynę Wirtualną > **ponowną synchronizację**.

    ![Ręczna ponowna synchronizacja](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Ponów próbę wykonania procesu

Jeśli wystąpi błąd replikacji, może zostać użyty wbudowany mechanizm ponawiania. Ponów próbę jest klasyfikowany zgodnie z opisem w tabeli.

**Kategoria** | **Szczegóły**
--- | ---
**Błąd nieodwracalny** | Nie jest podejmowana próba ponowienia. Maszyna wirtualna będzie mieć stan **Krytyczny** i będzie wymagana interwencja administratora.<br/><br/> Błędy te przykłady przerwany łańcuch dysków VHD, nieprawidłowy stan repliki maszyny Wirtualnej, błędy uwierzytelniania sieci, błędy autoryzacji i maszyny Wirtualnej nie można odnaleźć błędy (w przypadku autonomicznych serwerów funkcji Hyper-V.
**Błędy odwracalne** | Ponowne próby są wykonywane co interwał replikacji przy użyciu wycofywania wykładniczego zwiększającego interwał ponawiania prób od początku pierwszej próby o 1, 2, 4, 8 i 10 minut. Jeśli błąd będzie się powtarzać, ponowne próby będą wykonywane co 30 minut. Oto kilka przykładów: błędy sieci, błędy małej ilości i warunki małej ilości pamięci.



## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

1. Na platformie Azure, można uruchomić planowanego lub nieplanowanego trybu failover z maszynami wirtualnymi funkcji Hyper-V w środowisku lokalnym. Jeśli zostanie uruchomione planowane przejście w tryb failover, źródłowe maszyny wirtualne zostaną wyłączone w celu zapewnienia, że nie będzie miała miejsca utrata danych. Jeśli w lokacji głównej nie jest dostępny, należy uruchomić nieplanowany tryb failover.
2. W trybie Failover pojedynczą maszynę lub tworzyć plany odzyskiwania, aby organizować tryb failover wiele maszyn.
3. Możesz uruchomić tryb failover. Po zakończeniu pierwszego etapu przejścia w tryb failover, należy można zobaczyć utworzone repliki maszyn wirtualnych na platformie Azure. Jeśli jest to wymagane, do maszyny wirtualnej można przypisać publiczny adres IP.
4. Następnie następuje zatwierdzenie trybu failover, aby można było rozpocząć uzyskiwanie dostępu do obciążenia z poziomu repliki maszyny Wirtualnej platformy Azure.

Gdy infrastruktury lokalnej będzie uruchomione ponownie, możesz można wykonać powrotu po awarii. Powrót po awarii odbywa się w trzech etapach:

1. Czy uruchamiał planowanego trybu failover z platformy Azure do lokacji lokalnej:
    - **Skrócić czas przestoju**: Jeśli używasz tej opcji usługa Site Recovery synchronizuje dane przed włączeniem trybu failover. Sprawdza, czy dane zmienione bloki i pobiera je do lokacji lokalnej, podczas przechowuje maszyny Wirtualnej platformy Azure, działa, minimalizując przestoje. Ręczne określenie, czy tryb failover, należy wykonać, zamknięcie maszyny Wirtualnej platformy Azure, wszelkie zmiany różnicowe końcowe są kopiowane i uruchomieniem trybu failover.
    - **Pełne pobieranie**: Po wybraniu tej opcji dane są synchronizowane podczas pracy awaryjnej. Ta opcja spowoduje pobranie całego dysku. Jest szybsze, ponieważ nie sumy kontrolne są obliczane, ale ma więcej przestojów. Użyj tej opcji, jeśli działała repliki maszyn wirtualnych platformy Azure przez pewien czas lub lokalna maszyna wirtualna została usunięta.
    - **Tworzenie maszyny Wirtualnej**: Możesz wybrać, czy niepowodzenie powrót do tej samej maszyny Wirtualnej lub do alternatywnej maszyny Wirtualnej. Można określić, Usługa Site Recovery należy utworzyć maszynę Wirtualną, jeśli jeszcze nie istnieje.

2. Po zakończeniu początkowej synchronizacji, należy wybrać do przełączenia w tryb failover. Po zakończeniu instalacji możesz zalogować się na lokalną maszynę Wirtualną w celu sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami. W witrynie Azure portal widać, że maszyny wirtualne platformy Azure zostały zatrzymane.
3.  Następnie należy zatwierdzić tryb failover, aby zakończyć i rozpocząć uzyskiwanie dostępu do obciążenia z lokalnej maszyny Wirtualnej ponownie.
4. Po obciążeń przywrócono po awarii, możesz włączyć replikacji odwrotnej, tak, aby replikować do platformy Azure ponownie lokalnych maszyn wirtualnych.



## <a name="next-steps"></a>Kolejne kroki


Postępuj zgodnie z [w tym samouczku](tutorial-prepare-azure.md) wprowadzenie funkcji Hyper-V do replikacji platformy Azure.


