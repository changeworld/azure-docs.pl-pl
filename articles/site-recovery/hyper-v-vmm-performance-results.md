---
title: Wyniki testu dla replikacji maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM do lokacji dodatkowej za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące testowania wydajnościowego na potrzeby replikacji maszyn wirtualnych z funkcją Hyper-V w chmurach programu VMM do lokacji dodatkowej przy użyciu usługi Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 7e2f5c344a0fb632956ab5d5b951ee69cff528ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60363551"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Wyniki testu dla replikacji funkcji Hyper-V do lokacji dodatkowej


Ten artykuł zawiera wyniki testu podczas replikowania maszyn wirtualnych z funkcją Hyper-V w chmurach programu System Center Virtual Machine Manager (VMM) do dodatkowego centrum danych wydajności.

## <a name="test-goals"></a>Cele testu

Celem testowanie było sprawdzić, jak Usługa Site Recovery podczas replikacji w stanie stabilności.

- Replikacji w stanie stabilnym występuje, gdy została ukończona replikacji początkowej maszyn wirtualnych i planowana jest synchronizacja zmian różnicowych.
- Ważne jest mierzenie wydajności przy użyciu stanie stabilności, ponieważ jest on stan, w której większość maszyn wirtualnych pozostają, chyba że wystąpienia nieoczekiwanych awarii.
- Testowe wdrożenie składa się z dwóch lokacji lokalnych z serwera programu VMM w taki sposób, w każdej lokacji. Tego wdrożenia testowego jest typowym przykładem w przypadku wdrożenia w biurach/oddziałach głównego z siedziby, działając jako lokację główną i oddziału jako witryny pomocniczej lub odzyskiwania.

## <a name="what-we-did"></a>Co zrobiliśmy

Oto, co mamy w teście przeszedł:

1. Maszyny wirtualne utworzone przy użyciu szablonów w programie VMM.
2. Uruchomione maszyny wirtualne, a przechwycone bazowych metryk wydajności ponad 12 godzin.
3. Chmury utworzone na serwerze podstawowym i odzyskiwania serwerów programu VMM.
4. Replikacji w usłudze Site Recovery, w tym mapowania między źródłem i odzyskiwania chmur.
5. Włączono ochronę dla maszyn wirtualnych i mogą je do ukończenia replikacji początkowej.
6. Oczekiwano kilka godzin stabilizacji systemu.
7. Przechwytywane metryki wydajności ponad 12 godzin, gdzie wszystkie maszyny wirtualne pozostaje nieoczekiwany stan replikacji dla tych 12 godzin.
8. Mierzone różnica między bazowych metryk wydajności i metryki wydajności replikacji.


## <a name="primary-server-performance"></a>Wydajność serwera podstawowego

* Funkcji Hyper-V Replica (używane przez usługę Site Recovery) asynchronicznie śledzi zmiany w pliku dziennika z magazynem minimalne obciążenie na serwerze podstawowym.
* Repliki Hyper-V korzysta z własnym utrzymywane w dobrym stanie pamięci podręcznej, aby zminimalizować operacje We/Wy obciążenia do śledzenia. Przechowuje zapisuje VHDX w pamięci i opróżnia je do pliku dziennika, przed upływem terminu, który dziennika są wysyłane do lokacji odzyskiwania. Dysk opróżniania występuje także w zapisy osiągnięciu limitu wcześniej.
* Wykres poniżej przedstawia obciążenie stanie stabilności operacji We/Wy do replikacji. Widać, że operacje We/Wy obciążenie z powodu replikacji jest około 5%, która jest mało prawdopodobne.

  ![Wyniki podstawowego](./media/hyper-v-vmm-performance-results/IC744913.png)

Repliki Hyper-V korzysta z pamięci na serwerze podstawowym, aby zoptymalizować wydajność dysków. Jak pokazano na poniższym wykresie, pamięć, obciążenie na wszystkich serwerach w klastrze podstawowy jest brzegowych. Pamięć, obciążenie wyświetlany jest procent pamięci używana przez funkcję replikacji, w porównaniu do całkowitej pamięci zainstalowanych na serwerze funkcji Hyper-V.

![Wyniki podstawowego](./media/hyper-v-vmm-performance-results/IC744914.png)

Funkcja Hyper-V Replica ma minimalne obciążenie procesora CPU. Jak pokazano na wykresie, obciążenia związanego z replikacją jest z zakresu od % 2 – 3.

![Wyniki podstawowego](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Wydajność serwera pomocniczego

Repliki Hyper-V korzysta z małej ilości pamięci na serwerze odzyskiwania zoptymalizować liczbę operacji magazynu. Wykres zawiera podsumowanie wykorzystania pamięci na serwerze odzyskiwania. Pamięć, obciążenie wyświetlany jest procent pamięci używana przez funkcję replikacji, w porównaniu do całkowitej pamięci zainstalowanych na serwerze funkcji Hyper-V.

![Wyniki dodatkowej](./media/hyper-v-vmm-performance-results/IC744916.png)

Liczba operacji We/Wy do lokalizacji odzyskiwania jest funkcją, liczby operacji zapisu w lokacji głównej. Możemy przyjrzeć się łączna liczba operacji We/Wy do lokalizacji odzyskiwania w porównaniu z łączna liczba operacji We/Wy operacji zapisu i lokacji głównej. Wykresy pokazują, że łączna liczba operacji We/Wy do lokalizacji odzyskiwania jest

* Około 1,5 raza więcej niż zapisu operacji We/Wy na serwerze podstawowym.
* Około 37% łączna liczba operacji We/Wy w lokacji głównej.

![Wyniki dodatkowej](./media/hyper-v-vmm-performance-results/IC744917.png)

![Wyniki dodatkowej](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Wpływ na użycie sieci

Średnio 275 Mb na sekundę przepustowość sieci zostało użyte między węzłami podstawowymi i odzyskiwania (z włączoną kompresją), przed istniejących przepustowości 5 Gb na sekundę.

![Wykorzystanie sieci wyniki](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Wpływ na wydajność maszyny Wirtualnej

Ważną kwestią jest wpływ replikacji dla obciążeń produkcyjnych uruchamianych na maszynach wirtualnych. Jeśli lokacja główna jest odpowiednio do replikacji, istnieje nie powinna mieć żadnego wpływu na obciążenia. Uproszczone funkcji Hyper-V Replica mechanizm śledzenia zapewnia obciążeń działających na maszynach wirtualnych nie są zagrożone podczas replikacji w stanie stabilnym. Jest to zilustrowane w poniższym wykresów.

Ten wykres pokazuje, że operacje we/wy wykonywane przez maszyny wirtualne uruchomione na różnych obciążeń, przed i po włączeniu replikacji. Można zaobserwować, że nie ma żadnej różnicy między nimi.

![Wyniki efekt repliki](./media/hyper-v-vmm-performance-results/IC744920.png)

Poniższy wykres pokazuje przepływność maszyn wirtualnych działających na różnych obciążeń, przed i po włączeniu replikacji. Można zaobserwować, że replikacja została znaczącego wpływu.

![Efekty repliki wyników](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Podsumowanie

Wyniki wyraźnego wyświetlania informacji dotyczących odzyskiwania lokacji, w połączeniu z funkcją Hyper-V Replica, skaluje się z co najmniej w czasie dużego klastra. Usługa Site Recovery oferuje proste wdrożenie, replikacja, zarządzanie i monitorowanie. Repliki Hyper-V udostępnia infrastrukturę niezbędne do pomyślnego zakończenia replikacji skalowania. 

## <a name="test-environment-details"></a>Szczegóły środowiska testowego

### <a name="primary-site"></a>Lokacja główna

* Lokacja główna ma klastrze zawierającym pięć serwerów funkcji Hyper-V, działających 470 maszyn wirtualnych.
* Maszyny wirtualne nie działały różnych obciążeń, a wszystkie mają włączoną ochronę Site Recovery.
* Magazyn dla węzła klastra jest zapewniana przez sieci SAN iSCSI. Model – Hitachi HUS130.
* Każdy serwer z klastra ma cztery karty sieciowe (NIC) w jedną GB/s każdego.
* Dwie karty sieciowe są podłączone do sieci prywatnej iSCSI, a dwa są podłączone do sieci zewnętrznej przedsiębiorstwa. Jedną z sieci zewnętrznej jest zarezerwowana dla tylko komunikacji klastra.

![Podstawowe wymagania sprzętowe](./media/hyper-v-vmm-performance-results/IC744922.png)

| Serwer | Pamięć RAM | Modelowanie | Procesor | Liczba procesorów | NIC | Oprogramowanie |
| --- | --- | --- | --- | --- | --- | --- |
| Serwery funkcji Hyper-V w klastrze: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128ESTLAB HOST25 ma 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) E5 procesora CPU — 4620 0 \@ 2,20 GHz |4 |Czy mogę GB/s x 4 |Windows Server Datacenter 2012 R2 (x64) i roli Hyper-V |
| Serwer programu VMM |2 | | |2 |1 Gb/s |System Windows Server Database 2012 R2 (x 64) i program VMM 2012 R2 |

### <a name="secondary-site"></a>Lokacja dodatkowa

* Lokacja dodatkowa ma klastra pracy awaryjnej sześciu węzłów.
* Magazyn dla węzła klastra jest zapewniana przez sieci SAN iSCSI. Model – Hitachi HUS130.

![Specyfikacja podstawowego sprzętu](./media/hyper-v-vmm-performance-results/IC744923.png)

| Serwer | Pamięć RAM | Modelowanie | Procesor | Liczba procesorów | NIC | Oprogramowanie |
| --- | --- | --- | --- | --- | --- | --- |
| Serwery funkcji Hyper-V w klastrze: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) E5 procesora CPU — 2630 0 \@ 2.30 GHz |2 |Czy mogę GB/s x 4 |Windows Server Datacenter 2012 R2 (x64) i roli Hyper-V |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) E5 procesora CPU — 4620 0 \@ 2,20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) i roli Hyper-V |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) E5 procesora CPU — 4620 0 \@ 2,20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) i roli Hyper-V |
| Serwer programu VMM |2 | | |2 |1 Gb/s |System Windows Server Database 2012 R2 (x 64) i program VMM 2012 R2 |

### <a name="server-workloads"></a>Obciążenie serwera

* Do celów testowych możemy pobrać obciążeń często używane w scenariuszach klientów w przedsiębiorstwie.
* Używamy [IOMeter](http://www.iometer.org) z cechy obciążenia podsumowane w tabeli symulacji.
* Można ustawić wszystkie profile IOMeter zapisu wzorców obciążeń operacji zapisu w losowych bajtów do symulowania najgorszego przypadku.

| Obciążenie | Rozmiar operacji We/Wy (KB) | % Dostępu | % Odczytu | Liczba oczekujących operacji We/Wy | Wzorzec operacji We/Wy |
| --- | --- | --- | --- | --- | --- |
| Serwer plików |48163264 |60%20%5%5%10% |80%80%80%80%80% |88888 |Wszystkie 100% losowe |
| Program SQL Server (woluminu 1) programu SQL Server (woluminu 2) |864 |100%100% |70%0% |88 |100% random100% sekwencyjne |
| Exchange |32 |100% |67% |8 |100% losowe |
| Stacja robocza/VDI |464 |66%34% |70%95% |11 |Losowe zarówno 100% |
| Serwer plików w sieci Web |4864 |33%34%33% |95%95%95% |888 |Wszystkie 75% losowe |

### <a name="vm-configuration"></a>Konfiguracja maszyny Wirtualnej

* 470 maszyny wirtualne w klastrze podstawowego.
* Wszystkie maszyny wirtualne z dyskiem VHDX.
* Maszyny wirtualne w uruchamianiu obciążeń liczonych podsumowane w tabeli. Wszystkie zostały utworzone przy użyciu szablonów w programie VMM.

| Obciążenie | # Maszyn wirtualnych | Minimalna ilość pamięci RAM (GB) | Maksymalna ilość pamięci RAM (GB) | Rozmiar dysku logicznego (GB) na maszynę Wirtualną | Maksymalna liczba IOPS |
| --- | --- | --- | --- | --- | --- |
| Oprogramowanie SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Serwer plików |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Serwer sieci Web |149 |.5 |1 |80 |6 |
| SUMA |470 | | |96.83 TB |4108 |

### <a name="site-recovery-settings"></a>Ustawień usługi Site Recovery

* Usługa Site Recovery została skonfigurowana dla środowiska lokalnego do ochrony w środowisku lokalnym
* Serwer VMM ma cztery skonfigurowano chmury, zawierającą serwery w klastrze funkcji Hyper-V i maszyn wirtualnych.

| Chmura podstawowa programu VMM | Chronione maszyny wirtualne | Częstotliwość replikacji | Dodatkowe punkty odzyskiwania |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 min. |Brak |
| PrimaryCloudRpo30s |47 |30 sekund |Brak |
| PrimaryCloudRpo30sArp1 |47 |30 sekund |1 |
| PrimaryCloudRpo5m |235 |5 min |Brak |

### <a name="performance-metrics"></a>Metryki wydajności

W tabeli przedstawiono metryki wydajności i liczniki, które zostały mierzone we wdrożeniu.

| Metryka | Licznik |
| --- | --- |
| Procesor CPU |\Processor(_Total)\% Processor Time |
| Dostępna pamięć |\Memory\Available pamięć (MB) |
| Operacje wejścia/wyjścia |Transfery \Disk \PhysicalDisk (%) (_łącznie) na sekundę |
| Maszyna wirtualna (IOPS) operacje odczytu/s |\Hyper-V wirtualne urządzenie magazynujące (\<wirtualnego dysku twardego >) \Read OP./s |
| Operacje zapisu (IOPS) maszyny Wirtualnej/s |\Hyper-V wirtualne urządzenie magazynujące (\<wirtualnego dysku twardego >) \Write operacji/S |
| Przepływność zapisu maszyny Wirtualnej |\Hyper-V wirtualne urządzenie magazynujące (\<wirtualnego dysku twardego >) \Read bajty/s |
| Przepływność zapisu na maszynie Wirtualnej |\Hyper-V wirtualne urządzenie magazynujące (\<wirtualnego dysku twardego >) \Write bajty/s |

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie replikacji](hyper-v-vmm-disaster-recovery.md)
