---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 6a3e2034792fdc0a4a8fed7885c7d5ad78ea24d9
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67501262"
---
Rodzina maszyn wirtualnych z serii B pozwala wybrać rozmiar maszyny Wirtualnej, w którym zapewnia niezbędne podstawowy poziom wydajności dla obciążenia, z możliwością przejścia wydajność procesora CPU do 100% Intel® Broadwell E5-2673 v4 2,3 GHz lub procesor Intel® Haswell 2,4 GHz E5-2673 v3 Procesor wirtualny vCPU.

Maszyny wirtualne z serii B są idealne dla obciążeń, które nie są pełnej wydajności procesora CPU w sposób ciągły, takich jak serwery sieci web, dowód pojęcia i małych baz danych oraz wdrażanie środowisk kompilacji. Te obciążenia mają zwykle wymagań dotyczących wydajności z możliwością zwiększania wydajności. Seria B zapewnia możliwość nabycia rozmiar maszyny Wirtualnej z linii bazowej wydajności i wystąpienia maszyny Wirtualnej są gromadzone środki na korzystanie z podczas wykorzystywana jest mniejsza niż jej linii bazowej. Gdy maszyna wirtualna zgromadzonych środków, maszyna wirtualna może serii powyżej linii bazowej, przy użyciu procesora wirtualnego vCPU do 100%, gdy aplikacja wymaga wyższej wydajności procesorów CPU.

Seria B jest dostępna w następujących rozmiarów maszyn wirtualnych:

| Size             | Procesor wirtualny  | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Podstawowa wydajność procesora CPU maszyny wirtualnej | Maksymalna wydajność procesora CPU maszyny wirtualnej | Początkowe środki | Zapisane środki/godz. | Maksymalna ilość zapisanych środków | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: IOPS / MB/s | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202%                  | 1200%                   | 360                   | 121                 | 2909           | 16                                     | 6480 / 75                                 | 4320 / 50                                  | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270%                  | 1600%                   | 480                   | 162                 | 3888           | 32                                     | 8640 / 100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337%                  | 2000%                   | 600                   | 203                 | 4860           | 32                                     | 10800 / 125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls jest obsługiwana tylko w systemie Linux

## <a name="workload-example"></a>Przykładowe obciążenie

Należy wziąć pod uwagę aplikacji pakietu office/wyewidencjonowania. Aplikacja musi wzrosty procesora CPU w godzinach pracy, ale nie dużej mocy obliczeniowej podczas poza godzinami pracy. W tym przykładzie obciążenia wymaga 16vCPU maszynę wirtualną z 64GiB pamięci RAM na potrzeby wydajnej pracy.

W tabeli przedstawiono dane o ruchu co godzinę i wykres jest wizualną reprezentację tego ruchu.

B16 cechy:

Maks. danych o wydajności: 16vCPU * 100% = 1600%

Punkt odniesienia: 270%

![Wykres przedstawiający co godzinę dane o ruchu](./media/virtual-machines-common-b-series-burstable/office-workload.png)

| Scenariusz | Time | Użycie procesora CPU (%) | Środki na korzystanie z zgromadzonych<sup>1</sup> | Środki na korzystanie z dostępnych |
| --- | --- | --- | --- | --- |
| B16ms wdrożenia | Wdrożenie | Wdrożenie  | 480 (początkowe środki) | 480 |
| Brak ruchu | 0:00 | 0 | 162 | 642 |
| Brak ruchu | 1:00 | 0 | 162 | 804 |
| Brak ruchu | 2:00 | 0 | 162 | 966 |
| Brak ruchu | 3:00 | 0 | 162 | 1128 |
| Brak ruchu | 4:00 | 0 | 162 | 1290 |
| Brak ruchu | 5:00 | 0 | 162 | 1452 |
| O niewielkim ruchu | 6:00 | 270 | 0 | 1452 |
| Pracownicy są dostarczane do pakietu office (aplikacja wymaga procesora wirtualnego vCPU 80%) | 7:00 | 1280 | -606 | 846 |
| Pracownicy nadal dostępne do pakietu office (aplikacja wymaga procesora wirtualnego vCPU 80%) | 8:00 | 1280 | -606 | 240 |
| O niewielkim ruchu | 9:00 | 270 | 0 | 240 |
| O niewielkim ruchu | 10:00 | 100 | 102 | 342 |
| O niewielkim ruchu | 11:00 | 50 | 132 | 474 |
| O niewielkim ruchu | 12:00 | 100 | 102 | 576 |
| O niewielkim ruchu | 13:00 | 100 | 102 | 678 |
| O niewielkim ruchu | 14:00 | 50 | 132 | 810 |
| O niewielkim ruchu | 15:00 | 100 | 102 | 912 |
| O niewielkim ruchu | 16:00 | 100 | 102 | 1014 |
| Pracowników, wyewidencjonowywanie (vCPU aplikacji potrzebom 100%) | 17:00 | 1600 | -798 | 216 |
| O niewielkim ruchu | 18:00 | 270 | 0 | 216 |
| O niewielkim ruchu | 19:00 | 270 | 0 | 216 |
| O niewielkim ruchu | 20:00 | 50 | 132 | 348 |
| O niewielkim ruchu | 21:00 | 50 | 132 | 480 |
| Brak ruchu | 22:00 | 0 | 162 | 642 |
| Brak ruchu | 23:00 | 0 | 162 | 804 |

<sup>1</sup> środki na korzystanie z zgromadzonych/środki na korzystanie z używany w ciągu godziny jest odpowiednikiem: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes`.  

W przypadku D16s_v3, która ma 16 procesorów wirtualnych Vcpu i 64 GiB pamięci stawka godzinowa jest $0.936 za godzinę (miesięczne $673.92) i B16ms z 16 procesorów wirtualnych Vcpu i 64 GiB pamięci, że stawka jest 0.794 $ za godzinę (miesięczne $547.86). <b> Skutkuje to 15% oszczędności!</b>

## <a name="q--a"></a>Pytania i odpowiedzi

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>Pyt.: Jak uzyskać 135% bazowej wydajności z maszyny Wirtualnej
**Odp.:** 135% jest współużytkowany przez 8 procesory wirtualne wchodzące w skład rozmiar maszyny Wirtualnej. Na przykład jeśli aplikacja korzysta z 4 z 8 rdzeniami nad przetwarzania wsadowego, a każda z tych 4 procesory wirtualne są uruchamiane o 30% wykorzystania łączną wydajność procesora CPU maszyny Wirtualnej będzie równy 120%.  Co oznacza, że maszyny Wirtualnej będzie tworzenia czas środki, w oparciu o delta 15% od wydajności bazowego.  Ale oznacza to również, że przypadku środków dostępnych w tej samej maszyny Wirtualnej użyć 100% wszystkich 8 procesorów wirtualnych użytkownika zapewniając tej maszyny Wirtualnej maks. wydajność 800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>Pyt.: Jak można monitorować Moje salda środków i użycie
**Odp.:** Firma Microsoft będzie wprowadzona 2 nowe metryki w najbliższych tygodniach **środki** metryki pozwala wyświetlić ile środków na korzystanie z maszyny Wirtualnej ma wpłaty i **ConsumedCredit** metryki będą wyświetlane jak wiele środki na procesory CPU maszyny Wirtualnej ma używane z konta w banku.    Będzie można wyświetlić te metryki z poziomu okienka metryki w witrynie portal lub programowo za pośrednictwem interfejsów API usługi Azure Monitor.

Aby uzyskać więcej informacji na temat dostępu do danych metryk dla platformy Azure, zobacz [Przegląd metryk w systemie Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>Pyt.: Jak środki są zbierane?
**Odp.:** Stawki gromadzenia i użycie maszyny Wirtualnej są ustawiane w taki sposób, że maszynę Wirtualną z systemem w dokładnie jej poziom wydajności bazowej będzie miał netto akumulacja ani zużycia przenoszenie obsługi dużego ruchu środki na korzystanie z.  Maszyny Wirtualnej musi wzrostu netto środków na korzystanie z zawsze wtedy, gdy jest on uruchomiony pod jej poziom wydajności bazowej i będzie miał netto spadek środki na korzystanie z zawsze wtedy, gdy maszyna wirtualna jest wykorzystujących procesor CPU, więcej niż jej poziom wydajności bazowej.

**Przykład**:  Czy mogę wdrożyć Maszynę wirtualną przy użyciu rozmiaru B1ms Mój czas mała liczba godzin i obecności aplikacji bazy danych. Ten rozmiar umożliwia mojej aplikacji służące do 20% procesora wirtualnego vCPU jako planu bazowego, czyli 0,2 środków na minutę, których można używać lub bank. 

Moja aplikacja jest zajęta na początku i końcu Moje dzień roboczy pracowników, między 7:00-9:00:00 i 4:00-18:00:00. W trakcie innych 20 godzin dnia, Moja aplikacja jest zazwyczaj przy bezczynności (%), tylko przy użyciu 10% procesora wirtualnego vCPU. Godziny poza szczytem, czy mogę zdobyć 0,2 środków na minutę, ale tylko używać środków 0.l na minutę, więc Moja maszyna wirtualna będzie Bankowi 0,1 x 60 = 6 środki na godzinę.  20 godzin, których jestem poza szczytem I będzie bankowego 120 kredytów.  

W godzinach szczytu Moja aplikacja średnie wykorzystanie procesora wirtualnego vCPU 60%, nadal jest zdobyć 0,2 środków na minutę, ale mogę używać 0,6 środków na minutę, net kosztem 0,4 kredytów minutę lub Update 0.4 x 60 = 24 środki o wartości na godzinę. Ma 4 godziny dziennie szczytowe użycie, więc koszt wynosi 4 x 24 = 96 środki na korzystanie z mojego użycia szczytowego.

Czy w przypadku podjęcia 120 kredytów, który mogę zdobytych poza godzinami i odejmowanie 96 środków, używane dla mojego szczytu, czy bankowego dodatkowych kredytów 24, dziennie, którego mogę użyć innych wzmożeniach działania.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>Pyt.: Jak obliczyć, środki na korzystanie z zgromadzonych i używać?
**Odp.:** Można użyć następującej formuły: 

(Bazowej wydajności procesora CPU maszyny wirtualnej — użycie Procesora) / 100 = bank środków lub użycia na minutę

np. w powyżej wystąpienia według planu bazowego jest 20% i użycie procesora CPU są grupowania 10% (20-10%) / 100 = 0,1 środki na minutę.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>Pyt.: Seria B obsługuje dyski z danymi usługi Premium Storage?
**Odp.:** Tak, wszystkie rozmiary serii B obsługuje dyski z danymi usługi Premium Storage.   
    
### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>Pyt.: Dlaczego moja pozostałe środki wynosi 0 po ponownego wdrażania lub zatrzymać/uruchomić?
**A** : Gdy maszyna wirtualna jest "REDPLOYED" i maszyna wirtualna przechodzi do innego węzła, skumulowany środki zostaną utracone. Jeśli maszyna wirtualna jest zatrzymana/uruchomiona, ale pozostaje na tym samym węźle, maszyna wirtualna zachowuje skumulowana środków. Zawsze, gdy maszyna wirtualna zacznie od nowa w węźle, otrzymuje początkowe środki, Standard_B8ms jest 240 minut.
    
### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>Pyt.: Co się stanie, czy wdrożenie obrazu systemu operacyjnego nieobsługiwany na B1ls?
**A** : B1ls obsługuje tylko obrazy systemu Linux, a w przypadku wdrożenia dowolnego innego obraz systemu operacyjnego nie może pobrać w najlepszym środowisku klienta.
