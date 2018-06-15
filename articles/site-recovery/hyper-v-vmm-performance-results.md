---
title: Wyniki testu dla replikacji maszyn wirtualnych funkcji Hyper-V w chmurach VMM do lokacji dodatkowej z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje o testowanie wydajnościowe na potrzeby replikacji maszyn wirtualnych funkcji Hyper-V w chmurach VMM do lokacji dodatkowej przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2018
ms.author: raynew
ms.openlocfilehash: e15f435a3f32b8908b5b93bccc6c57710ab589bc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29378897"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Wyniki testu dla funkcji Hyper-V replikacji do lokacji dodatkowej

W tym artykule przedstawiono wyniki testu podczas replikowania maszyn wirtualnych funkcji Hyper-V w chmurach programu System Center Virtual Machine Manager (VMM), do dodatkowego centrum danych wydajności.

## <a name="test-goals"></a>Cele testu

Celem badania było zbadać sposób wykonywania odzyskiwania lokacji podczas replikacji stanie stabilności.

- Stanie stabilności replikacji występuje, gdy maszyny wirtualne zakończyły replikację początkową i synchronizacji zmiany różnicowe.
- Ważne jest do pomiaru wydajności przy użyciu stałej stanu, ponieważ jest w stanie, w których większość maszyn wirtualnych pozostają, chyba że wystąpienia nieoczekiwanych awarii.
- Testowe wdrożenie składa się z dwoma lokacjami lokalnymi, z serwerem programu VMM w każdej lokacji. Tego wdrożenia testowego jest typowy head/oddział wdrożenia pakietu office, z centrali działający jako lokacji głównej i w biurze oddziału jako lokacji dodatkowej lub odzyskiwania.

## <a name="what-we-did"></a>Firma Microsoft może

Oto, co możemy w teście przeszedł:

1. Maszyny wirtualne utworzone za pomocą szablonów programu VMM.
2. Uruchomione maszyny wirtualne, a następnie przechwycić metryki wydajności bazowej niż 12 godzin.
3. Utworzony chmur na serwerze podstawowym i odzyskiwania serwerów programu VMM.
4. Replikacji w lokacji odzyskiwania, w tym mapowania między źródłem i odzyskiwania chmury.
5. Włączyć ochronę maszyn wirtualnych i możliwość ukończenia replikacji początkowej.
6. Oczekiwano kilku godzinach stabilizacji systemu.
7. Przechwytywane metryki wydajności niż 12 godzin, gdzie wszystkich maszyn wirtualnych pozostają nieoczekiwany stan replikacji dla tych 12 godzin.
8. Mierzone różnica między metryki wydajności bazowej i metryk wydajności replikacji.


## <a name="primary-server-performance"></a>Wydajność serwera podstawowego

* Funkcji Hyper-V Replica (używane przez usługę Site Recovery) asynchronicznie śledzi zmiany w pliku dziennika z magazynem minimalna narzut na serwerze podstawowym.
* Repliki funkcji Hyper-V korzysta z własnym zapewnienia pamięci podręcznej, aby zminimalizować liczbę IOPS nakładów pracy dla śledzenia. Przechowuje zapisuje VHDX w pamięci i opróżnia je w pliku dziennika, przed upływem terminu, dziennik jest wysyłane do witryny odzyskiwania. Dysk opróżniania odbywa się, jeśli zapisami osiągnęła limit wcześniej.
* Wykres poniżej przedstawia stanie stabilności obciążenie IOPS dla replikacji. Widać, że IOPS nakłady pracy związane z powodu replikacji jest około 5%, która jest mało.

  ![Podstawowy wyników](./media/hyper-v-vmm-performance-results/IC744913.png)

Repliki funkcji Hyper-V używa pamięci na serwerze podstawowym, aby zoptymalizować wydajność dysku. Jak pokazano na wykresie, pamięć, narzut na wszystkich serwerach w klastrze podstawowego jest brzegowych. Pamięć narzut to wartości procentowej pamięci używana przez funkcję replikacji, w porównaniu do całkowitej ilości pamięci zainstalowanych na serwerze funkcji Hyper-V.

![Podstawowy wyników](./media/hyper-v-vmm-performance-results/IC744914.png)

Funkcja Hyper-V Replica ma minimalną obciążenia Procesora. Jak pokazano na wykresie, obciążenia związanego z replikacją jest z zakresu od 2 do 3%.

![Podstawowy wyników](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Wydajność serwera pomocniczego

Repliki funkcji Hyper-V używa mała ilość pamięci na serwerze odzyskiwania w celu optymalizowania liczby operacji magazynu. Wykres zawiera podsumowanie użycia pamięci na serwerze odzyskiwania. Pamięć narzut to wartości procentowej pamięci używana przez funkcję replikacji, w porównaniu do całkowitej ilości pamięci zainstalowanych na serwerze funkcji Hyper-V.

![Wyniki dodatkowej](./media/hyper-v-vmm-performance-results/IC744916.png)

Liczba operacji We/Wy w lokacji odzyskiwania jest funkcją liczbę operacji zapisu w lokacji głównej. Załóżmy przyjrzeć się całkowita liczba operacji We/Wy w lokacji odzyskiwania w porównaniu z całkowita liczba operacji We/Wy i zapisu w lokacji głównej. Wykresy pokazują, że łączną liczbę IOPS w lokacji odzyskiwania jest

* Około 1,5 raza zapisu IOPS na serwerze podstawowym.
* Około 37% całkowitej IOPS w lokacji głównej.

![Wyniki dodatkowej](./media/hyper-v-vmm-performance-results/IC744917.png)

![Wyniki dodatkowej](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Wpływ na wykorzystanie sieci

Średnia 275 Mb na sekundę przepustowość sieci użyto między węzłami podstawowymi i odzyskiwania (z włączoną kompresją), przed istniejących przepustowości 5 Gb na sekundę.

![Wykorzystanie sieci wyników](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Wpływ na wydajność maszyny Wirtualnej

Ważną kwestią jest jego wpływ na replikację w przypadku obciążeń produkcyjnych uruchomionych na maszynach wirtualnych. Jeśli w lokacji głównej można ją było właściwie jest przeznaczona do replikacji, brak nie powinny być ich wpływ na obciążenie. Lightweight funkcji Hyper-V Replica śledzenia mechanizm zapewnia nie dotyczy obciążeń uruchomionych na maszynach wirtualnych podczas replikacji stanie stabilności. Jest to zilustrowane na poniższych wykresach.

Ten wykres pokazuje liczbę IOPS wykonywane przez maszyny wirtualne z różnych obciążeń przed i po włączeniu replikacji. Można obserwować, czy nie ma żadnej różnicy między nimi.

![Wyniki efekt repliki](./media/hyper-v-vmm-performance-results/IC744920.png)

Wykres ukazuje przepływności maszyn wirtualnych uruchomionych różnych obciążeń przed i po włączeniu replikacji. Można obserwować, czy replikacja nie ma znaczący wpływu.

![Efekty repliki wyników](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Podsumowanie

Wyraźnie wyniki wskazują, czy usługi Site Recovery, w połączeniu z funkcji Hyper-V Replica skaluje się z co najmniej w czasie dużych klastra. Usługa Site Recovery zapewnia proste wdrożenie, replikacji, zarządzania i monitorowania. Repliki funkcji Hyper-V udostępnia infrastrukturę niezbędne zakończona pomyślnie replikacja skalowania. 

## <a name="test-environment-details"></a>Szczegóły środowiska testowego

### <a name="primary-site"></a>Lokacja główna

* Lokacja główna ma klastrze zawierającym pięć serwerów funkcji Hyper-V, uruchomionych 470 maszyn wirtualnych.
* Maszyny wirtualne uruchomione różnych obciążeń i wszystkie ma włączoną ochronę usługi Site Recovery.
* Magazyn dla węzła klastra jest zapewniana przez sieć SAN iSCSI. Model – Hitachi HUS130.
* Każdy serwer klastra ma cztery karty sieciowe (NIC) z jedną GB.
* Dwie karty sieciowe są podłączone do sieci prywatnej iSCSI, a dwie są podłączone do sieci zewnętrznej przedsiębiorstwa. Jedną z sieci zewnętrznej jest zarezerwowana dla tylko na komunikację klastra.

![Podstawowe wymagania sprzętowe](./media/hyper-v-vmm-performance-results/IC744922.png)

| Serwer | Pamięć RAM | Model | Procesor | Liczba procesorów | NIC | Oprogramowanie |
| --- | --- | --- | --- | --- | --- | --- |
| Serwery funkcji Hyper-V w klastrze: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128ESTLAB HOST25 ma 256 |R820 firmy Dell PowerEdge™ |Intel(R) Xeon(R) CPU E5-4620 0 @ 2.20GHz |4 |I GB/s x 4 |Windows Server Datacenter 2012 R2 (x64) + roli funkcji Hyper-V |
| Serwer programu VMM |2 | | |2 |1 Gb/s |Windows Server bazy danych 2012 R2 (x 64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Lokacja dodatkowa

* Lokacja dodatkowa ma klastra pracy awaryjnej sześciu węzłów.
* Magazyn dla węzła klastra jest zapewniana przez sieć SAN iSCSI. Model – Hitachi HUS130.

![Specyfikacja podstawowy sprzęt](./media/hyper-v-vmm-performance-results/IC744923.png)

| Serwer | Pamięć RAM | Model | Procesor | Liczba procesorów | NIC | Oprogramowanie |
| --- | --- | --- | --- | --- | --- | --- |
| Serwery funkcji Hyper-V w klastrze: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |R720 firmy Dell PowerEdge™ |Intel(R) Xeon(R) E5 procesora CPU — 2630 0 @ 2.30GHz |2 |I GB/s x 4 |Windows Server Datacenter 2012 R2 (x64) + roli funkcji Hyper-V |
| ESTLAB-HOST17 |128 |R820 firmy Dell PowerEdge™ |Intel(R) Xeon(R) CPU E5-4620 0 @ 2.20GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + roli funkcji Hyper-V |
| ESTLAB-HOST24 |256 |R820 firmy Dell PowerEdge™ |Intel(R) Xeon(R) CPU E5-4620 0 @ 2.20GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + roli funkcji Hyper-V |
| Serwer programu VMM |2 | | |2 |1 Gb/s |Windows Server bazy danych 2012 R2 (x 64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Obciążenie serwera

* Do celów testowych możemy pobrać obciążeń często używane w scenariuszach dla przedsiębiorstw klienta.
* Używamy [IOMeter](http://www.iometer.org) z cech obciążenia podsumowane w tabeli na symulacyjnych.
* Wszystkie profile IOMeter są ustawione na zapis losowych bajtów do symulowania najgorszych zapisu wzorce dla obciążeń.

| Obciążenie | We/Wy rozmiar (KB) | % Dostępu | % Odczytu | Oczekujące operacje We/Wy | Wzorzec operacji We/Wy |
| --- | --- | --- | --- | --- | --- |
| Serwer plików |48163264 |60%20%5%5%10% |80%80%80%80%80% |88888 |Wszystkie 100% losowych |
| SQL Server (woluminu 1) programu SQL Server (woluminu 2) |864 |100%100% |70%0% |88 |100% random100% sekwencyjne |
| Exchange |32 |100% |67% |8 |losowe 100% |
| Stacja robocza/VDI |464 |66%34% |70%95% |11 |Losowe zarówno 100% |
| Serwer plików w sieci Web |4864 |33%34%33% |95%95%95% |888 |Wszystkie 75% losowych |

### <a name="vm-configuration"></a>Konfiguracja maszyny Wirtualnej

* 470 maszyny wirtualne w klastrze podstawowego.
* Wszystkie maszyny wirtualne z dysku VHDX.
* Maszyny wirtualne z obciążeniami podsumowane w tabeli. Wszystkie zostały utworzone szablony programu VMM.

| Obciążenie | # Maszyny wirtualne | Minimalna ilość pamięci RAM (GB) | Maksymalna ilość pamięci RAM (GB) | Rozmiar dysku logicznego (GB) dla maszyny Wirtualnej | Maksymalna liczba IOPS |
| --- | --- | --- | --- | --- | --- |
| Oprogramowanie SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Serwer plików |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Serwer sieci Web |149 |.5 |1 |80 |6 |
| ŁĄCZNIE |470 | | |96.83 TB |4108 |

### <a name="site-recovery-settings"></a>Ustawienia odzyskiwania lokacji

* Usługa Site Recovery został skonfigurowany do środowiska lokalnego do ochrony lokalnej
* Serwer VMM ma cztery skonfigurowaną chmurę, zawierającą serwery w klastrze funkcji Hyper-V i ich maszyny wirtualne.

| Podstawowy chmury VMM | Chronione maszyny wirtualne | Częstotliwość replikacji | Dodatkowe punkty odzyskiwania |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minut. |None |
| PrimaryCloudRpo30s |47 |30 sekund |None |
| PrimaryCloudRpo30sArp1 |47 |30 sekund |1 |
| PrimaryCloudRpo5m |235 |5 minut. |None |

### <a name="performance-metrics"></a>Metryki wydajności

W tabeli przedstawiono metryki wydajności i liczniki, które zostały zmierzone w ramach wdrożenia.

| Metryka | Licznik |
| --- | --- |
| Procesor CPU |\Processor(_Total)\% Processor Time |
| Dostępna pamięć |\Memory\Available (MB) |
| Operacje wejścia/wyjścia |Transfery \Disk \PhysicalDisk (_Total) na sekundę |
| Maszyna wirtualna (IOPS) operacje odczytu/s |\Hyper-V wirtualne urządzenie magazynujące (<VHD>) \Read operacji na sekundę |
| Operacje zapisu (IOPS) maszyny Wirtualnej na sekundę |\Hyper-V wirtualne urządzenie magazynujące (<VHD>) \Write operacji/S |
| Przepływność odczytu dla maszyny Wirtualnej |\Hyper-V wirtualne urządzenie magazynujące (<VHD>) \Read bajty/s |
| Przepływność zapisu maszyny Wirtualnej |\Hyper-V wirtualne urządzenie magazynujące (<VHD>) \Write bajty/s |

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie replikacji](hyper-v-vmm-disaster-recovery.md)
