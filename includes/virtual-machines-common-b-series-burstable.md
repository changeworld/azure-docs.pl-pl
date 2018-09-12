---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 14feb7ad09a24904034f9ae90cf4a54cf786a44c
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44369408"
---
Rodzina maszyn wirtualnych z serii B pozwala wybrać rozmiar maszyny Wirtualnej, w którym zapewnia niezbędne podstawowy poziom wydajności dla obciążenia, z możliwością przejścia wydajność procesora CPU do 100% Intel® Broadwell E5-2673 v4 2,3 GHz lub procesor Intel® Haswell 2,4 GHz E5-2673 v3 Procesor wirtualny vCPU.

Maszyny wirtualne z serii B są idealne dla obciążeń, które nie są pełnej wydajności procesora CPU w sposób ciągły, takich jak serwery sieci web, dowód pojęcia i małych baz danych oraz wdrażanie środowisk kompilacji. Te obciążenia mają zwykle wymagań dotyczących wydajności z możliwością zwiększania wydajności. Seria B zapewnia możliwość nabycia rozmiar maszyny Wirtualnej z linii bazowej wydajności i wystąpienia maszyny Wirtualnej są gromadzone środki na korzystanie z podczas wykorzystywana jest mniejsza niż jej linii bazowej. Gdy maszyna wirtualna zgromadzonych środków, maszyna wirtualna może serii powyżej linii bazowej, przy użyciu procesora wirtualnego vCPU do 100%, gdy aplikacja wymaga wyższej wydajności procesorów CPU.

Seria B jest dostępna w następujących sześć rozmiarów maszyn wirtualnych:

| Rozmiar          | procesory wirtualne | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Bazowej wydajności procesora CPU maszyny wirtualnej | Maksymalna liczba wydajności procesora CPU maszyny wirtualnej | Środki na korzystanie z wpłaty / godzinę | Maksymalna liczba wpłaty środki na korzystanie z |
|---------------|--------|-------------|----------------|--------------------------------|---------------------------|-----------------------|--------------------|
| Standard_B1s  | 1      | 1           | 4              | 10%                            | 100%                      | 6                     | 144                |
| Standard_B1ms | 1      | 2           | 4              | 20%                            | 100%                      | 12                    | 288                |
| Standard_B2s  | 2      | 4           | 8              | 40%                            | 200%                      | 24                    | 576                |
| Standard_B2ms | 2      | 8           | 16             | 60%                            | 200%                      | 36                    | 864                |
| Standard_B4ms | 4      | 16          | 32             | 90%                            | 400%                      | 54                    | 1296               |
| Standard_B8ms | 8      | 32          | 64             | 135%                           | 800%                      | 81                    | 1944               |




## <a name="q--a"></a>Pytania i odpowiedzi 

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>P: jak uzyskać 135% bazowej wydajności z maszyny Wirtualnej
**A**: 135% jest współużytkowany przez 8 procesory wirtualne wchodzące w skład rozmiar maszyny Wirtualnej. Na przykład jeśli aplikacja korzysta z 4 z 8 rdzeniami nad przetwarzania wsadowego, a każda z tych 4 procesory wirtualne są uruchamiane o 30% wykorzystania łączną wydajność procesora CPU maszyny Wirtualnej będzie równy 120%.  Co oznacza, że maszyny Wirtualnej będzie tworzenia czas środki, w oparciu o delta 15% od wydajności bazowego.  Ale oznacza to również, że przypadku środków dostępnych w tej samej maszyny Wirtualnej użyć 100% wszystkich 8 procesorów wirtualnych użytkownika zapewniając tej maszyny Wirtualnej maks. wydajność 800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>P: jak można monitorować Moje salda środków i użycie
**A**: Firma Microsoft będzie wprowadzona 2 nowe metryki w najbliższych tygodniach **środki** metryki pozwala wyświetlić ile środków na korzystanie z maszyny Wirtualnej ma wpłaty i **ConsumedCredit** metryki będą wyświetlane jak wiele procesora CPU środki na korzystanie z maszyny Wirtualnej zużyła z banku.    Będzie można wyświetlić te metryki z poziomu okienka metryki w witrynie portal lub programowo za pośrednictwem interfejsów API usługi Azure Monitor.

Aby uzyskać więcej informacji na temat dostępu do danych metryk dla platformy Azure, zobacz [Przegląd metryk w systemie Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>P: jak środki są zbierane?
**A**: stawki gromadzenia i użycie maszyny Wirtualnej są ustawiane w taki sposób, że maszynę Wirtualną z systemem w dokładnie jej poziom wydajności bazowej będzie miał netto akumulacja ani zużycia przenoszenie obsługi dużego ruchu środki na korzystanie z.  Maszyny Wirtualnej musi wzrostu netto środków na korzystanie z zawsze wtedy, gdy jest on uruchomiony pod jej poziom wydajności bazowej i będzie miał netto spadek środki na korzystanie z zawsze wtedy, gdy maszyna wirtualna jest wykorzystujących procesor CPU, więcej niż jej poziom wydajności bazowej.

**Przykład**: czy mogę wdrożyć Maszynę wirtualną przy użyciu rozmiaru B1ms dla mojej aplikacji bazy danych w krótkim czasie i obecności. Ten rozmiar umożliwia mojej aplikacji służące do 20% procesora wirtualnego vCPU jako planu bazowego, czyli 0,2 środków na minutę, których można używać lub bank. 

Moja aplikacja jest zajęta na początku i końcu Moje dzień roboczy pracowników, między 7:00-9:00:00 i 4:00-18:00:00. W trakcie innych 20 godzin dnia, Moja aplikacja jest zazwyczaj przy bezczynności (%), tylko przy użyciu 10% procesora wirtualnego vCPU. Godziny poza szczytem, czy mogę zdobyć 0,2 środków na minutę, ale tylko używać środków 0.l na minutę, więc Moja maszyna wirtualna będzie Bankowi 0,1 x 60 = 6 środki na godzinę.  20 godzin, których jestem poza szczytem I będzie bankowego 120 kredytów.  

W godzinach szczytu Moja aplikacja średnie wykorzystanie procesora wirtualnego vCPU 60%, nadal jest zdobyć 0,2 środków na minutę, ale mogę używać 0,6 środków na minutę, net kosztem 0,4 kredytów minutę lub Update 0.4 x 60 = 24 środki o wartości na godzinę. Ma 4 godziny dziennie szczytowe użycie, więc koszt wynosi 4 x 24 = 96 środki na korzystanie z mojego użycia szczytowego.

Czy w przypadku podjęcia 120 kredytów, który mogę zdobytych poza godzinami i odejmowanie 96 środków, używane dla mojego szczytu, czy bankowego dodatkowych kredytów 24, dziennie, którego mogę użyć innych wzmożeniach działania.


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>Pyt.: seria B obsługuje dyski z danymi usługi Premium Storage?
**A**: tak, wszystkie rozmiary serii B obsługi dysków z danymi usługi Premium Storage.   
    
### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>P: Dlaczego mój pozostałe środki wynosi 0 po ponownego wdrażania lub zatrzymać/uruchomić?
**A** : gdy maszyna wirtualna jest "REDPLOYED", a maszyna wirtualna przechodzi do innego węzła, skumulowany środki zostaną utracone. Jeśli maszyna wirtualna jest zatrzymana/uruchomiona, ale pozostaje na tym samym węźle, maszyna wirtualna zachowuje skumulowana środków. Zawsze, gdy maszyna wirtualna zacznie od nowa w węźle, otrzymuje początkowe środki, Standard_B8ms jest 240 minut.

    

    
