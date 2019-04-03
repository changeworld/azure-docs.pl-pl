---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, azure-resource-manager
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/14/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 8afaeaea3c8ceb0de33147e70e00f3f57c857ca7
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883160"
---
Utwórz maszynę wirtualną (VM), ponowne uruchamianie zatrzymano (cofnięto przydział) maszyn wirtualnych lub zmienić rozmiar maszyny Wirtualnej z systemem Microsoft Azure przydziela zasoby obliczeniowe, aby Twoja subskrypcja. Firma Microsoft stale inwestuje w dodatkową infrastrukturę i funkcje, aby upewnić się, że zawsze mamy wszystkie typy maszyn wirtualnych do obsługi klientów. Jednak czasami mogą występować błędy alokacji zasobów z powodu niespotykany wzrost popytu na usługi platformy Azure w określonych regionach. Ten problem może wystąpić podczas próby utworzenia, lub uruchomić maszyny wirtualne w regionie, gdy maszyny wirtualne wyświetl następujący kod błędu i komunikat:

**Kod błędu:**: AllocationFailed lub ZonalAllocationFailed

**Komunikat o błędzie**: "Niepowodzenie alokacji. Nie mamy wystarczającej pojemności dla żądanego rozmiaru maszyny Wirtualnej w tym regionie. Przeczytaj więcej na temat zwiększania prawdopodobieństwa pomyślnej alokacji sukcesu w https://aka.ms/allocation-guidance"

W tym artykule opisano przyczyny niektórych typowych błędów alokacji i sugeruje możliwe środki zaradcze.

Jeśli problem z platformą Azure nie jest opisany w tym artykule, odwiedź stronę [fora poświęcone systemowi Azure w witrynie MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Możesz zadać problemu na tych forów lub do @AzureSupport w serwisie Twitter. Ponadto mogą zgłaszać żądanie pomocy technicznej platformy Azure, wybierając Uzyskaj pomoc techniczną na [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) lokacji.

Do momentu preferowany typ maszyny Wirtualnej jest dostępna w Twoim regionie preferowaną, zaleca się klienci, którzy występują problemy z wdrażaniem wziąć pod uwagę wskazówki zawarte w poniższej tabeli jako rozwiązanie tymczasowe. 

Identyfikowanie scenariusza, który najlepiej odpowiada tej sprawy, a następnie ponów próbę wykonania żądania alokacji przy użyciu odpowiedniego rozwiązania sugerowane, aby zwiększyć prawdopodobieństwo powodzenia alokacji. Alternatywnie możesz zawsze ponowić próbę później. Jest to spowodowane za mało zasobów zostały zwolnione w klastrze, regionu lub strefy, aby obsłużyć Twojego żądania. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Zmiana rozmiaru maszyny wirtualnej lub dodanie maszyn wirtualnych do istniejącego zestawu dostępności

### <a name="cause"></a>Przyczyna

Żądanie, aby zmienić rozmiar maszyny Wirtualnej lub Dodaj do istniejącego zestawu dostępności maszyny Wirtualnej musi sprawdzone w oryginalnym klastrze hostującym istniejących dostępności zestawu. Żądany rozmiar maszyny Wirtualnej jest obsługiwana przez klaster, ale klaster może obecnie nie masz wystarczającej pojemności. 

### <a name="workaround"></a>Obejście

Jeśli maszyna wirtualna może być częścią zestawu dostępności różnych, Utwórz Maszynę wirtualną w innej zestaw dostępności (w tym samym regionie). Następnie można dodać tej nowej maszyny Wirtualnej do tej samej sieci wirtualnej.

Zatrzymaj (Cofnij ich przydział) wszystkich maszyn wirtualnych w tym samym dostępności zestawu, a następnie ponownie uruchom każdy z nich.
Aby zatrzymać: Kliknij opcję grupy zasobów > [grupy zasobów] > Zasoby > [zestawie dostępności] > maszyny wirtualne > [maszyny wirtualnej] > Zatrzymaj.
Po zatrzymania wszystkich maszyn wirtualnych, wybierz pierwszą maszynę Wirtualną, a następnie kliknij przycisk Uruchom.
Ten krok zapewnia, że uruchomieniu nowej próba alokacji i że nowy klaster można wybrać z wystarczającą pojemnością.

## <a name="restart-partially-stopped-deallocated-vms"></a>Ponowne uruchomienie częściowo zatrzymanych maszyn wirtualnych (z cofniętą alokacją)

### <a name="cause"></a>Przyczyna

Częściowe cofania alokacji oznacza, że zatrzymana (przydział zostanie cofnięty) co najmniej jeden, ale nie wszystkich maszyn wirtualnych w dostępności zestawu. Gdy cofany jest przydział maszyny Wirtualnej, są wydawane skojarzonych zasobów. Ponowne uruchamianie maszyn wirtualnych w zestawie dostępności częściowo przydział zostanie cofnięty jest taka sama, jak dodawanie maszyn wirtualnych do istniejącego zestawu dostępności. W związku z tym żądania alokacji musi sprawdzone w oryginalnym klastrze hostów, które zestaw dostępności istniejący, które może nie mieć wystarczającej pojemności.

### <a name="workaround"></a>Obejście

Zatrzymaj (Cofnij ich przydział) wszystkich maszyn wirtualnych w tym samym dostępności zestawu, a następnie ponownie uruchom każdy z nich.
Aby zatrzymać: Kliknij opcję grupy zasobów > [grupy zasobów] > Zasoby > [zestawie dostępności] > maszyny wirtualne > [maszyny wirtualnej] > Zatrzymaj.
Po zatrzymania wszystkich maszyn wirtualnych, wybierz pierwszą maszynę Wirtualną, a następnie kliknij przycisk Uruchom.
Spowoduje to się, że uruchomieniu nowej próba alokacji i że nowy klaster można wybrać z wystarczającą pojemnością.

## <a name="restart-fully-stopped-deallocated-vms"></a>Ponowne uruchomienie w pełni zatrzymanych maszyn wirtualnych (z cofniętą alokacją)

### <a name="cause"></a>Przyczyna

Oznacza pełną dezalokacji zostanie zatrzymana (cofnięty przydział) wszystkich maszyn wirtualnych w zestawie dostępności. Żądanie alokacji, aby ponownie uruchomić te maszyny wirtualne będą ukierunkowane na wszystkich klastrach, które obsługują żądany rozmiar w obrębie regionu lub strefy. Zmień żądania alokacji na sugestie w tym artykule, a następnie ponowić próbę żądania, aby zwiększyć prawdopodobieństwo powodzenia alokacji. 

### <a name="workaround"></a>Obejście

Jeśli używasz starszej serię maszyn wirtualnych lub rozmiarów, takich jak Dv1, DSv1, Av1, D15v2 lub DS15v2, należy wziąć pod uwagę przeniesienie do nowszych wersji. Zobacz te zalecenia dotyczące określonych rozmiarów maszyn wirtualnych.
Jeśli nie masz możliwość użycia innego rozmiaru maszyny Wirtualnej, spróbuj wykonać wdrożenie w innym regionie, w ramach tej samej lokalizacji geograficznej. Aby uzyskać więcej informacji na temat dostępnych rozmiarów maszyn wirtualnych w każdym regionie, w https://aka.ms/azure-regions

Jeśli używane są strefy dostępności, spróbuj innej strefy w regionie, który może mieć dostępnej pojemności dla żądanego rozmiaru maszyny Wirtualnej.

Jeśli żądania alokacji jest duży (ponad 500 rdzeni), zobacz wskazówki zawarte w poniższych sekcjach dzielenia żądania na mniejszych wdrożeniach.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Błędy alokacji starsze rozmiarów maszyn wirtualnych (Av1, Dv1, DSv1, D15v2, DS15v2 itp.)

W miarę rozwijania infrastruktury platformy Azure, możemy wdrożyć sprzęt nowszej generacji, która jest przeznaczona do obsługi najnowszych typów maszyn wirtualnych. Niektóre starsze maszyny wirtualne z serii nie uruchamiaj w infrastrukturze najnowszej generacji. Z tego powodu klienci od czasu do czasu mogą występować błędy alokacji tych starszych jednostek SKU. Aby uniknąć tego problemu, firma Microsoft zachęca klientów korzystających z maszyn wirtualnych z serii starszych rozważ przeniesienie na równoważne nowsze maszyny wirtualne na poniższe zalecenia: Te maszyny wirtualne są zoptymalizowane pod kątem najnowszego sprzętu i będzie można korzystać z zalet lepsze ceny i wydajności. 

|Starsze maszyny Wirtualnej serii/wielkości|Zalecany nowszej rozmiar maszyny Wirtualnej — seria /|Więcej informacji|
|----------------------|----------------------------|--------------------|
|Av1 serii|[Seria Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Serii DSv1 lub Dv1 (D1 – D5)|[Dv3 i Seria Dsv3](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Serii DSv1 lub Dv1 (D11 do D14)|[Ev3 lub innej serii Esv3](../articles/virtual-machines/windows/sizes-memory.md#esv3-series)|
|D15v2 lub DS15v2|Jeśli używasz modelu wdrażania Menedżera theResource aby można było korzystać z zalet większych rozmiarów maszyn wirtualnych, należy wziąć pod uwagę przeniesienie E16v3/E16sv3 lub E32v3/E32sv3. Są one przeznaczone do uruchamiania na sprzęcie najnowszej generacji. Jeśli używasz modelu wdrażania usługi Resource Manager, aby upewnić się, że z wystąpieniem maszyny Wirtualnej jest izolowane do sprzętu dedykowanego pojedynczemu klientowi, rozważ przejście do nowego izolowane rozmiarów maszyny Wirtualnej, element E64i_v3 lub element E64is_v3, które są zaprojektowane do uruchamiania na sprzęcie najnowszej generacji. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/


## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Błędy alokacji w przypadku dużych wdrożeń (ponad 500 rdzeni)

Zmniejsz liczbę wystąpień żądany rozmiar maszyny Wirtualnej, a następnie spróbuj ponownie wykonać operację wdrożenia. Ponadto w przypadku większych wdrożeń, możesz chcieć oceny [zestawów skalowania maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Liczba wystąpień maszyn wirtualnych automatycznie można zwiększyć lub zmniejszyć w odpowiedzi na zapotrzebowanie lub zdefiniowany harmonogram, a masz większe prawdopodobieństwo powodzenia alokacji, ponieważ wdrożeń mogły być rozkładane na wiele klastrów. 

## <a name="background-information"></a>Informacje uzupełniające
### <a name="how-allocation-works"></a>Jak działa alokacji
Serwery w centrach danych platformy Azure są partycjonowane na klastry. Przeważnie próba wykonania żądania alokacji jest podejmowana w wielu klastrach, ale istnieje możliwość, że niektóre ograniczenia wynikające z żądania alokacji wymuszają próbę żądania tylko w jednym klastrze platformy Azure. W tym artykule będziemy odnosić się do tego jako "przypięte do klastra". 1 poniżej diagramie przypadku normalnych alokacji, który zostanie użyty w wielu klastrach. Diagram 2 ilustruje przypadek alokacji, został przypięty do klastra 2, ponieważ jest to, gdzie znajduje się istniejącego zestawu CS_1 usługi w chmurze lub dostępności.
![Diagram alokacji](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Dlaczego zdarzają się błędy alokacji
Gdy żądanie alokacji zostanie przypięty do klastra ma wyższe prawdopodobieństwo, których nie można odnaleźć bezpłatnych zasobów, ponieważ pula zasobów dostępny jest mniejszy. Ponadto jeśli żądania alokacji jest przypięta do klastra, ale typ zasobu, której szukasz nie jest obsługiwana przez ten klaster, Twoje żądanie zakończy się niepowodzeniem nawet wtedy, gdy klaster ma bezpłatnymi zasobami. Następujące 3 Diagram ilustruje przypadek, w którym przypiętych alokacja nie powiedzie się, ponieważ klaster tylko kandydujące nie ma bezpłatnymi zasobami. Diagram 4 ilustruje przypadek, gdzie przypiętych alokacji nie działa, ponieważ klaster tylko Release candidate nie obsługuje żądanego rozmiaru maszyny Wirtualnej, mimo że klaster ma bezpłatnymi zasobami.

![Niepowodzenie alokacji przypięte](./media/virtual-machines-common-allocation-failure/Allocation2.png)


