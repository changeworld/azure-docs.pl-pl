---
title: Optymalizuj wydajność na maszynach wirtualnych z serii Lsv2 platformy Azure — Magazyn
description: Dowiedz się, jak zoptymalizować wydajność rozwiązania na maszynach wirtualnych serii Lsv2.
services: virtual-machines-windows
author: sasha-melamed
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 57b248908a02327f2521be05920259681a26817a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920233"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Optymalizacja wydajności na maszynach wirtualnych serii Lsv2

Maszyny wirtualne serii Lsv2 obsługują różne obciążenia, które wymagają wysokiej wydajności we/wy i przepustowości w lokalnej pamięci masowej w wielu różnych aplikacjach i branżach.  Seria Lsv2 jest idealna dla baz danych Big Data, SQL, NoSQL, hurtowni danych i dużych transakcyjnych baz danych, w tym Cassandra, MongoDB, Cloudera i Redis.

Konstrukcja maszyn wirtualnych serii Lsv2 (VMs) maksymalizuje procesor AMD EPYC™ 7551, aby zapewnić najlepszą wydajność między procesorem, pamięcią, urządzeniami NVMe i maszynami wirtualnymi. Oprócz maksymalizacji wydajności sprzętu maszyny wirtualne z serii Lsv2 są przeznaczone do pracy z potrzebami systemów operacyjnych Windows i Linux w celu uzyskania lepszej wydajności ze sprzętem i oprogramowaniem.

Dostrajanie oprogramowania i sprzętu spowodowało zoptymalizowaną wersję [centrum danych systemu Windows Server 2019,](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)wydaną na początku grudnia 2018 r. w portalu Azure Marketplace, która obsługuje maksymalną wydajność na urządzeniach NVMe na maszynach wirtualnych z serii Lsv2.

Ten artykuł zawiera wskazówki i sugestie, aby zapewnić, że obciążenia i aplikacje osiągną maksymalną wydajność zaprojektowaną na maszynach wirtualnych. Informacje na tej stronie będą stale aktualizowane, ponieważ więcej obrazów zoptymalizowanych za pomocą Lsv2 zostanie dodanych do portalu Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Architektura chipsetu AMD EYPC™

Maszyny wirtualne z serii Lsv2 wykorzystują procesory AMD EYPC™ oparte na mikroarchitekturze Zen. Firma AMD opracowała infinity fabric (IF) dla EYPC™ jako skalowalne połączenie dla swojego modelu NUMA, który może być używany do komunikacji on-die, on-package i multi-package. W porównaniu z QPI (Quick-Path Interconnect) i UPI (Ultra-Path Interconnect) używanymi w nowoczesnych monolitycznych procesorach firmy Intel, architektura AMD o małej matrycy NUMA może przynieść zarówno korzyści wydajnościowe, jak i wyzwania. Rzeczywisty wpływ przepustowości pamięci i ograniczenia opóźnienia mogą się różnić w zależności od typu uruchomionych obciążeń.

## <a name="tips-for-maximizing-performance"></a>Wskazówki dotyczące maksymalizacji wydajności

* Sprzęt, który zasila maszyny wirtualne z serii Lsv2, wykorzystuje urządzenia NVMe z ośmioma parami kolejek we/wy (QP). Każda kolejka we/wy urządzenia NVMe jest w rzeczywistości parą: kolejką przesyłania i kolejką zakończenia. Sterownik NVMe jest skonfigurowany do optymalizacji wykorzystania tych ośmiu K/WE/Op poprzez dystrybucję we/wy w harmonogramie okrężnym. Aby uzyskać maksymalną wydajność, uruchom osiem zadań na urządzenie, aby dopasować.

* Unikaj mieszania poleceń administratora NVMe (na przykład zapytania informacyjnego NVMe SMART itp.) z poleceniami We/Wy NVMe podczas aktywnych obciążeń. Urządzenia Lsv2 NVMe są wspierane przez technologię Hyper-V NVMe Direct, która przełącza się w tryb "powolny", gdy zajdą w czasie oczekiwania jakiekolwiek polecenia administratora NVMe. Użytkownicy Lsv2 mogą zobaczyć dramatyczny spadek wydajności we/wy NVMe, jeśli tak się stanie.

* Użytkownicy Lsv2 nie powinni polegać na informacjach NUMA urządzenia (wszystkie 0) zgłoszonych z poziomu maszyny Wirtualnej dla dysków danych, aby zdecydować o koligacji NUMA dla swoich aplikacji. Zalecanym sposobem na lepszą wydajność jest rozłożenie obciążeń na procesory, jeśli to możliwe. 

* Maksymalna obsługiwana głębokość kolejki na parę kolejek We/Wy dla urządzenia Lsv2 VM NVMe wynosi 1024 (w porównaniu z limitem Amazon i3 QD 32). Użytkownicy Lsv2 powinni ograniczyć swoje (syntetyczne) obciążenia porównawcze do głębokości kolejki 1024 lub niższej, aby uniknąć wyzwalania pełnych warunków kolejki, co może zmniejszyć wydajność.

## <a name="utilizing-local-nvme-storage"></a>Wykorzystanie lokalnej pamięci masowej NVMe

Pamięć lokalna na dysku NVMe o pojemności 1,92 TB na wszystkich maszynach wirtualnych Lsv2 jest ulotna. Podczas pomyślnego standardowego ponownego uruchomienia maszyny Wirtualnej dane na lokalnym dysku NVMe będą się powtarzać. Dane nie będą zachowywane na NVMe, jeśli maszyna wirtualna jest ponownie wcielona, de-przydzielone lub usunięte. Dane nie będą się powtarzać, jeśli inny problem powoduje, że maszyna wirtualna lub sprzęt, na którym jest uruchomiona, stają się w złej kondycji. W takim przypadku wszystkie dane na starym hoście są bezpiecznie usuwane.

Będą również przypadki, gdy maszyna wirtualna musi zostać przeniesiona na inny komputer-host, na przykład podczas planowanej operacji konserwacji. Planowane operacje konserwacji i niektóre awarie sprzętu można przewidzieć za pomocą [zaplanowanych zdarzeń](scheduled-events.md). Zaplanowane zdarzenia powinny być używane do aktualizacji na wszelkie przewidywane operacje konserwacji i odzyskiwania.

W przypadku, gdy zdarzenie planowanej konserwacji wymaga maszyny Wirtualnej do odtworzenia na nowym hoście z pustymi dyskami lokalnymi, dane będą musiały zostać ponownie zsynchronizowane (ponownie, z wszystkich danych na starym hoście są bezpiecznie wymazane). Dzieje się tak, ponieważ maszyny wirtualne z serii Lsv2 nie obsługują obecnie migracji na żywo na lokalnym dysku NVMe.

Istnieją dwa tryby planowanej konserwacji.

### <a name="standard-vm-customer-controlled-maintenance"></a>Standardowa konserwacja sterowana przez klienta

- Maszyna wirtualna jest przenoszona do zaktualizowanego hosta podczas okna 30-dniowego.
- Dane magazynu lokalnego Lsv2 mogą zostać utracone, dlatego zaleca się tworzenie kopii zapasowych danych przed zdarzeniem.

### <a name="automatic-maintenance"></a>Automatyczna konserwacja

- Występuje, jeśli klient nie wykonuje konserwacji kontrolowanej przez klienta lub w przypadku procedur awaryjnych, takich jak zdarzenie zero-day zabezpieczeń.
- Przeznaczone do zachowania danych klientów, ale istnieje niewielkie ryzyko zamrożenia lub ponownego uruchomienia maszyny Wirtualnej.
- Dane magazynu lokalnego Lsv2 mogą zostać utracone, dlatego zaleca się tworzenie kopii zapasowych danych przed zdarzeniem.

W przypadku nadchodzących zdarzeń usługi użyj kontrolowanego procesu konserwacji, aby wybrać czas najwygodniejszy dla aktualizacji. Przed zdarzeniem możesz uzyskać zapas zapasów danych w magazynie w wersji premium. Po zakończeniu zdarzenia konserwacji można zwrócić dane do odświeżanej lokalnej pamięci masowej NVMe maszyn wirtualnych Lsv2.

Scenariusze, które przechowują dane na lokalnych dyskach NVMe obejmują:

- Maszyna wirtualna jest uruchomiona i w dobrej kondycji.
- Maszyna wirtualna jest ponownie uruchamiana w miejscu (przez Ciebie lub platformy Azure).
- Maszyna wirtualna jest wstrzymana (zatrzymana bez delokacji).
- Większość planowanych operacji serwisowych.

Scenariusze, które bezpiecznie wymazywać dane w celu ochrony klienta obejmują:

- Maszyna wirtualna jest ponownie wcielona, zatrzymana (de-przydzielona) lub usunięta (przez Ciebie).
- Maszyna wirtualna staje się w złej kondycji i musi obsługiwać leczyć do innego węzła z powodu problemu ze sprzętem.
- Niewielka liczba planowanych operacji obsługi konserwacji, która wymaga ponownego przydzielenia maszyny Wirtualnej do innego hosta w celu obsługi.

Aby dowiedzieć się więcej o opcjach tworzenia kopii zapasowych danych w magazynie lokalnym, zobacz [Tworzenie kopii zapasowych i odzyskiwanie po awarii dysków usługi Azure IaaS](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

* **Jak rozpocząć wdrażanie maszyn wirtualnych z serii Lsv2?**  
   Podobnie jak każda inna maszyna wirtualna, użyj [portalu,](quick-create-portal.md) [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)lub programu [PowerShell,](quick-create-powershell.md) aby utworzyć maszynę wirtualną.

* **Czy pojedynczy błąd dysku NVMe spowoduje, że wszystkie maszyny wirtualne na hoście nie powiodą się?**  
   Jeśli w węźle sprzętowym zostanie wykryta awaria dysku, sprzęt jest w stanie awarii. W takim przypadku wszystkie maszyny wirtualne w węźle są automatycznie de-przydzielone i przeniesione do węzła w dobrej kondycji. W przypadku maszyn wirtualnych z serii Lsv2 oznacza to, że dane klienta w węźle awarii również są bezpiecznie usuwane i muszą zostać odtworzone przez klienta w nowym węźle. Jak wspomniano, zanim migracja na żywo stanie się dostępna na Lsv2, dane w węźle awarii będą aktywnie przenoszone z maszynami wirtualnymi, ponieważ są one przesyłane do innego węzła.

* **Czy muszę wprowadzać zmiany w sondowaniu w systemie Windows w systemie Windows Server 2012 lub Windows Server 2016?**  
   Sondowanie NVMe jest dostępne tylko w systemie Windows Server 2019 na platformie Azure.  

* **Czy mogę wrócić do tradycyjnego modelu procedury przerywania usługi (ISR)?**  
   Maszyny wirtualne serii Lsv2 są zoptymalizowane pod kątem sondowania NVMe. Aktualizacje są stale dostarczane w celu zwiększenia wydajności sondowania.

* **Czy mogę dostosować ustawienia sondowania w systemie Windows Server 2019?**  
   Ustawienia sondowania nie są regulowane przez użytkownika.
   
## <a name="next-steps"></a>Następne kroki

* Zobacz specyfikacje wszystkich [maszyn wirtualnych zoptymalizowanych pod kątem wydajności magazynu](sizes-storage.md) na platformie Azure
