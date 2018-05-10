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
ms.openlocfilehash: 24d89b617c347bc9443b437c92cb034acb3e05cb
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
Podczas tworzenia maszyny wirtualnej (VM), ponownego uruchomienia zatrzymanej maszyny wirtualnej (cofnięciu przydziału) lub zmień rozmiar maszyny Wirtualnej Microsoft Azure przydziela zasoby obliczeniowe do subskrypcji. Firma Microsoft stale najlepiej zainwestować w dodatkowe infrastruktury i funkcje, aby upewnić się, że zawsze mamy wszystkie typy maszyn wirtualnych do obsługi klientów. Jednak czasami mogą wystąpić błędy alokacji zasobów z powodu Niespotykana wzrostu popytu na usług platformy Azure w określonych regionach. Ten problem może wystąpić przy próbie utworzenia lub uruchomić maszyny wirtualne w regionie, gdy maszyny wirtualne są wyświetlane następujący kod błędu i komunikat:

**Kod błędu:**: AllocationFailed lub ZonalAllocationFailed

**Komunikat o błędzie**: "alokacja nie powiodła się. W tym regionie nie ma wystarczającą pojemność żądany rozmiar maszyny Wirtualnej. Dowiedz się więcej o poprawy prawdopodobieństwo sukcesu alokacji w http://aka.ms/allocation-guidance"

W tym artykule opisano przyczyny niektórych typowych błędów alokacji, a także sugeruje możliwe środki zaradcze.

Jeśli problem Azure nie jest opisany w tym artykule, odwiedź [Azure fora MSDN i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Możesz zamieścić problemu na tych fora lub do @AzureSupport w serwisie Twitter. Ponadto można pliku żądania pomocy technicznej platformy Azure, wybierając Uzyskaj pomoc techniczną w [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) lokacji.

Dopóki preferowany typ maszyny Wirtualnej jest dostępny w Twoim regionie preferowany, zaleca się klienci, którzy występują problemy z wdrażaniem wziąć pod uwagę wskazówki zawarte w poniższej tabeli jako celu tymczasowego obejścia problemu. 

Scenariusz, który najlepiej odpowiada Twoim przypadku zidentyfikować, a następnie ponów żądanie alokacji przy użyciu odpowiedniego sugerowane rozwiązania zwiększyć prawdopodobieństwo sukcesu alokacji. Alternatywnie możesz zawsze. Spróbuj ponownie później. Jest to spowodowane za mało zasobów został zwolniony w klastrze, regionu lub strefę, aby obsłużyć żądanie. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Zmień rozmiar maszyny Wirtualnej lub dodawanie maszyn wirtualnych do istniejącego zestawu dostępności

### <a name="cause"></a>Przyczyna

Żądanie rozmiar maszyny Wirtualnej lub dodać do istniejącego zestawu dostępności maszyny Wirtualnej musi nastąpiła w oryginalnego klastra obsługującego dostępności istniejącego zestawu. Żądany rozmiar maszyny Wirtualnej jest obsługiwana przez klaster, ale klaster nie może obecnie miał wystarczającą pojemność. 

### <a name="workaround"></a>Obejście

Jeśli maszyna wirtualna może być częścią zestawu dostępności innego, należy utworzyć Maszynę wirtualną w różnych dostępności, ustawić (w tym samym regionie). Następnie można dodać tej nowej maszyny Wirtualnej do tej samej sieci wirtualnej.

Zatrzymaj (deallocate) wszystkich maszyn wirtualnych w tym samym dostępności ustawiona, a następnie uruchom ponownie każdej z nich.
Aby zatrzymać: kliknij grup zasobów > [grupie zasobów] > zasobów > [zestawu dostępności] > maszyn wirtualnych > [maszyny wirtualnej] > Zatrzymaj.
Po zatrzymanie wszystkich maszyn wirtualnych, wybierz pierwsza maszyna wirtualna, a następnie kliknij przycisk Uruchom.
Ten krok zapewnia uruchomieniu nowego próba alokacji i że nowy klaster można wybrać, który ma wystarczającą pojemność.

## <a name="restart-partially-stopped-deallocated-vms"></a>Uruchom ponownie częściowo zatrzymania maszyny wirtualnej (cofnięciu przydziału)

### <a name="cause"></a>Przyczyna

Częściowe dezalokacji oznacza, że zatrzymane (cofnięciu przydziału) co najmniej jeden, ale nie wszystkie, maszyn wirtualnych w dostępności ustawione. Gdy deallocate maszyny Wirtualnej, są wydawane skojarzonych zasobów. Ponowne uruchamianie maszyn wirtualnych w zestawie dostępności częściowo deallocated jest taka sama jak dodawanie maszyn wirtualnych do istniejącego zestawu dostępności. W związku z tym żądanie alokacji musi być podejmowane w oryginalnym klastrze hostów, dla których zestawu dostępności istniejących, które może nie mieć wystarczającej.

### <a name="workaround"></a>Obejście

Zatrzymaj (deallocate) wszystkich maszyn wirtualnych w tym samym dostępności ustawiona, a następnie uruchom ponownie każdej z nich.
Aby zatrzymać: kliknij grup zasobów > [grupie zasobów] > zasobów > [zestawu dostępności] > maszyn wirtualnych > [maszyny wirtualnej] > Zatrzymaj.
Po zatrzymanie wszystkich maszyn wirtualnych, wybierz pierwsza maszyna wirtualna, a następnie kliknij przycisk Uruchom.
Zapewni to, uruchomieniu nowego próba alokacji i że nowy klaster można wybrać, który ma wystarczającą pojemność.

## <a name="restart-fully-stopped-deallocated-vms"></a>Uruchom ponownie pełni zatrzymania maszyny wirtualnej (cofnięciu przydziału)

### <a name="cause"></a>Przyczyna

Pełna dezalokacji oznacza, że został zatrzymany (cofnięciu przydziału) wszystkich maszyn wirtualnych w zestawie dostępności. Żądanie alokacji o ponowne uruchomienie tych maszyn wirtualnych będzie obowiązywać wszystkich klastrów, które obsługują wymagany rozmiar w obrębie regionu lub strefy. Zmień żądania alokacji na sugestie w tym artykule i ponów żądanie, aby zwiększyć prawdopodobieństwo pomyślnego alokacji. 

### <a name="workaround"></a>Obejście

Jeśli używasz starszej serii maszyn wirtualnych lub rozmiary, takich jak Dv1, DSv1, Av1, D15v2 lub DS15v2, należy wziąć pod uwagę przeniesienie do nowszych wersji. Zobacz te zalecenia dotyczące określonych rozmiarów maszyn wirtualnych.
Jeśli nie masz opcję, aby użyć innego rozmiaru maszyny Wirtualnej, spróbuj przeprowadzić wdrożenie w innym regionie, w ramach tej samej lokalizacji geograficznej. Aby uzyskać więcej informacji o dostępnych rozmiarów maszyn wirtualnych w każdym regionie w https://aka.ms/azure-regions

Jeśli używane są strefy dostępności, spróbuj innej strefy w regionie, który może być dostępna pojemność żądany rozmiar maszyny Wirtualnej.

Jeśli żądanie alokacji jest duży (więcej niż 500 rdzenie), zobacz wskazówki zawarte w następnych sekcjach, aby podzielić żądania w mniejszych wdrożeniach.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Błędy alokacji starsze rozmiarów maszyn wirtualnych (Av1 Dv1, DSv1, D15v2, DS15v2, itp.)

Jak możemy rozwinąć infrastruktury platformy Azure, możemy wdrożyć sprzęcie nowszej generacji, zaprojektowany w celu obsługi najnowszej typów maszyny wirtualnej. Niektóre starsze serii maszyn wirtualnych nie należy uruchamiać na naszych najnowsze infrastruktury generacji. Z tego powodu klienci od czasu do czasu mogą występować błędy alokacji dla tych starszych wersji produktu. Aby uniknąć tego problemu, zachęcamy klientów, którzy przy użyciu starszej wersji serii maszyn wirtualnych należy wziąć pod uwagę przeniesienie na równoważne nowsza maszynami wirtualnymi na poniższe zalecenia: te maszyny wirtualne są zoptymalizowane pod kątem najnowszego sprzętu i będzie można korzystać z lepiej cennik i wydajności. 

|Starsze rozmiar maszyny Wirtualnej — serii /|Zalecane nowszej rozmiar maszyny Wirtualnej — serii /|Więcej informacji|
|----------------------|----------------------------|--------------------|
|Av1 serii|[Av2 serii](../articles/virtual-machines/windows/sizes-general.md#av2-series)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Dv1 lub serii DSv1 (D1 do D5)|[Dv3 lub DSv3 serii](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-sup1sup)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1 lub serii DSv1 (D11 do D14)|[Ev3 lub ESv3 serii](../articles/virtual-machines/windows/sizes-memory.md#ev3-series)|
|D15v2 lub DS15v2|Jeśli używasz modelu wdrażania Menedżera theResource Aby korzystać z większych rozmiarów maszyn wirtualnych, rozważ migrację D16v3/DS16v3 lub D32v3/DS32v3. Są one przeznaczone do uruchamiania w najnowszej sprzęcie generacji. Jeśli używasz modelu wdrażania usługi Resource Manager do upewnij się, że wystąpienie maszyny Wirtualnej jest izolowana sprzętu przeznaczonego do jednego klienta, należy wziąć pod uwagę przeniesienie do nowego izolowanego rozmiary maszyn, E64i_v3 lub E64is_v3, które są przeznaczone do uruchamiania w najnowszej sprzęcie generacji. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Błędy alokacji w przypadku dużych wdrożeń (ponad 500 rdzenie)

Zmniejsz liczbę wystąpień żądany rozmiar maszyny Wirtualnej, a następnie spróbuj ponownie wykonać operację wdrożenia. Ponadto w przypadku większych wdrożeń, może chcesz ocenić [zestawy skalowania maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Liczba wystąpień maszyn wirtualnych można automatycznie zwiększyć lub zmniejszyć w odpowiedzi na żądanie lub zdefiniowany harmonogram, i mieć większe prawdopodobieństwo sukcesu alokacji, ponieważ wdrożeń można było ich rozmieszczenie wielu klastrów. 

## <a name="background-information"></a>Informacje uzupełniające
### <a name="how-allocation-works"></a>Jak działa alokacji
Serwery w centrach danych platformy Azure są partycjonowane na klastry. Przeważnie próba wykonania żądania alokacji jest podejmowana w wielu klastrach, ale istnieje możliwość, że niektóre ograniczenia wynikające z żądania alokacji wymuszają próbę żądania tylko w jednym klastrze platformy Azure. W tym artykule firma Microsoft będzie odwoływać się do to jako "przypięty do klastra". Poniższy diagram 1 przedstawiono w przypadku normalnych alokacji, który zostanie użyty w wielu klastrach. Diagram 2 przedstawia to alokacji który zawiera przypięta do klastra 2, ponieważ jest to, gdzie jest hostowana istniejącego zestawu CS_1 usługi w chmurze lub dostępności.
![Diagram alokacji](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Dlaczego stanie błędów alokacji
Żądanie alokacji jest przypięta do klastra, ma wyższy stopień można znaleźć zwolnić zasoby, ponieważ pula zasobów dostępny jest mniejszy. Ponadto jeśli żądanie alokacji jest przypięta do klastra, ale typ zasobu, która miała nie jest obsługiwany przez ten klaster, żądanie zakończy się niepowodzeniem nawet w przypadku klastra z bezpłatnych zasobów. Następujące 3 Diagram przedstawia przypadek, w których alokacji przypiętych nie powiedzie się, ponieważ klaster tylko kandydujące nie ma zwolnić zasoby. Diagram 4 przedstawia przypadek, w którym przypiętych alokacji nie działa, ponieważ klastra tylko kandydata nie obsługuje żądany rozmiar maszyny Wirtualnej, nawet jeśli klaster ma zwolnić zasoby.

![Błąd alokacji przypiętych](./media/virtual-machines-common-allocation-failure/Allocation2.png)


