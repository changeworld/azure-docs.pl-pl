---
title: Usługa Azure Site Recovery dotyczące rozwiązywania problemów w trwające problemy z replikacją Azure – Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów i błędów podczas replikowania maszyn wirtualnych platformy Azure w celu odzyskiwania po awarii
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 9ff756270c368d39b7ef78d7c1046f7c91169668
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103750"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Trwającą Rozwiązywanie problemów z w replikacji maszyn wirtualnych platformy Azure do platformy Azure

W tym artykule opisano typowe problemy w usłudze Azure Site Recovery podczas replikacji i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego regionu. Wyjaśniono również sposób rozwiązać ten problem. Aby uzyskać więcej informacji o obsługiwanych konfiguracjach, zobacz [macierz obsługi do replikowania maszyn wirtualnych platformy Azure](site-recovery-support-matrix-azure-to-azure.md).

Usługa Azure Site Recovery stale replikuje dane w regionie źródłowym w regionie odzyskiwania po awarii i tworzy punkt odzyskiwania spójnego na poziomie awarii, co 5 minut. Jeśli usługa Site Recovery nie może utworzyć punktów odzyskiwania na 60 minut, ostrzega użytkownika z tymi informacjami:

Komunikat o błędzie: "Brak awarii spójne dostępnego punktu odzyskiwania dla maszyny Wirtualnej w ciągu ostatnich 60 minut."</br>
Identyfikator błędu: 153007 </br>

W poniższych sekcjach opisano przyczyny i potencjalne rozwiązania.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Współczynnik zmian danych na źródłowej maszynie wirtualnej
Usługa Azure Site Recovery uruchamia zdarzenie, jeśli współczynnik zmian danych na źródłowej maszynie wirtualnej jest większy niż obsługiwany limit. Aby sprawdzić, czy problem jest spowodowane dużą liczbą zmian, przejdź do **zreplikowane elementy** > **maszyny Wirtualnej** > **zdarzenia — ostatnie 72 godziny**.
Zdarzenia powinny zostać wyświetlone "Współczynnik wykracza poza obsługiwane limity zmian danych":

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Jeśli wybierzesz zdarzenia, powinny zostać wyświetlone informacje o dysku:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Limity usługi Azure Site Recovery
W poniższej tabeli przedstawiono limity usługi Azure Site Recovery. Limity te są oparte na naszych testach, ale nie obejmują wszystkich możliwych operacji We/Wy kombinacji aplikacji. Rzeczywiste wyniki mogą różnić w zależności od kombinacji operacji we/wy aplikacji. 

Istnieją dwa ograniczenia należy wziąć pod uwagę, współczynnik zmian danych na dysku i współczynnik zmian danych maszyny wirtualnej. Na przykład Przyjrzyjmy się dysk Premium P20 w poniższej tabeli. Usługa Site Recovery może obsługiwać 5 MB/s o współczynniku dysku z maksymalnie pięć tych dysków na maszynę Wirtualną, ze względu na limit wynoszący 25 MB/s z łącznym dziennym współczynniku maszyny Wirtualnej.

**Cel magazynu replikacji** | **Średniego rozmiaru operacji We/Wy na dysk źródłowy** |**Średni współczynnik zmian danych dla dysku źródłowego** | **Łączna ilość danych, współczynnika zmian dziennie w przypadku dysk danych źródłowych**
---|---|---|---
Standard Storage | 8 KB | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB  | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |  336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy |10 MB/s | 842 GB na dysk

### <a name="solution"></a>Rozwiązanie
Usługa Azure Site Recovery ma ograniczenia częstotliwości zmian danych, w zależności od typu dysku. Aby dowiedzieć się, jeśli ten problem dotyczy przechwytują lub cykliczne, znaleźć je zmienić częstotliwość dotyczy maszyny wirtualnej. Przejdź do źródłowej maszyny wirtualnej i metryki w obszarze **monitorowanie**i Dodaj metryki, jak pokazano w tym zrzucie ekranu:

![Trzy kroki proces służące do znajdowania współczynnik zmian danych](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Wybierz **Dodaj metrykę**i Dodaj **systemu operacyjnego zapisu Bajty dysku/s** i **danych zapisu Bajty dysku/s**.
2. Monitoruj kolekcji, jak pokazano na zrzucie ekranu.
3. Widok Suma zapisu operacje wykonywane przez dyski systemu operacyjnego i połączyć wszystkie dyski z danymi. Te metryki może nie dać użytkownikowi informacje na poziomie na dysku, ale wskazują wzorzec łączny współczynnik zmian danych.

W przypadku kolekcji jest ze sporadycznymi danych serii i danych zmiany stawka jest większa niż 10 MB/s (dla warstwy Premium) oraz 2 MB/s (dla warstwy standardowa) dla niektórych czasu i sprowadza się, zostanie nadrób zaległości replikacji. Ale jeśli zmian poza obsługiwane ograniczenie w większości przypadków, należy wziąć pod uwagę jedną z tych opcji, jeśli jest to możliwe:

* **Wykluczanie dysku, który powoduje współczynnik zmian danych o wysokiej**: Dysk można wykluczyć za pomocą [PowerShell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine).
* **Zmień warstwę dysk magazynujący odzyskiwania po awarii**: Ta opcja jest możliwe tylko w przypadku zmian danych na dysku jest mniejsza niż 10 MB/s. Załóżmy, że Maszynę wirtualną z dyskiem P10 ma wartość współczynnika zmian danych przekracza 8 MB/s, ale mniej niż 10 MB/s. Jeśli odbiorca może używać dysk P30 dla magazynu docelowego podczas ochrony, problem może zostać rozwiązany.

## <a name="Network-connectivity-problem"></a>Problemy z łącznością sieciową

### <a name="network-latency-to-a-cache-storage-account"></a>Opóźnienie sieci z kontem magazynu pamięci podręcznej
Usługa Site Recovery wysyła dane replikowane do konta magazynu pamięci podręcznej. Może pojawić się opóźnienie sieci, w przypadku przekazywania danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej jest mniejsza niż 4 MB w 3 sekundy. 

Aby sprawdzić, czy problem związany z opóźnieniem, użyj [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) do przekazania danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej. Opóźnienie jest wysoka, sprawdź, w przypadku korzystania z wirtualnego urządzenia sieciowego (WUS) do kontrolowania wychodzącego ruchu sieciowego z maszynami wirtualnymi. Urządzenie może być ograniczona w przypadku wszystkich ruch związany z replikacją przechodzi przez urządzenia WUS. 

Zaleca się utworzenie punktu końcowego usługi sieci w sieci wirtualnej na "Magazyn", aby ruch związany z replikacją nie prowadzi do urządzenia WUS. Aby uzyskać więcej informacji, zobacz [konfiguracji urządzenia wirtualnego sieci](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Połączenie sieciowe
W przypadku replikacji usługi Site Recovery do pracy, łączność wychodząca z określonych adresów URL lub IP zakresów jest wymagane z maszyny Wirtualnej. Jeśli maszyna wirtualna znajduje się za zaporą lub używa reguł Sieciowej grupy zabezpieczeń sieci do sterowania ruchem wychodzącym, może być jedną z tych problemów twarzy. Aby upewnić się, wszystkie adresy URL są połączone, zobacz [połączenia ruchu wychodzącego dla adresów URL Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 