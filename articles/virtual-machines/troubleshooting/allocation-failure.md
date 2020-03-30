---
title: Rozwiązywanie problemów z błędami alokacji maszyn wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z błędami alokacji podczas tworzenia, ponownego uruchamiania lub ponownego rozmiaru maszyny wirtualnej na platformie Azure
services: virtual-machines
documentationcenter: ''
author: JiangChen79
manager: felixwu
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 04/13/2018
ms.author: cjiang
ms.openlocfilehash: b4750ad9fdfa214aa4d7b6a0355c319e7eb1d9c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484404"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>Rozwiązywanie problemów związanych z błędami alokacji występującymi podczas tworzenia lub ponownego uruchamiania maszyn wirtualnych na platformie Azure bądź zmieniania ich rozmiaru

Podczas tworzenia maszyny wirtualnej (VM), ponowne uruchomienie zatrzymane (cofnięto przydział) maszyn wirtualnych lub zmienić rozmiar maszyny wirtualnej, Microsoft Azure przydziela zasoby obliczeniowe do subskrypcji. Nieustannie inwestujemy w dodatkową infrastrukturę i funkcje, aby upewnić się, że zawsze mamy wszystkie typy maszyn wirtualnych dostępne do obsługi popytu klientów. Jednak od czasu do czasu mogą wystąpić błędy alokacji zasobów z powodu bezprecedensowego wzrostu popytu na usługi platformy Azure w określonych regionach. Ten problem może wystąpić podczas próby utworzenia lub uruchomienia maszyn wirtualnych w regionie, podczas gdy maszyny wirtualne wyświetlają następujący kod błędu i komunikat:

**Kod błędu:** AlokacjaFailed lub ZonalAllocationFailed

**Komunikat o błędzie**: "Alokacja nie powiodła się. Nie mamy wystarczającej pojemności dla żądanego rozmiaru maszyny Wirtualnej w tym regionie. Przeczytaj więcej o zwiększaniu prawdopodobieństwa\/sukcesu alokacji na https: /aka.ms/allocation-guidance"

W tym artykule wyjaśniono przyczyny niektórych typowych błędów alokacji i sugeruje możliwe środki zaradcze.

Jeśli problem z platformą Azure nie został rozwiązany w tym artykule, odwiedź [fora platformy Azure w witrynie MSDN i Przepełnienie stosu.](https://azure.microsoft.com/support/forums/) Możesz opublikować swój problem na @AzureSupport tych forach lub na Twitterze. Ponadto można złożyć żądanie pomocy technicznej platformy Azure, wybierając pozycję Uzyskaj pomoc techniczną w witrynie [pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/options/)

Dopóki preferowany typ maszyny Wirtualnej nie będzie dostępny w preferowanym regionie, zalecamy klientom, którzy napotykają problemy z wdrażaniem, aby potraktowali wskazówki w poniższej tabeli jako tymczasowe obejście. 

Zidentyfikuj scenariusz, który najlepiej pasuje do sprawy, a następnie ponów próbę żądania alokacji, używając odpowiedniego sugerowanego obejścia, aby zwiększyć prawdopodobieństwo powodzenia alokacji. Alternatywnie zawsze można ponowić próbę później. Jest tak, ponieważ w klastrze, regionie lub strefie mogło zostać zwolniona wystarczająca ilość zasobów, aby uwzględnić żądanie. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Zmiana rozmiaru maszyny wirtualnej lub dodanie maszyn wirtualnych do istniejącego zestawu dostępności

### <a name="cause"></a>Przyczyna

Żądanie, aby zmienić rozmiar maszyny Wirtualnej lub dodać maszynę wirtualną do istniejącego zestawu dostępności, musi zostać wypróbowane w oryginalnym klastrze obsługującym istniejący zestaw dostępności. Żądany rozmiar maszyny Wirtualnej jest obsługiwany przez klaster, ale klaster może obecnie nie mieć wystarczającej pojemności. 

### <a name="workaround"></a>Obejście

Jeśli maszyna wirtualna może być częścią innego zestawu dostępności, utwórz maszynę wirtualną w innym zestawie dostępności (w tym samym regionie). Tę nową maszynę wirtualną można następnie dodać do tej samej sieci wirtualnej.

Zatrzymaj (cofnięto przydział) wszystkie maszyny wirtualne w tym samym zestawie dostępności, a następnie uruchom ponownie każdą z nich.
Aby zatrzymać: kliknij pozycję Grupy zasobów > [grupa zasobów] > zasoby > [zestaw dostępności] > maszyny wirtualne > [maszyna wirtualna] > zatrzymaj.
Po zatrzymaniu wszystkich maszyn wirtualnych wybierz pierwszą maszynę wirtualną, a następnie kliknij przycisk Start.
Ten krok zapewnia, że zostanie uruchomiony nowy próba alokacji i że można wybrać nowy klaster, który ma wystarczającą pojemność.

## <a name="restart-partially-stopped-deallocated-vms"></a>Ponowne uruchomienie częściowo zatrzymanych maszyn wirtualnych (z cofniętą alokacją)

### <a name="cause"></a>Przyczyna

Częściowe alokacji oznacza, że zatrzymano (cofnięto przydział) jeden lub więcej, ale nie wszystkie, maszyny wirtualne w zestawie dostępności. Po przydzieleniu alokacji maszyny Wirtualnej skojarzone zasoby są zwalniane. Ponowne uruchamianie maszyn wirtualnych w częściowo cofnięto alokacji zestawu dostępności jest taka sama jak dodawanie maszyn wirtualnych do istniejącego zestawu dostępności. W związku z tym żądanie alokacji musi być wypróbowany w oryginalnym klastrze, który obsługuje istniejący zestaw dostępności, który może nie mieć wystarczającej pojemności.

### <a name="workaround"></a>Obejście

Zatrzymaj (cofnięto przydział) wszystkie maszyny wirtualne w tym samym zestawie dostępności, a następnie uruchom ponownie każdą z nich.
Aby zatrzymać: kliknij pozycję Grupy zasobów > [grupa zasobów] > zasoby > [zestaw dostępności] > maszyny wirtualne > [maszyna wirtualna] > zatrzymaj.
Po zatrzymaniu wszystkich maszyn wirtualnych wybierz pierwszą maszynę wirtualną, a następnie kliknij przycisk Start.
Zapewni to, że zostanie uruchomiony nowy próba alokacji i że można wybrać nowy klaster, który ma wystarczającą pojemność.

## <a name="restart-fully-stopped-deallocated-vms"></a>Ponowne uruchomienie w pełni zatrzymanych maszyn wirtualnych (z cofniętą alokacją)

### <a name="cause"></a>Przyczyna

Pełna alokacja oznacza, że zatrzymano (cofnięto przydział) wszystkie maszyny wirtualne w zestawie dostępności. Żądanie alokacji, aby ponownie uruchomić te maszyny wirtualne będzie kierować wszystkie klastry, które obsługują żądany rozmiar w regionie lub strefie. Zmień żądanie alokacji według sugestii w tym artykule i ponów próbę żądania, aby zwiększyć szansę na powodzenie alokacji. 

### <a name="workaround"></a>Obejście

Jeśli używasz starszych serii lub rozmiarów maszyn wirtualnych, takich jak Dv1, DSv1, Av1, D15v2 lub DS15v2, rozważ przejście do nowszych wersji. Zobacz te zalecenia dotyczące określonych rozmiarów maszyn wirtualnych.
Jeśli nie masz opcji używania innego rozmiaru maszyny Wirtualnej, spróbuj wdrożyć w innym regionie w tym samym środowisku geograficznym. Aby uzyskać więcej informacji na temat dostępnych rozmiarów maszyn wirtualnych w każdym regioniehttps://aka.ms/azure-regions

Jeśli używasz stref dostępności, spróbuj innej strefy w regionie, który może mieć dostępną pojemność dla żądanego rozmiaru maszyny Wirtualnej.

Jeśli żądanie alokacji jest duży (więcej niż 500 rdzeni), zobacz wskazówki w poniższych sekcjach, aby podzielić żądanie na mniejsze wdrożenia.

Spróbuj [ponownie wdrożyć maszynę wirtualną](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/redeploy-to-new-node-windows). Ponowne wdrożenie maszyny Wirtualnej przydziela maszynę wirtualną do nowego klastra w regionie.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Błędy alokacji dla starszych rozmiarów maszyn wirtualnych (Av1, Dv1, DSv1, D15v2, DS15v2 itp.)

W miarę rozszerzania infrastruktury platformy Azure wdrażamy sprzęt nowszej generacji, który jest przeznaczony do obsługi najnowszych typów maszyn wirtualnych. Niektóre maszyny wirtualne starszej serii nie są uruchamiane w naszej infrastrukturze najnowszej generacji. Z tego powodu klienci mogą od czasu do czasu wystąpić błędy alokacji dla tych starszych jednostek SKU. Aby uniknąć tego problemu, zachęcamy klientów korzystających z starszych maszyn wirtualnych serii, aby rozważyli przejście do równoważnych nowszych maszyn wirtualnych zgodnie z następującymi zaleceniami: Te maszyny wirtualne są zoptymalizowane pod kątem najnowszego sprzętu i umożliwiają korzystanie z lepszych cen i wyników. 

|Starsza seria VM/rozmiar|Zalecane nowsze serie/rozmiar maszyn wirtualnych|Więcej informacji|
|----------------------|----------------------------|--------------------|
|Seria Av1|[Seria Av2](../av2-series.md)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Seria Dv1 lub DSv1 (Od D1 do D5)|[Seria Dv3 lub DSv3](../dv3-dsv3-series.md)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Seria Dv1 lub DSv1 (Od D11 do D14)|[Seria Ev3 lub ESv3](../ev3-esv3-series.md)|
|D15v2 lub DS15v2|Jeśli używasz modelu wdrażania Menedżera zasobów w celu skorzystania z większych rozmiarów maszyn wirtualnych, należy rozważyć przejście do D16v3/DS16v3 lub D32v3/DS32v3. Są one przeznaczone do pracy na sprzęcie najnowszej generacji. Jeśli używasz modelu wdrażania Menedżera zasobów, aby upewnić się, że wystąpienie maszyny Wirtualnej jest odizolowane od sprzętu dedykowanego jednemu klientowi, należy rozważyć przeniesienie do nowych izolowanych rozmiarów maszyn wirtualnych, E64i_v3 lub E64is_v3, które są przeznaczone do uruchamiania na sprzęcie najnowszej generacji. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Błędy alokacji dla dużych wdrożeń (ponad 500 rdzeni)

Zmniejsz liczbę wystąpień żądanego rozmiaru maszyny Wirtualnej, a następnie ponów próbę wykonania operacji wdrażania. Ponadto w przypadku większych wdrożeń można ocenić [zestawy skalowania maszyny wirtualnej platformy Azure.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) Liczba wystąpień maszyn wirtualnych można automatycznie zwiększyć lub zmniejszyć w odpowiedzi na żądanie lub zdefiniowanego harmonogramu i masz większe szanse na sukces alokacji, ponieważ wdrożenia mogą być rozłożone na wiele klastrów. 

## <a name="background-information"></a>Informacje podstawowe
### <a name="how-allocation-works"></a>Jak działa alokacja
Serwery w centrach danych platformy Azure są partycjonowane na klastry. Przeważnie próba wykonania żądania alokacji jest podejmowana w wielu klastrach, ale istnieje możliwość, że niektóre ograniczenia wynikające z żądania alokacji wymuszają próbę żądania tylko w jednym klastrze platformy Azure. W tym artykule będziemy odnosić się do tego jako "przypięty do klastra." Diagram 1 poniżej ilustruje przypadek normalnej alokacji, która jest podejmowana w wielu klastrach. Diagram 2 ilustruje przypadek alokacji, która jest przypięta do klastra 2, ponieważ w tym miejscu znajduje się istniejący zestaw CS_1 lub dostępności usługi w chmurze.
![Diagram alokacji](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Dlaczego wystąpiły błędy alokacji
Gdy żądanie alokacji jest przypięte do klastra, istnieje większa szansa na nieznalezienie wolnych zasobów, ponieważ dostępna pula zasobów jest mniejsza. Ponadto jeśli żądanie alokacji jest przypięte do klastra, ale typ żądanego zasobu nie jest obsługiwany przez ten klaster, żądanie zakończy się niepowodzeniem, nawet jeśli klaster ma wolne zasoby. Na poniższym diagramie 3 przedstawiono przypadek, w którym przypięty alokacji nie powiedzie się, ponieważ tylko klaster kandydata nie ma wolnych zasobów. Diagram 4 ilustruje przypadek, w którym przypięta alokacja kończy się niepowodzeniem, ponieważ tylko klaster kandydata nie obsługuje żądanego rozmiaru maszyny Wirtualnej, nawet jeśli klaster ma wolne zasoby.

![Niepowodzenie przypiętej alokacji](./media/virtual-machines-common-allocation-failure/Allocation2.png)


