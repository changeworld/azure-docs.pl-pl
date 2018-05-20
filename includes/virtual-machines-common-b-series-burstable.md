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
ms.openlocfilehash: 8a7207328f49488b0df8f6e1e0ed86c6f965d32f
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
Rodziny wirtualna B serii można wybrać rozmiar maszyny Wirtualnej, w którym dostarcza niezbędne podstawowym poziomie wydajności dla obciążenia, możliwość serii wydajności procesora CPU do 100% v4 Intel® Broadwell E5-2673 2.3 GHz lub procesor Intel® Haswell 2,4 GHz E5-2673 v3 vCPU.

Maszyny wirtualne serii B idealnie nadają się do obciążeń, które nie muszą pełną wydajność procesora, takich jak serwery sieci web, małych baz danych i programowania i testowania środowisk. Te obciążenia mają zwykle burstable wymagania. Seria B zapewnia możliwość nabycia dla rozmiaru maszyny Wirtualnej z linii bazowej wydajności i wystąpienia maszyny Wirtualnej buduje środków, podczas korzystania z mniej niż jego linii bazowej. Po zebraniu środki maszyny Wirtualnej, maszyna wirtualna może serii powyżej linii bazowej, przy użyciu maksymalnie 100% vCPU, gdy aplikacja wymaga wyższej wydajności procesora CPU.

Seria B składa się z następujących sześciu rozmiarów maszyn wirtualnych:

| Rozmiar          | w vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Podstawowej wydajności procesora CPU maszyny wirtualnej | Maksymalna liczba wydajności procesora CPU maszyny wirtualnej | Kredyty wpłaty / godzina | Maksymalna liczba wpłaty środków |
|---------------|--------|-------------|----------------|--------------------------------|---------------------------|-----------------------|--------------------|
| Standard_B1s  | 1      | 1           | 4              | 10%                            | 100%                      | 6                     | 144                |
| Standard_B1ms | 1      | 2           | 4              | 20%                            | 100%                      | 12                    | 288                |
| Standard_B2s  | 2      | 4           | 8              | 40%                            | 200%                      | 24                    | 576                |
| Standard_B2ms | 2      | 8           | 16             | 60%                            | 200%                      | 36                    | 864                |
| Standard_B4ms | 4      | 16          | 32             | 90%                            | 400%                      | 54                    | 1296               |
| Standard_B8ms | 8      | 32          | 64             | 135%                           | 800%                      | 81                    | 1944               |




## <a name="q--a"></a>Pytania i odpowiedzi 

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>Pytanie: jak uzyskać 135% linii bazowej wydajności z maszyny Wirtualnej
**A**: 135% jest udostępniana między 8 vCPU firmy wchodzące w skład rozmiar maszyny Wirtualnej. Na przykład jeśli aplikacja korzysta z 4 8 rdzeni pracy przetwarzania wsadowego, a każdy z tych 4 vCPU są uruchomione na 30% wykorzystania całkowitej wydajności procesora CPU maszyny Wirtualnej będzie równy 120%.  Co oznacza, czy maszyna wirtualna będzie tworzenia czas środki w oparciu o różnice 15% z linii bazowej wydajności.  Ale oznacza to również, że jeśli użytkownik ma dostępne, że tej samej maszyny Wirtualnej może używać 100% wszystkich vCPU 8 środków przez nadanie tej maszyny Wirtualnej maks. wydajność 800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>Pytanie: jak można monitorować Moje salda środków oraz zużycie
**A**: Firma Microsoft będzie wprowadzenie 2 nowe metryki w najbliższych tygodniach **środki** Metryka pozwala wyświetlić liczbę środków maszyny Wirtualnej ma wpłaty i **ConsumedCredit** Metryka przedstawia liczbę procesorów środki na korzystanie z banku zużyła maszyny Wirtualnej.    Będzie mogła wyświetlać te metryki z okienka metryki w portalu lub programistycznie za pośrednictwem interfejsów API usługi Azure monitora.

Aby uzyskać więcej informacji na temat sposobu uzyskania dostępu do danych metryki dla platformy Azure, zobacz [omówienie metryk w Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>Pytanie: jak zgromadzonych środków
**A**: stawki gromadzenia i zużycia maszyny Wirtualnej są ustawione tak, aby maszyny Wirtualnej z systemem poziomie dokładnie jego podstawowej wydajności będzie mieć net akumulacji ani zużycia poszerzająca środków.  Maszyny Wirtualnej zostanie wzrostu netto w środków zawsze, gdy jest uruchomiona poniżej poziomu wydajność bazy i będzie miał net spadek środków zawsze, gdy maszyna wirtualna jest wykorzystanie procesora CPU, więcej niż poziom jego wydajność bazy.

**Przykład**: wdrożyć Maszynę wirtualną przy użyciu rozmiaru B1ms dla mojej aplikacji bazy danych w krótkim czasie i obecności. Ten rozmiar umożliwia Moja aplikacja ma być używana do 20% vCPU jako Mój linii bazowej jest.2 środków na minutę, których można użyć lub bank. 

Moja aplikacja jest zajęta na początku i na koniec dnia roboczego pracowników, między 7:00-9:00 AM a 4:00 — 6:00 PM. W innych 20 godzin dnia, mojej aplikacji jest zwykle w stanie bezczynności, tylko przy użyciu 10% vCPU. Na godziny poza szczytem I zdobyć 0,2 środków na minutę, ale tylko używać 0.l środków na minutę, więc Moja maszyna wirtualna zostanie Bankowi.1 x 60 = 6 środków na godzinę.  Do 20 godzin, w których jestem poza szczytem I będzie Bankowi 120 środków.  

W godzinach szczytu Moja aplikacja oblicza średnią 60% wykorzystania vCPU, I nadal zdobyć 0,2 środków na minutę, ale korzystać z 0,6 środków na minutę, net koszty.4 środków minutę lub.4 x 60 = 24 środków na korzystanie z na godzinę. Ma cztery godziny dziennie szczytowego wykorzystania, więc koszt wynosi 4 x 24 = 96 środków dla szczytowego użycia.

Czy w przypadku podjęcia środków 120, który I uzyskany poza szczytem i odjąć 96 kredytu używane dla moich godzinach szczytu, Bankowi I dodatkowe kredyty 24 dziennie, którego można użyć dla innych seria działań.


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>Pytanie: czy serii B obsługuje magazyn w warstwie Premium dyski danych?
**A**: tak, wszystkie rozmiary serii B obsługi dysków z danymi magazyn w warstwie Premium.   
    
### <a name="q-why-is-my-remaining-credit-are-set-to-0-after-a-redepoy-or-a-stopstart"></a>Pytanie: Dlaczego jest moje pozostałe środki są ustawione na 0 po redepoy lub stop/start?
**A** : gdy maszyna wirtualna jest "REDPLOYED" i maszyny Wirtualnej są przenoszone do innego węzła, skumulowany środki zostaną utracone. Jeśli maszyna wirtualna jest zatrzymana uruchomiona, ale pozostaje w tym samym węźle, maszyna wirtualna zachowuje skumulowany środków. Zawsze, gdy maszyna wirtualna zacznie świeże w węźle, staje kredytu początkowej, Standard_B8ms jest 240 minut.

    

    
