---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: ecf70bbbeae8fd68309f3343615f021038fb10b6
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66111434"
---
Rodzina maszyn wirtualnych z serii B pozwala wybrać rozmiar maszyny Wirtualnej, w którym zapewnia niezbędne podstawowy poziom wydajności dla obciążenia, z możliwością przejścia wydajność procesora CPU do 100% Intel® Broadwell E5-2673 v4 2,3 GHz lub procesor Intel® Haswell 2,4 GHz E5-2673 v3 Procesor wirtualny vCPU.

Maszyny wirtualne z serii B są idealne dla obciążeń, które nie są pełnej wydajności procesora CPU w sposób ciągły, takich jak serwery sieci web, dowód pojęcia i małych baz danych oraz wdrażanie środowisk kompilacji. Te obciążenia mają zwykle wymagań dotyczących wydajności z możliwością zwiększania wydajności. Seria B zapewnia możliwość nabycia rozmiar maszyny Wirtualnej z linii bazowej wydajności i wystąpienia maszyny Wirtualnej są gromadzone środki na korzystanie z podczas wykorzystywana jest mniejsza niż jej linii bazowej. Gdy maszyna wirtualna zgromadzonych środków, maszyna wirtualna może serii powyżej linii bazowej, przy użyciu procesora wirtualnego vCPU do 100%, gdy aplikacja wymaga wyższej wydajności procesorów CPU.

Seria B jest dostępna w następujących sześć rozmiarów maszyn wirtualnych:

| Rozmiar             | Procesor wirtualny  | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Bazowej wydajności procesora CPU maszyny wirtualnej | Maksymalna liczba wydajności procesora CPU maszyny wirtualnej | Środki początkowe | Środki na korzystanie z wpłaty / godzinę | Maksymalna liczba wpłaty środki na korzystanie z | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: IOPS / MB/s | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |

<sup>1</sup> B1ls jest obsługiwana tylko w systemie Linux

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


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>Pyt.: Seria B obsługuje dyski z danymi usługi Premium Storage?
**Odp.:** Tak, wszystkie rozmiary serii B obsługuje dyski z danymi usługi Premium Storage.   
    
### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>Pyt.: Dlaczego moja pozostałe środki wynosi 0 po ponownego wdrażania lub zatrzymać/uruchomić?
**A** : Gdy maszyna wirtualna jest "REDPLOYED" i maszyna wirtualna przechodzi do innego węzła, skumulowany środki zostaną utracone. Jeśli maszyna wirtualna jest zatrzymana/uruchomiona, ale pozostaje na tym samym węźle, maszyna wirtualna zachowuje skumulowana środków. Zawsze, gdy maszyna wirtualna zacznie od nowa w węźle, otrzymuje początkowe środki, Standard_B8ms jest 240 minut.
    
### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>Pyt.: Co się stanie, czy wdrożenie obrazu systemu operacyjnego nieobsługiwany na B1ls?
**A** : B1ls obsługuje tylko obrazy systemu Linux, a w przypadku wdrożenia dowolnego innego obraz systemu operacyjnego nie może pobrać w najlepszym środowisku klienta.
    
### <a name="q-why-is-there-no-pricing-information-for-b1ls-windows"></a>Pyt.: Dlaczego jest nie informacje o cenach dla systemu windows B1ls
**A** : B1ls obsługuje wyłącznie obrazy systemu Linux, a w przypadku wdrożenia dowolnego innego obraz systemu operacyjnego mogą różnić się w najlepszym środowisku klienta, ale opłaty zostaną naliczone.


    

    
