---
title: Testowanie replikacji maszyny wirtualnej funkcji Hyper V do lokacji dodatkowej za pomocą programu VMM przy użyciu usługi Azure Site Recovery
description: Ten artykuł zawiera informacje dotyczące testowania wydajności replikacji maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM do lokacji dodatkowej przy użyciu usługi Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 3edd182e335bc679d95d7be64f45b617a9f54c1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73663174"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Wyniki testów replikacji funkcji Hyper-V do lokacji dodatkowej


Ten artykuł zawiera wyniki testów wydajności podczas replikowania maszyn wirtualnych funkcji Hyper-V w chmurach Menedżera maszyn wirtualnych (VMM) w centrum systemu do pomocniczego centrum danych.

## <a name="test-goals"></a>Cele testowe

Celem testowania było zbadanie, jak program Site Recovery wykonuje podczas replikacji w stanie ustalonym.

- Replikacja w stanie ustalonym występuje, gdy maszyny wirtualne zakończyły replikację początkową i synchronizują zmiany różnicowe.
- Ważne jest, aby zmierzyć wydajność przy użyciu stanu stałego, ponieważ jest to stan, w którym większość maszyn wirtualnych pozostają, chyba że wystąpią nieoczekiwane awarie.
- Wdrożenie testowe składało się z dwóch lokacji lokalnych, z serwerem VMM w każdej lokacji. To wdrożenie testowe jest typowe dla wdrożenia centrali/oddziału, z centralą działającą jako lokacja główna, a oddziałem jako lokacją dodatkową lub lokacją odzyskiwania.

## <a name="what-we-did"></a>Co zrobiliśmy

Oto, co zrobiliśmy w zdaniu testu:

1. Utworzono maszyny wirtualne przy użyciu szablonów programu VMM.
2. Uruchomiono maszyny wirtualne i przechwycono metryki wydajności linii bazowej w ciągu 12 godzin.
3. Utworzone chmury na podstawowych i odzyskiwania serwerów VMM.
4. Skonfigurowano replikację w układzie Site Recovery, w tym mapowanie między chmurami źródłowymi i odzyskiwania.
5. Włączono ochronę maszyn wirtualnych i pozwoliło im zakończyć replikację początkową.
6. Czekał kilka godzin na stabilizację systemu.
7. Przechwycone metryki wydajności w ciągu 12 godzin, gdzie wszystkie maszyny wirtualne pozostały w oczekiwanym stanie replikacji przez te 12 godzin.
8. Zmierzyono różnicę między metrykami wydajności linii bazowej a metrykami wydajności replikacji.


## <a name="primary-server-performance"></a>Wydajność serwera podstawowego

* Replika funkcji Hyper-V (używana przez usługę Site Recovery) asynchronicznie śledzi zmiany w pliku dziennika, przy minimalnym obciążeniu magazynem na serwerze podstawowym.
* Replika funkcji Hyper-V wykorzystuje pamięć podręczną z własnym utrzymaniem, aby zminimalizować obciążenie we/wy dotyczące we/wy na potrzeby śledzenia. Przechowuje zapisy do VHDX w pamięci i opróżnia je do pliku dziennika przed czasem, że dziennik jest wysyłany do witryny odzyskiwania. Opróżnianie dysku dzieje się również wtedy, gdy zapisy trafią na z góry określony limit.
* Poniższy wykres przedstawia stan stały we/wy obciążenie dla replikacji. Widzimy, że obciążenie we/wy z powodu replikacji wynosi około 5%, co jest dość niskie.

  ![Wyniki podstawowe](./media/hyper-v-vmm-performance-results/IC744913.png)

Replika funkcji Hyper-V używa pamięci na serwerze podstawowym w celu optymalizacji wydajności dysku. Jak pokazano na poniższym wykresie, obciążenie pamięci na wszystkich serwerach w klastrze podstawowym jest marginalne. Pokazane obciążenie pamięci jest procentem pamięci używanej przez replikację w porównaniu z całkowitą zainstalowaną pamięcią na serwerze funkcji Hyper-V.

![Wyniki podstawowe](./media/hyper-v-vmm-performance-results/IC744914.png)

Replika funkcji Hyper-V ma minimalne obciążenie procesora. Jak pokazano na wykresie, obciążenie replikacji mieści się w zakresie od 2 do 3%.

![Wyniki podstawowe](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Wydajność serwera pomocniczego

Replika funkcji Hyper-V używa niewielkiej ilości pamięci na serwerze odzyskiwania, aby zoptymalizować liczbę operacji magazynu. Wykres podsumowuje użycie pamięci na serwerze odzyskiwania. Pokazane obciążenie pamięci jest procentem pamięci używanej przez replikację w porównaniu z całkowitą zainstalowaną pamięcią na serwerze funkcji Hyper-V.

![Wyniki wtórne](./media/hyper-v-vmm-performance-results/IC744916.png)

Ilość operacji we/wy w lokacji odzyskiwania jest funkcją liczby operacji zapisu w lokacji głównej. Przyjrzyjmy się całkowitej operacji we/wy w lokacji odzyskiwania w porównaniu z całkowitą operacji we/wy i operacji zapisu w lokacji głównej. Wykresy pokazują, że całkowita liczba we/wy we/wy w miejscu odzyskiwania jest

* Około 1,5 razy zapisu IOPS na podstawowym.
* Około 37% wszystkich we/wy we/wy w lokacji głównej.

![Wyniki wtórne](./media/hyper-v-vmm-performance-results/IC744917.png)

![Wyniki wtórne](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Wpływ na wykorzystanie sieci

Między węzłami podstawowymi i odzyskiwania (z włączoną kompresją) używano średnio 275 Mb na sekundę, przy istniejącej przepustowości 5 Gb na sekundę.

![Wyniki wykorzystania sieci](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Wpływ na wydajność maszyny wirtualnej

Ważną kwestią jest wpływ replikacji na obciążenia produkcyjne uruchomione na maszynach wirtualnych. Jeśli lokacja główna jest odpowiednio aprowizowana do replikacji, nie powinno mieć żadnego wpływu na obciążenia. Lekki mechanizm śledzenia repliki funkcji Hyper-V zapewnia, że podczas replikacji w stanie ustalonym nie ma to wpływu na obciążenia działające na maszynach wirtualnych. Jest to zilustrowane na poniższych wykresach.

Ten wykres przedstawia usługi We/Wy wykonywane przez maszyny wirtualne z różnymi obciążeniami, przed i po włączeniu replikacji. Można zaobserwować, że nie ma różnicy między nimi.

![Wyniki efektu repliki](./media/hyper-v-vmm-performance-results/IC744920.png)

Na poniższym wykresie przedstawiono przepływność maszyn wirtualnych z różnymi obciążeniami, przed i po włączeniu replikacji. Można zaobserwować, że replikacja nie ma znaczącego wpływu.

![Efekty repliki wyników](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Podsumowanie

Wyniki wyraźnie pokazują, że usługa Site Recovery w połączeniu z repliką funkcji Hyper-V jest dobrze skalowana przy minimalnym obciążeniu dla dużego klastra. Usługa Site Recovery zapewnia proste wdrażanie, replikację, zarządzanie i monitorowanie. Replika funkcji Hyper-V zapewnia niezbędną infrastrukturę do pomyślnego skalowania replikacji. 

## <a name="test-environment-details"></a>Szczegóły środowiska testowego

### <a name="primary-site"></a>Lokacja główna

* Lokacja główna ma klaster zawierający pięć serwerów funkcji Hyper-V z 470 maszynami wirtualnymi.
* Maszyny wirtualne uruchamiają różne obciążenia i wszystkie mają włączoną ochronę odzyskiwania witryny.
* Magazyn dla węzła klastra jest dostarczany przez san iSCSI. Model – Hitachi HUS130.
* Każdy serwer klastra ma cztery karty sieciowe (NIC) po jednym Gb/s.
* Dwie karty sieciowe są podłączone do sieci prywatnej iSCSI, a dwie są połączone z zewnętrzną siecią przedsiębiorstwa. Jedna z sieci zewnętrznych jest zarezerwowana tylko dla komunikacji klastra.

![Podstawowe wymagania sprzętowe](./media/hyper-v-vmm-performance-results/IC744922.png)

| Serwer | Pamięć RAM | Model | Procesor | Liczba procesorów | Karta sieciowa | Oprogramowanie |
| --- | --- | --- | --- | --- | --- | --- |
| Serwery funkcji Hyper-V w klastrze: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 ma 256 |Dell ™ PowerEdge ™ R820 |Procesor Intel(R) Xeon(R) E5-4620 0 \@ 2,20 GHz |4 |I Gb/s x 4 |Windows Server Datacenter 2012 R2 (x64) + funkcja funkcji Hyper-V |
| Serwer VMM |2 | | |2 |1 Gb/s |Baza danych systemu Windows Server 2012 R2 (x64) + program VMM 2012 R2 |

### <a name="secondary-site"></a>Lokacja dodatkowa

* Lokacja pomocnicza ma sześciowęzłowy klaster trybu failover.
* Magazyn dla węzła klastra jest dostarczany przez san iSCSI. Model – Hitachi HUS130.

![Podstawowa specyfikacja sprzętu](./media/hyper-v-vmm-performance-results/IC744923.png)

| Serwer | Pamięć RAM | Model | Procesor | Liczba procesorów | Karta sieciowa | Oprogramowanie |
| --- | --- | --- | --- | --- | --- | --- |
| Serwery funkcji Hyper-V w klastrze: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Zasilacze ™ firmy Dell ™ R720 |Procesor Intel(R) Xeon(R) E5-2630 0 \@ 2,30 GHz |2 |I Gb/s x 4 |Windows Server Datacenter 2012 R2 (x64) + funkcja funkcji Hyper-V |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Procesor Intel(R) Xeon(R) E5-4620 0 \@ 2,20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + funkcja funkcji Hyper-V |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Procesor Intel(R) Xeon(R) E5-4620 0 \@ 2,20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + funkcja funkcji Hyper-V |
| Serwer VMM |2 | | |2 |1 Gb/s |Baza danych systemu Windows Server 2012 R2 (x64) + program VMM 2012 R2 |

### <a name="server-workloads"></a>Obciążenia serwerów

* Do celów testowych wybraliśmy obciążenia powszechnie używane w scenariuszach dla klientów korporacyjnych.
* Używamy [IOMeter](http://www.iometer.org) z charakterystyką obciążenia podsumowane w tabeli do symulacji.
* Wszystkie profile IOMeter są ustawione na pisanie losowych bajtów, aby symulować wzorce zapisu najgorszego przypadku dla obciążeń.

| Obciążenie | Rozmiar we/wy (KB) | % dostępu | %Odczyt | Wybitne we/wy | Wzór we/wy |
| --- | --- | --- | --- | --- | --- |
| Serwer plików |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |Wszystkie 100% losowe |
| SQL Server (tom 1)<br />SQL Server (tom 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% losowo<br />100% sekwencyjne |
| Exchange |32 |100% |67% |8 |100% losowo |
| Stacja robocza/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Zarówno w 100% losowo |
| Serwer plików sieci Web |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Wszystkie 75% losowych |

### <a name="vm-configuration"></a>Konfiguracja maszyny Wirtualnej

* 470 maszyn wirtualnych w klastrze podstawowym.
* Wszystkie maszyny wirtualne z dyskiem VHDX.
* Maszyny wirtualne z uruchomionymi obciążeniami podsumowane w tabeli. Wszystkie zostały utworzone za pomocą szablonów programu VMM.

| Obciążenie | # Maszyny wirtualne | Minimalna ilość pamięci RAM (GB) | Maksymalna ilość pamięci RAM (GB) | Rozmiar dysku logicznego (GB) na maszynę wirtualną | Maksymalna liczba we/wy |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Serwer plików |50 |1 |2 |552 |22 |
| Vdi |149 |.5 |1 |80 |6 |
| Serwer sieci Web |149 |.5 |1 |80 |6 |
| SUMA |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Ustawienia odzyskiwania witryny

* Odzyskiwanie lokacji zostało skonfigurowane dla lokalnej ochrony lokalnej
* Serwer programu VMM ma skonfigurowane cztery chmury zawierające serwery klastra funkcji Hyper-V i ich maszyny wirtualne.

| Podstawowa chmura programu VMM | Chronione maszyny wirtualne | Częstotliwość replikacji | Dodatkowe punkty odzyskiwania |
| --- | --- | --- | --- |
| Podstawowa ChmuraRpo15m |142 |15 min. |Brak |
| PrimaryCloudRpo30s |47 |30 sek. |Brak |
| Usługa PrimaryCloudRpo30sArp1 |47 |30 sek. |1 |
| Podstawowa ChmuraRpo5m |235 |5 min |Brak |

### <a name="performance-metrics"></a>Metryki wydajności

W tabeli podsumowano metryki wydajności i liczniki, które zostały zmierzone we wdrożeniu.

| Metryka | Licznik |
| --- | --- |
| Procesor CPU |\Processor(_Total)\% Processor Time |
| Dostępna pamięć |\Pamięć\Dostępne bajty MBytes |
| Liczba operacji we/wy na sekundę |\PhysicalDisk(_Total)\Transfery dysków/s |
| Operacje odczytu maszyny Wirtualnej (IOPS) na sekundę |\Wirtualne urządzenie pamięci\<masowej Hyper-V(> VHD)\Operacje odczytu/s |
| Operacje zapisu maszyny Wirtualnej (IOPS) na sekundę |\Wirtualne urządzenie pamięci\<masowej Hyper-V(> VHD)\Operacje zapisu/S |
| Przepływność odczytu maszyny Wirtualnej |\Wirtualne urządzenie pamięci\<masowej Hyper-V(> VHD)\Odczyt bajtów/s |
| Przepływność zapisu maszyny Wirtualnej |\Wirtualne urządzenie pamięci\<masowej Hyper-V(> VHD)\Write Bytes/s |

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie replikacji](hyper-v-vmm-disaster-recovery.md)
