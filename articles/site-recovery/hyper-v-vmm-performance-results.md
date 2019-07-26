---
title: Wyniki testu dotyczące replikacji maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM do lokacji dodatkowej z Azure Site Recovery | Microsoft Docs
description: Ten artykuł zawiera informacje o testowaniu wydajności replikacji maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM do lokacji dodatkowej przy użyciu Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: a7413b2dcb24a42092eb2af9816b1d29a8306e19
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377212"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Wyniki testu dla replikacji funkcji Hyper-V do lokacji dodatkowej


Ten artykuł zawiera wyniki testów wydajności podczas replikowania maszyn wirtualnych funkcji Hyper-V w chmurach System Center Virtual Machine Manager (VMM) do dodatkowego centrum danych.

## <a name="test-goals"></a>Cele testu

Celem testowania było zbadanie, jak Site Recovery wykonuje podczas replikacji o stałej kondycji.

- Replikacja o stałym stanie występuje, gdy maszyny wirtualne ukończyją replikację początkową i synchronizują zmiany różnicowe.
- Ważne jest, aby mierzyć wydajność przy użyciu stałego stanu, ponieważ jest to stan, w którym są pozostawi większość maszyn wirtualnych, chyba że wystąpi nieoczekiwany czas przestoju.
- Wdrożenie testowe obejmuje dwie lokacje lokalne z serwerem programu VMM w każdej lokacji. To wdrożenie testowe jest typowym wdrożeniem biura/oddziału, z siedzibą firmy jako lokacją główną, a biurem oddziału jako lokacji dodatkowej lub odzyskania.

## <a name="what-we-did"></a>Co się stało

Oto co się dzieje w przebiegu testu:

1. Tworzenie maszyn wirtualnych przy użyciu szablonów programu VMM.
2. Uruchomiono maszyny wirtualne i przechwycone metryki wydajności przez 12 godzin.
3. Utworzono chmury na podstawowym i odzyskiwanym serwerze programu VMM.
4. Skonfigurowana replikacja w Site Recovery, łącznie z mapowaniem między chmurami źródłowymi i odzyskiwania.
5. Włączono ochronę dla maszyn wirtualnych i zezwalają na ukończenie replikacji początkowej.
6. Zaczekaj kilka godzin dla stabilizacji systemu.
7. Przechwycone metryki wydajności przez 12 godzin, gdzie wszystkie maszyny wirtualne pozostawały w oczekiwanym stanie replikacji przez 12 godzin.
8. Pomiar różnicy między podstawowymi metrykami wydajności i metrykami wydajności replikacji.


## <a name="primary-server-performance"></a>Podstawowa wydajność serwera

* Funkcja Hyper-V Replica (używana przez Site Recovery) asynchronicznie śledzi zmiany w pliku dziennika przy minimalnym obciążeniu magazynu na serwerze podstawowym.
* Funkcja Hyper-V Replica wykorzystuje nieobsługiwaną pamięć podręczną pamięci, aby zminimalizować obciążenie operacji we/wy na potrzeby śledzenia. Zapisuje zapisy w dysku VHDX w pamięci i opróżnia je do pliku dziennika przed upływem czasu wysłania dziennika do lokacji odzyskiwania. Opróżnianie dysku następuje również wtedy, gdy w zapisie osiągnięto wstępnie określony limit.
* Wykres poniżej przedstawia obciążenie operacji wejścia/wyjścia o stałym stanie dla replikacji. Możemy zobaczyć, że obciążenie IOPS spowodowane replikacją wynosi około 5%, co jest dość małe.

  ![Podstawowe wyniki](./media/hyper-v-vmm-performance-results/IC744913.png)

Funkcja Hyper-V Replica używa pamięci na serwerze podstawowym w celu zoptymalizowania wydajności dysków. Jak pokazano na poniższym wykresie, obciążenie pamięci na wszystkich serwerach w klastrze podstawowym jest marginalne. Pokazanym obciążeniem pamięci jest procent pamięci używanej przez replikację w porównaniu do całkowitej zainstalowanej pamięci na serwerze funkcji Hyper-V.

![Podstawowe wyniki](./media/hyper-v-vmm-performance-results/IC744914.png)

Funkcja Hyper-V Replica ma minimalne obciążenie procesora CPU. Jak pokazano na wykresie, obciążenie związane z replikacją znajduje się w zakresie 2-3%.

![Podstawowe wyniki](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Wydajność serwera pomocniczego

Funkcja Hyper-V Replica korzysta z niewielkiej ilości pamięci na serwerze odzyskiwania, aby zoptymalizować liczbę operacji magazynowania. Wykres podsumowuje użycie pamięci na serwerze odzyskiwania. Pokazanym obciążeniem pamięci jest procent pamięci używanej przez replikację w porównaniu do całkowitej zainstalowanej pamięci na serwerze funkcji Hyper-V.

![Wyniki pomocnicze](./media/hyper-v-vmm-performance-results/IC744916.png)

Liczba operacji we/wy w lokacji odzyskiwania jest funkcją liczby operacji zapisu w lokacji głównej. Przyjrzyjmy się łącznym operacjom we/wy w lokacji odzyskiwania w porównaniu z całkowitymi operacjami we/wy i operacjami zapisu w lokacji głównej. Wykresy pokazują, że łączna liczba operacji we/wy w lokacji odzyskiwania jest

* Około 1,5 razy liczba operacji we/wy zapisu na serwerze podstawowym.
* Około 37% całkowitej liczby operacji we/wy w lokacji głównej.

![Wyniki pomocnicze](./media/hyper-v-vmm-performance-results/IC744917.png)

![Wyniki pomocnicze](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Wpływ na wykorzystanie sieci

Wykorzystano średnią z 275 MB na sekundę przepustowości sieci między węzłami głównymi i odzyskiwania (z włączoną kompresją), korzystając z istniejącej przepustowości wynoszącej 5 GB na sekundę.

![Wyniki wykorzystania sieci](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Wpływ na wydajność maszyny wirtualnej

Ważnym zagadnieniem jest wpływ replikacji w obciążeniach produkcyjnych uruchomionych na maszynach wirtualnych. Jeśli lokacja główna jest odpowiednio obsługiwana na potrzeby replikacji, nie ma żadnego wpływu na obciążenia. Mechanizm uproszczonego śledzenia funkcji Hyper-V Replica zapewnia, że obciążenia działające na maszynach wirtualnych nie będą miały wpływu na replikację z ustalonym stanem. Przedstawiono to na poniższych wykresach.

Ten wykres pokazuje liczbę operacji we/wy wykonywanych przez maszyny wirtualne z różnymi obciążeniami, przed i po włączeniu replikacji. Można zauważyć, że nie ma różnicy między tymi dwoma.

![Wyniki działania repliki](./media/hyper-v-vmm-performance-results/IC744920.png)

Na poniższym wykresie przedstawiono przepływność maszyn wirtualnych z różnymi obciążeniami, przed i po włączeniu replikacji. Można obserwować, że replikacja nie ma znaczącego wpływu.

![Efekty replik wyników](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Wniosek

Wyniki jasno pokazują, że Site Recovery, w połączeniu z funkcją Hyper-V Replica, skaluje się dobrze przy minimalnym obciążeniu dla dużego klastra. Site Recovery zapewnia proste wdrażanie, replikację, zarządzanie i monitorowanie. Funkcja Hyper-V Replica zapewnia infrastrukturę niezbędną do pomyślnego skalowania replikacji. 

## <a name="test-environment-details"></a>Szczegóły środowiska testowego

### <a name="primary-site"></a>Lokacja główna

* Lokacja główna ma klaster zawierający pięć serwerów funkcji Hyper-V, na których działają maszyny wirtualne 470.
* Maszyny wirtualne uruchamiają różne obciążenia, a wszystkie Site Recovery ochrony są włączone.
* Magazyn dla węzła klastra jest udostępniany przez sieć SAN iSCSI. Model – Hitachi HUS130.
* Każdy serwer klastra ma cztery karty sieciowe (nic) o jednej GB/s każdy.
* Dwie karty sieciowe są połączone z siecią prywatną iSCSI, a dwie są połączone z zewnętrzną siecią przedsiębiorstwa. Jedna z sieci zewnętrznych jest zarezerwowana tylko do komunikacji z klastrem.

![Podstawowe wymagania sprzętowe](./media/hyper-v-vmm-performance-results/IC744922.png)

| Serwer | Pamięć RAM | Modelowanie | Procesor | Liczba procesorów | NIC | Oprogramowanie |
| --- | --- | --- | --- | --- | --- | --- |
| Serwery funkcji Hyper-V w klastrze: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB – HOST25 ma 256 |Dell ™ PowerEdge ™ R820 |Procesor Intel (r) Xeon (R) E5-4620 0 \@ 2.20 GHz |4 |I GB/s x 4 |Windows Server Datacenter 2012 R2 (x64) + rola funkcji Hyper-V |
| Serwer programu VMM |2 | | |2 |1 Gb/s |Baza danych systemu Windows Server 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Lokacja dodatkowa

* Lokacja dodatkowa zawiera klaster trybu failover z sześcioma węzłami.
* Magazyn dla węzła klastra jest udostępniany przez sieć SAN iSCSI. Model – Hitachi HUS130.

![Podstawowa specyfikacja sprzętu](./media/hyper-v-vmm-performance-results/IC744923.png)

| Serwer | Pamięć RAM | Modelowanie | Procesor | Liczba procesorów | NIC | Oprogramowanie |
| --- | --- | --- | --- | --- | --- | --- |
| Serwery funkcji Hyper-V w klastrze: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell™ PowerEdge™ R720 |Procesor Intel (r) Xeon (R) E5-2630 0 \@ 2.30 GHz |2 |I GB/s x 4 |Windows Server Datacenter 2012 R2 (x64) + rola funkcji Hyper-V |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Procesor Intel (r) Xeon (R) E5-4620 0 \@ 2.20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + rola funkcji Hyper-V |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Procesor Intel (r) Xeon (R) E5-4620 0 \@ 2.20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + rola funkcji Hyper-V |
| Serwer programu VMM |2 | | |2 |1 Gb/s |Baza danych systemu Windows Server 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Obciążenia serwera

* Dla celów testowych wybieramy obciążenia często używane w scenariuszach klientów korporacyjnych.
* Używamy [IOMeter](http://www.iometer.org) z charakterystyką obciążenia podsumowaną w tabeli na potrzeby symulacji.
* Wszystkie profile IOMeter są ustawione do pisania losowych bajtów w celu symulowania wzorców zapisu dla najgorszego przypadku dla obciążeń.

| Obciążenie | Rozmiar we/wy (KB) | % Dostępu | % Odczytu | Oczekujące operacje I/OS | Wzorzec we/wy |
| --- | --- | --- | --- | --- | --- |
| Serwer plików |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |Wszystkie losowe 100% |
| SQL Server (Tom 1)<br />SQL Server (wolumin 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% losowo<br />100% sekwencyjne |
| Exchange |32 |100% |67% |8 |100% losowo |
| Stacja robocza/infrastruktura VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Co 100% losowo |
| Serwer plików sieci Web |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Wszystkie losowe 75% |

### <a name="vm-configuration"></a>Konfiguracja maszyny wirtualnej

* 470 maszyn wirtualnych w klastrze podstawowym.
* Wszystkie maszyny wirtualne z dyskiem VHDX.
* Maszyny wirtualne z uruchomionymi obciążeniami zostały podsumowane w tabeli. Wszystkie zostały utworzone przy użyciu szablonów programu VMM.

| Obciążenie | Liczba maszyn wirtualnych | Minimalna pamięć RAM (GB) | Maksymalna ilość pamięci RAM (GB) | Rozmiar dysku logicznego (GB) na maszynę wirtualną | Maksymalna liczba IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Serwer plików |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Serwer sieci Web |149 |.5 |1 |80 |6 |
| SUMA |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Ustawienia Site Recovery

* Site Recovery została skonfigurowana na potrzeby ochrony lokalnej w lokalnej usłudze
* Serwer programu VMM ma skonfigurowane cztery chmury zawierające serwery klastra funkcji Hyper-V i ich maszyny wirtualne.

| Podstawowa chmura programu VMM | Chronione maszyny wirtualne | Częstotliwość replikacji | Dodatkowe punkty odzyskiwania |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minut |Brak |
| PrimaryCloudRpo30s |47 |30 s |Brak |
| PrimaryCloudRpo30sArp1 |47 |30 s |1 |
| PrimaryCloudRpo5m |235 |5 min |Brak |

### <a name="performance-metrics"></a>Metryki wydajności

Tabela zawiera podsumowanie metryk wydajności i liczników, które były mierzone we wdrożeniu.

| Metryka | Licznik |
| --- | --- |
| Procesor CPU |\Processor(_Total)\% Processor Time |
| Available memory |\Memory\Available MBytes |
| IOPS |\PhysicalDisk(_Total)\Disk Transfers/sec |
| VM read (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Read Operations/Sec |
| VM write (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Write Operations/S |
| VM read throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Read Bytes/sec |
| VM write throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Write Bytes/sec |

## Next steps

[Set up replication](hyper-v-vmm-disaster-recovery.md)\Processor(_Total)\` Processor Timeation of Hyper-V VMs in VMM clouds to a secondary site with Azure Site Recovery | Microsoft Docs
description: This article provides information about performance testing for replication of Hyper-V VMs in VMM clouds to a secondary site using Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi

---
# Test results for Hyper-V replication to a secondary site


This article provides the results of performance testing when replicating Hyper-V VMs in System Center Virtual Machine Manager (VMM) clouds, to a secondary datacenter.

## Test goals

The goal of testing was to examine how Site Recovery performs during steady state replication.

- Steady state replication occurs when VMs have completed initial replication, and are synchronizing delta changes.
- It’s important to measure performance using steady state, because it’s the state in which most VMs remain, unless unexpected outages occur.
- The test deployment consisted of two on-premises sites, with a VMM server in each site. This test deployment is typical of a head office/branch office deployment, with head office acting as the primary site, and the branch office as the secondary or recovery site.

## What we did

Here's what we did in the test pass:

1. Created VMs using VMM templates.
2. Started VMs, and captured baseline performance metrics over 12 hours.
3. Created clouds on the primary and recovery VMM servers.
4. Configured replication in Site Recovery, including mapping between source and recovery clouds.
5. Enabled protection for VMs, and allowed them to complete initial replication.
6. Waited a couple of hours for system stabilization.
7. Captured performance metrics over 12 hours, where all VMs remained in an expected replication state for those 12 hours.
8. Measured the delta between the baseline performance metrics, and the replication performance metrics.


## Primary server performance

* Hyper-V Replica (used by Site Recovery) asynchronously tracks changes to a log file, with minimum storage overhead on the primary server.
* Hyper-V Replica utilizes self-maintained memory cache to minimize IOPS overhead for tracking. It stores writes to the VHDX in memory, and flushes them into the log file before the time that the log is sent to the recovery site. A disk flush also happens if the writes hit a predetermined limit.
* The graph below shows the steady state IOPS overhead for replication. We can see that the IOPS overhead due to replication is around 5%, which is quite low.

  ![Primary results](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V Replica uses memory on the primary server, to optimize disk performance. As shown in the following graph, memory overhead on all servers in the primary cluster is marginal. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Primary results](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V Replica has minimum CPU overhead. As shown in the graph, replication overhead is in the range of 2-3%.

![Primary results](./media/hyper-v-vmm-performance-results/IC744915.png)

## Secondary server performance

Hyper-V Replica uses a small amount of memory on the recovery server, to optimize the number of storage operations. The graph summarizes the memory usage on the recovery server. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744916.png)

The amount of I/O operations on the recovery site is a function of the number of write operations on the primary site. Let’s look at the total I/O operations on the recovery site in comparison with the total I/O operations and write operations on the primary site. The graphs show that the total IOPS on the recovery site is

* Around 1.5 times the write IOPS on the primary.
* Around 37% of the total IOPS on the primary site.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744917.png)

![Secondary results](./media/hyper-v-vmm-performance-results/IC744918.png)

## Effect on network utilization

An average of 275 Mb per second of network bandwidth was used between the primary and recovery nodes (with compression enabled), against an existing bandwidth of 5 Gb per second.

![Results network utilization](./media/hyper-v-vmm-performance-results/IC744919.png)

## Effect on VM performance

An important consideration is the impact of replication on production workloads running on the virtual machines. If the primary site is adequately provisioned for replication, there shouldn’t be any impact on the workloads. Hyper-V Replica’s lightweight tracking mechanism ensures that workloads running in the virtual machines are not impacted during steady-state replication. This is illustrated in the following graphs.

This graph shows IOPS performed by virtual machines running different workloads, before and after replication was enabled. You can observe that there is no difference between the two.

![Replica effect results](./media/hyper-v-vmm-performance-results/IC744920.png)

The following graph shows the throughput of virtual machines running different workloads, before and after replication was enabled. You can observe that replication has no significant impact.

![Results replica effects](./media/hyper-v-vmm-performance-results/IC744921.png)

## Conclusion

The results clearly show that Site Recovery, coupled with Hyper-V Replica, scales well with minimum overhead for a large cluster. Site Recovery provides simple deployment, replication, management and monitoring. Hyper-V Replica provides the necessary infrastructure for successful replication scaling. 

## Test environment details

### Primary site

* The primary site has a cluster containing five Hyper-V servers, running 470 virtual machines.
* The VMs run different workloads, and all have Site Recovery protection enabled.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.
* Each cluster server has four network cards (NICs) of one Gbps each.
* Two of the network cards are connected to an iSCSI private network, and two are connected to an external enterprise network. One of the external networks is reserved for cluster communications only.

![Primary hardware requirements](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 has 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Secondary site

* The secondary site has a six-node failover cluster.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.

![Primary hardware specification](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Server workloads

* For test purposes we picked workloads commonly used in enterprise customer scenarios.
* We use [IOMeter](http://www.iometer.org) with the workload characteristic summarized in the table for simulation.
* All IOMeter profiles are set to write random bytes to simulate worst-case write patterns for workloads.

| Workload | I/O size (KB) | % Access | %Read | Outstanding I/Os | I/O pattern |
| --- | --- | --- | --- | --- | --- |
| File Server |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |All 100% random |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% random<br />100% sequential |
| Exchange |32 |100% |67% |8 |100% random |
| Workstation/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Both 100% random |
| Web File Server |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |All 75% random |

### VM configuration

* 470 VMs on the primary cluster.
* All VMs with VHDX disk.
* VMs running workloads summarized in the table. All were created with VMM templates.

| Workload | # VMs | Minimum RAM (GB) | Maximum RAM (GB) | Logical disk size (GB) per VM | Maximum IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| File Server |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Web server |149 |.5 |1 |80 |6 |
| TOTAL |470 | | |96.83 TB |4108 |

### Site Recovery settings

* Site Recovery was configured for on-premises to on-premises protection
* The VMM server has four clouds configured, containing the Hyper-V cluster servers and their VMs.

| Primary VMM cloud | Protected VMs | Replication frequency | Additional recovery points |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 mins |None |
| PrimaryCloudRpo30s |47 |30 secs |None |
| PrimaryCloudRpo30sArp1 |47 |30 secs |1 |
| PrimaryCloudRpo5m |235 |5 mins |None |

### Performance metrics

The table summarizes the performance metrics and counters that were measured in the deployment.

| Metric | Counter |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Dostępna pamięć |\Memory\Available (MB) |
| IOPS |\PhysicalDisk (_Total) \Bajty transferów/s |
| Operacje odczytu maszyny wirtualnej (IOPS)/s |\Hyper-V wirtualne urządzenie magazynujące (\<VHD >) \Read operacje/s |
| Operacje zapisu maszyny wirtualnej (IOPS)/s |\Hyper-V wirtualne urządzenie magazynujące (\<VHD >) \Write operacje/S |
| Przepływność odczytu maszyny wirtualnej |\Hyper-V wirtualne urządzenie magazynujące (\<VHD >) \Read bajty/s |
| Przepływność zapisu maszyny wirtualnej |\Hyper-V wirtualne urządzenie magazynujące (\<VHD >) \Write bajty/s |

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie replikacji](hyper-v-vmm-disaster-recovery.md)
