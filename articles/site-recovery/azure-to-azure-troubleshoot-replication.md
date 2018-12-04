---
title: Usługa Azure Site Recovery Rozwiązywanie problemów z błędów i problemów z replikacją Azure – Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z błędów i problemów podczas replikowania maszyn wirtualnych platformy Azure w celu odzyskiwania po awarii
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 9a32ac1ae71cb7bd89c4252157c3a5cd395b2694
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842343"
---
# <a name="troubleshoot-azure-to-azure-vm-ongoing-replication-issues"></a>Rozwiązywanie problemów trwającej replikacji maszyny Wirtualnej platformy Azure do platformy Azure

W tym artykule opisano, jakie są najczęstsze problemy w usłudze Azure Site Recovery podczas replikacji i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego regionu i wyjaśniono, jak rozwiązywać problemy z nimi. Aby uzyskać więcej informacji o obsługiwanych konfiguracjach, zobacz [macierz obsługi do replikowania maszyn wirtualnych platformy Azure](site-recovery-support-matrix-azure-to-azure.md).


## <a name="recovery-points-not-getting-generated"></a>Punkty odzyskiwania nie generowanych

Komunikat o błędzie: Brak awarii spójne dostępnego punktu odzyskiwania dla maszyny Wirtualnej w ciągu ostatnich 60 minut.</br>
IDENTYFIKATOR BŁĘDU: 153007 </br>

Usługa Azure Site Recovery stale replikuje dane w regionie źródłowym w regionie odzyskiwania po awarii i tworzy punktu spójnego na poziomie awarii, co 5 minut. Jeśli usługa Site Recovery nie może utworzyć punktów odzyskiwania na 60 minut, następnie powiadamia użytkownika. Poniżej przedstawiono przyczyny, które może spowodować błąd:

**Przyczyny 1: [dużej ilości danych zmiany stawki na źródłowej maszynie wirtualnej](#high-data-change-rate-on-the-source-virtal-machine)**    
**Przyczyny 2: [problem z połączeniem sieciowym ](#Network-connectivity-issue)**

## <a name="causes-and-solutions"></a>Przyczyny i potencjalne rozwiązania

### <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Dużej ilości danych, zmień stawki dla źródłowej maszyny wirtualnej
Usługa Azure Site Recovery uruchamia zdarzenie, jeśli współczynnik zmian danych na źródłowej maszynie wirtualnej jest większy niż obsługiwany limit. Aby sprawdzić, czy problem dotyczy ze względu na dużą liczbą zmian, przejdź do elementów zreplikowany > maszynę Wirtualną > kliknij pozycję "zdarzenia — ostatnie 72 godziny".
Zdarzenie "Zmiana danych częstotliwość wykracza poza obsługiwane limity" powinien zostać wyświetlony, jak pokazano na poniższym zrzucie ekranu

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Po kliknięciu zdarzenia powinien zostać wyświetlony informacje o dysku dokładne jak pokazano na poniższym zrzucie ekranu

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


#### <a name="azure-site-recovery-limits"></a>Limity usługi Azure Site Recovery
W poniższej tabeli przedstawiono limity usługi Azure Site Recovery. Limity te są oparte na naszych testach, ale nie obejmują wszystkich możliwych kombinacji operacji we/wy aplikacji. Rzeczywiste wyniki mogą różnić w zależności od kombinacji operacji we/wy aplikacji. Firma Microsoft należy należy również zauważyć, że istnieją dwa ograniczenia należy wziąć pod uwagę na dysk danych na dane maszyny wirtualnej postępów i współczynnika zmian.
Na przykład, jeśli spojrzymy na dysk Premium P20 w poniższej tabeli, Site Recovery może obsługiwać 5 MB/s współczynniku dysku na maksymalnie pięciu tych dysków na maszynę Wirtualną z powodu limitu 25 MB/s łącznym dziennym współczynniku maszyny Wirtualnej.

**Cel magazynu replikacji** | **Średni rozmiar źródłowych operacji we/wy na dysku** |**Średni źródłowy współczynnik zmian danych na dysku** | **Łączny współczynnik zmian danych na dysku dziennie**
---|---|---|---
Standard Storage | 8 KB | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB  | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |  336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy |10 MB/s | 842 GB na dysk

### <a name="solution"></a>Rozwiązanie
Należy rozumiemy, że usługi Azure Site Recovery ma zmienić limitów szybkości na podstawie typu dysku danych. Aby dowiedzieć się, czy ten problem ma charakter cykliczny chwilowo, ważne jest, aby znaleźć wzorzec stawka dotyczy maszyny wirtualnej zmian danych.
Stawka dotyczy maszyny wirtualnej można znaleźć zmian danych. Przejdź do źródłowej maszyny wirtualnej > metryki w obszarze monitorowanie i Dodaj metryki, jak pokazano poniżej.

![high_data_change_rate](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Kliknij przycisk "Dodaj metryki" i Dodaj "Dysk systemu operacyjnego zapisane bajty/s" i "Dysku danych zapisane bajty/s".
2. Monitoruj kolekcji, jak pokazano na zrzucie ekranu.
3. Widoczna będzie, że suma zapisuje działania wykonywane przez dysk systemu operacyjnego i wszystkie dyski z danymi w połączeniu. Teraz te metryki nie mogą informować na informacje o poziomie dysku, ale to dobry wskaźnik wzorzec zmian łączna ilość danych.

W takich przypadkach powyżej, jeśli jest serii danych sporadyczne i współczynnik zmian danych jest większa niż 10 MB/s (dla warstwy Premium) oraz 2 MB/s (dla warstwy standardowa) przez pewien czas i sprowadza się, Replikacja zostanie nadążyć. Jednak jeśli zmian jest również przekracza obsługiwany limit w większości przypadków, następnie należy rozważyć jedną z poniższych opcji, jeśli jest to możliwe:

**Opcja 1:** wykluczyć dysk, co powoduje, że współczynnik zmian danych: </br>
Obecnie można wykluczyć dysk, przy użyciu [odzyskiwania lokacji w programie Powershell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine)

**Opcja 2:** zmienić warstwę dysku magazynu odzyskiwania po awarii: </br>
Ta opcja jest możliwe tylko wtedy, jeśli zmian danych na dysku jest mniejsza niż 10 MB/s. Pozwól, że maszyna wirtualna o P10 dysku ma wartość współczynnika zmian danych przekracza 8 MB/s, ale mniej niż 10 MB/s. Jeśli klienta można użyć P30 dysku dla magazynu docelowego podczas ochrony, ten problem można rozwiązać.

### <a name="Network-connectivity-issue"></a>Problem z łącznością sieciową

#### <a name="network-latency-to-cache-storage-account-"></a>Opóźnienie sieci do konta magazynu pamięci podręcznej:
 Usługa Site Recovery wysyła replikowane dane na koncie magazynu pamięci podręcznej, a ten problem może się zdarzyć, jeśli przekazywanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej jest mniejsza tego 4 MB 3 sekund. Aby sprawdzić, czy występuje problem z dowolnego związane z opóźnieniem, użyj [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) do przekazania danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej.<br>
Jeśli opóźnienie jest wysoka, sprawdź, jeśli używasz wirtualnych urządzeń sieciowych do kontrolowania wychodzącego ruchu sieciowego z maszynami wirtualnymi. Urządzenie może być ograniczona w przypadku wszystkich ruch związany z replikacją przechodzi przez urządzenia WUS. Zaleca się utworzenie punktu końcowego usługi sieci w sieci wirtualnej na "Magazyn", aby ruch związany z replikacją nie przechodzi do urządzenia WUS. Zapoznaj się [konfiguracji urządzenia wirtualnego sieci](https://docs.microsoft.com/en-us/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)

#### <a name="network-connectivity"></a>Połączenie sieciowe
W przypadku replikacji usługi Site Recovery do pracy, łączność wychodząca z określonych adresów URL lub IP zakresów jest wymagane z maszyny Wirtualnej. Jeśli maszyna wirtualna znajduje się za zaporą lub używa reguł Sieciowej grupy zabezpieczeń sieci do sterowania ruchem wychodzącym, może być jedną z tych problemów twarzy.</br>
Zapoznaj się [połączenia ruchu wychodzącego dla adresów URL Site Recovery](https://docs.microsoft.com/en-us/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) aby upewnić się, wszystkie adresy URL są połączone. 