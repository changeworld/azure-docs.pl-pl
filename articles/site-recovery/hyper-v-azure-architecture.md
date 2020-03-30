---
title: Architektura odzyskiwania po awarii funkcji Hyper-V w usłudze Azure Site Recovery
description: Ten artykuł zawiera omówienie składników i architektury używane podczas wdrażania odzyskiwania po awarii dla lokalnych maszyn wirtualnych funkcji Hyper-V (bez programu VMM) na platformie Azure za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 022d6edad1e907173dfde3481e60d2523be087a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082665"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Architektura odzyskiwania po awarii z funkcji Hyper-V do platformy Azure


W tym artykule opisano architekturę i procesy używane podczas replikowania, pracy awaryjnej i odzyskiwania maszyn wirtualnych funkcji Hyper-V między lokalnymi hostami funkcji Hyper-V a platformą Azure przy użyciu usługi [Azure Site Recovery.](site-recovery-overview.md)

Hosty funkcji Hyper-V można opcjonalnie zarządzać w chmurach prywatnych Menedżera maszyn wirtualnych (System Center Virtual Machine Manager).



## <a name="architectural-components---hyper-v-without-vmm"></a>Komponenty architektoniczne - Hyper-V bez programu VMM

Poniższa tabela i grafika zapewniają widok wysokiego poziomu składników używanych do replikacji funkcji Hyper-V na platformie Azure, gdy hosty funkcji Hyper-V nie są zarządzane przez program VMM.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure, konto magazynu platformy Azure i sieć platformy Azure. | Replikowane dane z lokalnych obciążeń maszyn wirtualnych są przechowywane na koncie magazynu. Maszyny wirtualne platformy Azure są tworzone przy obliczu replikowanych danych obciążenia po wykonaniu pracy awaryjnej z witryny lokalnej.<br/><br/> Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Funkcja Hyper-V** | Podczas wdrażania usługi Site Recovery można zbierać hosty funkcji Hyper-V i klastry do lokacji funkcji Hyper-V. Dostawca odzyskiwania witryny platformy Azure i agent usług odzyskiwania usługi na każdym autonomicznym hoście funkcji Hyper-V lub w każdym węźle klastra funkcji Hyper-V. | Dostawca koordynuje replikację za pomocą usługi Site Recovery przez Internet. Agent usługi Recovery Services obsługuje replikację danych.<br/><br/> Komunikacja zarówno ze strony dostawcy, jak i agenta, jest bezpieczna i szyfrowana. Zreplikowane dane w usłudze Azure Storage również są szyfrowane.
**Maszyny wirtualne funkcji Hyper-V** | Co najmniej jedna maszyna wirtualna uruchomiona w funkcji Hyper-V. | Nic nie musi być jawnie zainstalowane na maszynach wirtualnych.


**Architektura funkcji Hyper-V do platformy Azure (bez programu VMM)**

![Architektura](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)


## <a name="architectural-components---hyper-v-with-vmm"></a>Komponenty architektoniczne - Hyper-V z programem VMM

Poniższa tabela i grafika zapewniają widok wysokiego poziomu składników używanych do replikacji funkcji Hyper-V na platformie Azure, gdy hosty funkcji Hyper-V są zarządzane w chmurach programu VMM.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure, konto magazynu platformy Azure i sieć platformy Azure. | Replikowane dane z lokalnych obciążeń maszyn wirtualnych są przechowywane na koncie magazynu. Maszyny wirtualne platformy Azure są tworzone przy obliczu replikowanych danych po wykonaniu pracy awaryjnej z lokacji lokalnej.<br/><br/> Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Serwer VMM** | Serwer VMM ma co najmniej jedną chmurę zawierającą hosty funkcji Hyper-V. | Należy zainstalować dostawcę odzyskiwania lokacji na serwerze programu VMM, aby zorganizować replikację za pomocą usługi Site Recovery i zarejestrować serwer w magazynie usług odzyskiwania.
**Host funkcji Hyper-V** | Co najmniej jeden host/klaster funkcji Hyper-V zarządzany przez program VMM. |  Agent usług odzyskiwania jest instalowyny na każdym hoście funkcji Hyper-V lub w węźle klastra.
**Maszyny wirtualne funkcji Hyper-V** | Co najmniej jedna maszyna wirtualna uruchomiona na serwerze hosta funkcji Hyper-V. | Niczego nie trzeba jawnie instalować na maszynach wirtualnych.
**Obsługa sieci** | Sieci logiczne i maszyn wirtualnych skonfigurowane na serwerze VMM. Sieć maszyn wirtualnych powinna być połączona z siecią logiczną skojarzoną z chmurą. | Sieci maszyn wirtualnych są mapowane do sieci wirtualnych platformy Azure. Gdy maszyny wirtualne platformy Azure są tworzone po przełączeniu w tryb failover, są one dodawane do sieci platformy Azure, która jest mapowana do sieci maszyn wirtualnych.

**Architektura funkcji Hyper-V do platformy Azure (z programem VMM)**

![Składniki](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Proces replikacji

![Replikacja funkcji Hyper-V na platformę Azure](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Proces replikacji i odzyskiwania**


### <a name="enable-protection"></a>Włączanie ochrony

1. Po włączeniu ochrony dla maszyny wirtualnej funkcji Hyper-V, w witrynie Azure Portal lub środowisku lokalnym, zostanie uruchomione zadanie **Włącz ochronę**.
2. To zadanie sprawdza, czy maszyna spełnia wymagania wstępne, a następnie wywołuje metodę [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), aby skonfigurować replikację za pomocą określonych ustawień.
3. Zadanie uruchamia replikację początkową, wywołując metodę [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), aby zainicjować pełną replikację maszyny wirtualnej i wysłać dyski wirtualne maszyny wirtualnej na platformę Azure.
4. Zadanie można monitorować na karcie **Zadania.**      ![](media/hyper-v-azure-architecture/image1.png) Lista ![zadań Włącz dotowanie ochrony](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Replikacja danych początkowych

1. Po wyzwoleniu replikacji początkowej jest pobierana migawka [migawki maszyny Wirtualnej funkcji Funkcji Hyper V.](https://technet.microsoft.com/library/dd560637.aspx)
2. Wirtualne dyski twarde na maszynie wirtualnej są replikowane jeden po drugim, dopóki nie zostaną skopiowane na platformę Azure. Może to trochę potrwać, w zależności od rozmiaru maszyny Wirtualnej i przepustowości sieci. [Dowiedz się, jak](https://support.microsoft.com/kb/3056159) zwiększyć przepustowość sieci.
3. Jeśli zmiany dysku wystąpią podczas replikacji początkowej, moduł śledzenia replik repliki funkcji Hyper-V śledzi zmiany jako dzienniki replikacji funkcji Hyper-V (hrl). Te pliki dziennika znajdują się w tym samym folderze co dyski. Każdy dysk ma skojarzony plik hrl, który jest wysyłany do magazynu pomocniczego. Pliki migawki i dziennika zużywają zasoby dysku w trakcie replikacji początkowej.
4. Po zakończeniu replikacji początkowej migawka maszyny wirtualnej jest usuwana.
5. Różnicowe zmiany dysku w dzienniku są synchronizowane i scalane z dyskiem nadrzędnym.


### <a name="finalize-protection-process"></a>Finalizowanie procesu ochrony

1. Po zakończeniu replikacji początkowej zostanie **uruchamiana ochrona Finalize na maszynie wirtualnej.** Konfiguruje sieci i inne ustawienia po replikacji, tak aby maszyna wirtualna jest chroniona.
2. Na tym etapie można sprawdzić ustawienia maszyny Wirtualnej, aby upewnić się, że jest gotowy do pracy awaryjnej. Można uruchomić wiertło odzyskiwania po awarii (test pracy awaryjnej) dla maszyny Wirtualnej, aby sprawdzić, czy przechodzi w stan failover zgodnie z oczekiwaniami. 


## <a name="delta-replication"></a>Replikacja różnicowa

1. Po replikacji początkowej replikacja delta rozpoczyna się zgodnie z zasadami replikacji.
2. Moduł śledzenia replik repliki funkcji Hyper-V śledzi zmiany na wirtualnym dysku twardym jako pliki hrl. Z każdym dyskiem skonfigurowanym pod kątem replikacji jest skojarzony plik hrl.
3. Dziennik jest wysyłany do konta magazynu klienta. Gdy dziennik jest przesyłany na platformę Azure, zmiany na dysku podstawowym są śledzone w innym pliku dziennika w tym samym folderze.
4. Podczas replikacji początkowej i delta można monitorować maszynę wirtualną w witrynie Azure portal.

### <a name="resynchronization-process"></a>Proces ponownej synchronizowania

1. Jeśli replikacja różnicowa nie powiedzie się, a pełna replikacja byłaby kosztowna pod względem przepustowości lub czasu, maszyna wirtualna jest oznaczana do ponownej synchronizacji.
    - Jeśli na przykład pliki hrl będą zajmować 50% rozmiaru dysku, to maszyna wirtualna zostanie oznaczona do ponownej synchronizacji.
    -  Domyślnie ponowna synchronizacja jest zaplanowana do automatycznego uruchamiania poza godzinami pracy biura.
1.  Ponowna synchronizacja wysyła tylko dane różnicowe.
    - Minimalizuje ilość danych wysyłanych przez obliczanie sum kontrolnych źródłowych i docelowych maszyn wirtualnych.
    - Używa algorytmu fragmentowania stałego bloku, w którym pliki źródłowe i docelowe są podzielone na stałe fragmenty.
    - Sumy kontrolne dla każdego fragmentu są generowane. Są one porównywane w celu określenia, które bloki ze źródła muszą być stosowane do obiektu docelowego.
2. Po ukończeniu ponownej synchronizacji replikacja różnicowa powinna zostać wznowiona.
3. Jeśli nie chcesz czekać na domyślną ponowną synchronizację poza godzinami, możesz ponownie zsynchronizować maszynę wirtualną ręcznie. Na przykład, jeśli wystąpi awaria. Aby to zrobić, w witrynie Azure portal wybierz maszynę wirtualną > **Resynchronize**.

    ![Ręczna ponowna synchronizacja](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Ponów próbę procesu

Jeśli wystąpi błąd replikacji, może zostać użyty wbudowany mechanizm ponawiania. Ponów próbę jest klasyfikowany zgodnie z opisem w tabeli.

**Kategoria** | **Szczegóły**
--- | ---
**Błąd nieodwracalny** | Nie jest podejmowana próba ponowienia. Maszyna wirtualna będzie mieć stan **Krytyczny** i będzie wymagana interwencja administratora.<br/><br/> Przykładami tych błędów są uszkodzony łańcuch wirtualnej rzeczywistości wirtualnej, nieprawidłowy stan repliki maszyny Wirtualnej, błędy uwierzytelniania sieciowego, błędy autoryzacji i błędy maszyny Wirtualnej (dla autonomicznych serwerów Hyper-V.
**Błędy odwracalne** | Ponowne próby są wykonywane co interwał replikacji przy użyciu wycofywania wykładniczego zwiększającego interwał ponawiania prób od początku pierwszej próby o 1, 2, 4, 8 i 10 minut. Jeśli błąd będzie się powtarzać, ponowne próby będą wykonywane co 30 minut. Przykłady obejmują błędy sieciowe, błędy dysku o niskim poziomie i niski poziom pamięci.



## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

1. Planowane lub nieplanowane przejście w tryb failover można uruchomić z maszyn wirtualnych funkcji Hyper-V do platformy Azure. Jeśli zostanie uruchomione planowane przejście w tryb failover, źródłowe maszyny wirtualne zostaną wyłączone w celu zapewnienia, że nie będzie miała miejsca utrata danych. Uruchom nieplanowaną przełączoną w błąd, jeśli witryna główna nie jest dostępna.
2. W tryb failover można przełączyć pojedynczą maszynę lub można utworzyć plany odzyskiwania, aby zaaranżować tryb failover na wielu maszynach.
3. Uruchom przemiebienie awaryjne. Po zakończeniu pierwszego etapu pracy awaryjnej powinien być widoczny utworzone maszyny wirtualne repliki na platformie Azure. Jeśli jest to wymagane, do maszyny wirtualnej można przypisać publiczny adres IP.
4. Następnie zatwierdzić pracy awaryjnej, aby rozpocząć dostęp do obciążenia z repliki maszyny Wirtualnej platformy Azure.

Po ponownym uruchomieniu infrastruktury lokalnej można wrócić do komputera. Powrót po awarii występuje w trzech etapach:

1. Rozpoczęcie planowanej pracy awaryjnej z platformy Azure do lokacji lokalnej:
    - **Minimalizowanie przestojów:** Jeśli używasz tej opcji Usługa Site Recovery synchronizuje dane przed przełączeniem awaryjnym. Sprawdza, czy nie ma zmienionych bloków danych i pobiera je do lokacji lokalnej, podczas gdy maszyna wirtualna platformy Azure jest uruchomiona, minimalizując przestoje. Po ręcznym określeniu, że praca awaryjna powinna zakończyć się, maszyna wirtualna platformy Azure jest zamykana, wszelkie ostateczne zmiany delta są kopiowane i rozpoczyna się praca awaryjna.
    - **Pełne pobieranie**: Dzięki tej opcji dane są synchronizowane podczas pracy awaryjnej. Ta opcja powoduje pobranie całego dysku. Jest szybszy, ponieważ nie są obliczane sumy kontrolne, ale jest więcej przestojów. Użyj tej opcji, jeśli używasz repliki maszyn wirtualnych platformy Azure od jakiegoś czasu lub jeśli lokalna maszyna wirtualna została usunięta.
    - **Utwórz maszynę wirtualną:** Można wybrać, aby powiększyć powiększe z powrotem do tej samej maszyny wirtualnej lub do alternatywnej maszyny Wirtualnej. Można określić, że odzyskiwanie witryny należy utworzyć maszynę wirtualną, jeśli jeszcze nie istnieje.

2. Po zakończeniu synchronizacji początkowej należy wybrać, aby zakończyć przebienie awaryjne. Po jej zakończeniu można zalogować się do lokalnej maszyny Wirtualnej, aby sprawdzić wszystko działa zgodnie z oczekiwaniami. W witrynie Azure portal, można zobaczyć, że maszyny wirtualne platformy Azure zostały zatrzymane.
3.  Następnie należy zatwierdzić pracę awaryjną, aby zakończyć i rozpocząć dostęp do obciążenia z lokalnej maszyny Wirtualnej ponownie.
4. Po zakończeniu pracy obciążeń po awarii, można włączyć replikację odwrotną, tak aby lokalne maszyny wirtualne replikować na platformie Azure ponownie.



## <a name="next-steps"></a>Następne kroki


Wykonaj [ten samouczek,](tutorial-prepare-azure.md) aby rozpocząć pracę z replikacją funkcji Hyper-V na platformę Azure.


