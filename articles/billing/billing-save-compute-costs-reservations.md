---
title: Zaoszczędzić na wstępnie płatności maszyn wirtualnych platformy Azure — Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zastrzeżone wystąpienie maszyny wirtualnej Azure do zapisywania na kosztów maszyn wirtualnych.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: yashar
ms.openlocfilehash: 9c73102f09e252b449f78603debaf707b3c89c3b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="save-money-on-virtual-machines-with-reserved-virtual-machine-instances"></a>Zaoszczędzić na maszynach wirtualnych z zarezerwowanych wystąpień maszyn wirtualnych 
Zarezerwowane wystąpień maszyn wirtualnych umożliwiają wstępnie opłacać jednego roku lub trzech lat wydajności obliczeniowej, aby uzyskać rabat na maszyny wirtualne, których używasz. Znacznie zmniejsza koszty maszyny wirtualnej — do 72 procent płatności obejmujące cen — z jednego roku lub trzech lat góry zobowiązań. Zarezerwowane wystąpień maszyn wirtualnych jest rabat rozliczeń i nie ma wpływu na stan czasu wykonywania maszyn wirtualnych.

Zarezerwowane wystąpienie maszyny wirtualnej można kupić [portalu Azure](https://aka.ms/reservations). Aby uzyskać więcej informacji, zobacz [Przedpłata dla maszyn wirtualnych, a następnie zapisz pieniędzy Virtual Machine wystąpieniami zastrzeżone](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-virtual-machine-instance"></a>Dlaczego należy kupić zastrzeżone wystąpienie maszyny wirtualnej?
Jeśli masz maszyny wirtualne, które są uruchamiane przez dłuższy czas zakupu zastrzeżone wystąpienie maszyny wirtualnej umożliwia najlepszej skuteczne. Na przykład po uruchomieniu stale cztery wystąpienia standardowe D2 regionu zachodnie stany USA, bez zastrzeżenie naliczane są opłaty płatności — jako Przejdź — możesz stawkami. Jeśli kupisz zastrzeżone wystąpienie maszyny wirtualnej dla tych czterech maszyn wirtualnych, maszyny wirtualne natychmiast skorzystać rozliczeń. Są one już naliczane ze stawkami płatności — jako — możesz Przejdź. 

## <a name="what-charges-does-a-reserved-virtual-machine-instance-cover"></a>Jakie opłaty obejmuje zastrzeżone wystąpienie maszyny wirtualnej
Zastrzeżenie obejmuje jedynie opłat za infrastrukturę maszyny wirtualnej dla maszyn wirtualnych systemu Windows lub Linux. Rezerwacja nie obejmuje dodatkowych opłat oprogramowania, sieci lub magazynu. Dla maszyn wirtualnych systemu Windows, mogą obejmować koszty z licencjonowania systemu Windows [korzyści hybrydowego Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-virtual-machine-instance"></a>Kto jest uprawniony do zakupu zastrzeżone wystąpienie maszyny wirtualnej?
Azure klienci z tych typów subskrypcji można zakupić zastrzeżone wystąpienie maszyny wirtualnej:
-   Enterprise umowy typu oferty subskrypcji (MS-AZR - 0017P).
-   [Płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/) typu oferty subskrypcji (MS-AZR - 003 P).
Musisz być w roli "Właściciela" w subskrypcji kupić zastrzeżone wystąpienia. Do zakupu rezerwacji w rejestracji enterprise, administrator przedsiębiorstwa należy włączyć rezerwacji zakupy w portalu EA domyślnie to ustawienie jest włączone.
-   Cloud Solution Provider (CSP) partnerzy mogą korzystać z portalu zarządzania Azure lub [Centrum partnerskiego](https://docs.microsoft.com/partner-center/azure-reservations) zakupu rezerwacji.

## <a name="how-is-a-reserved-virtual-machine-instances-purchase-billed"></a>Jak jest rozliczany zakupu zastrzeżone wystąpień maszyn wirtualnych
Zakupu rezerwacji jest pobierana do metody płatności, związana z subskrypcji. Jeśli masz subskrypcję korporacyjną koszt rezerwacji jest odejmowany od Saldo zobowiązań. Jeśli konta zobowiązania pieniężnego nie obejmuje kosztów zastrzeżenia, są rozliczane nadwyżkowe.
Jeśli masz subskrypcję płatność za rzeczywiste użycie, natychmiastowe Naliczanie karty kredytowej, które masz na Twoim koncie. Jeśli opłaty są naliczane według faktury, można wyświetlić opłat przy następnej fakturze.

## <a name="how-is-the-purchased-reserved-virtual-machine-instance-discount-applied"></a>Sposób stosowania zakupionych rabat zastrzeżone wystąpienie maszyny wirtualnej?
Zniżki zastrzeżone wystąpienie maszyny wirtualnej ma zastosowanie do maszyn wirtualnych, które jest zgodny z atrybutem, wybrane w przypadku zakupu rezerwacji. Atrybuty obejmują zakresu, gdzie uruchomić pasującego maszyn wirtualnych. Na przykład jeśli chcesz rabat zastrzeżone wystąpienia maszyny Wirtualnej dla czterema maszynami wirtualnymi standardowe D2 regionu zachodnie stany USA wybierz subskrypcji, w którym są uruchomione maszyny wirtualne. Jeśli maszyny wirtualne są uruchomione w ramach różnych subskrypcji w ramach rejestracji/konta, wybierz zakres jako udostępniony. Zakres udostępniony umożliwia rabat zastrzeżenie ma zostać zastosowany w subskrypcjach.
Po kupić zastrzeżone wystąpienia maszyny Wirtualnej, można zmienić zakres. Aby zmienić zakres, w dokumentacji na temat zarządzania rezerwacji.

Zniżki rezerwacji ma zastosowanie tylko do maszyn wirtualnych w ramach subskrypcji enterprise lub typy z ofertą. Maszyn wirtualnych działających w ramach subskrypcji z innych typów oferty nie otrzymują rabaty rezerwacji. Dla rejestracji enterprise subskrypcji i testowania enterprise nie są kwalifikuje się do korzyści wystąpienia zastrzeżone.

Wpływ rezerwacji rozliczeń maszyny wirtualnej znajduje się w [opis aplikacji rezerwacji rozliczeń korzyści](https://go.microsoft.com/fwlink/?linkid=863405).

## <a name="what-happens-when-the-reservation-term-expires"></a>Co się stanie po upływie terminu rezerwacji?
Na końcu termin rezerwacji wygasa rabat rozliczeń i infrastruktury maszyny wirtualnej jest on rozliczany cenie płatności — jako — możesz Przejdź. Rezerwacji nie automatyczne odnawianie. Aby kontynuować pobieranie rabat rozliczeń, musisz kupić zastrzeżone nowe wystąpienie maszyny wirtualnej. 

## <a name="sizes-and-regional-availability"></a>Rozmiary i dostępności regionalne
Zastrzeżenia są dostępne dla większości rozmiarów maszyn wirtualnych z pewnymi wyjątkami:
- Rozmiarów maszyn wirtualnych w wersji zapoznawczej — dowolnej wielkości, która jest w wersji zapoznawczej nie są dostępne do zakupu zastrzeżone wystąpienie maszyny wirtualnej.
- Chmury — nie są dostępne dla zakupu w regionach Azure instytucji rządowych Stanów Zjednoczonych, Niemczech lub Chin zastrzeżone wystąpień maszyn wirtualnych. 
- Przydział jest niewystarczający — zastrzeżone wirtualna wystąpienie które są ograniczone do jednego subskrypcji musi być dostępne w ramach subskrypcji dla nowych RI przydziału vCPU. Na przykład jeśli subskrypcja docelowa ma limit przydziału równy 10 Vcpu dla rodziny D-Series, następnie nie kupisz zastrzeżone wystąpienia maszyny Wirtualnej dla wystąpień Standard_D1 11. Sprawdzanie przydziału zastrzeżenia obejmuje maszyn wirtualnych już wdrożona w subskrypcji. Na przykład, jeśli subskrypcja ma limit przydziału wynoszący 10 Vcpu D-Series rodziny. Ta subskrypcja ma dwa wystąpienia standard_D1 wdrożone, można kupić wystąpienia zastrzeżone maszyny Wirtualnej do 10 wystąpień standard_D1 w ramach tej subskrypcji. 
- Ograniczenia pojemności — w rzadkich przypadkach, limity Azure zakupu nowego zastrzeżenia dla podzbioru rozmiarów maszyn wirtualnych z powodu niskiej wydajności w regionie.

## <a name="next-steps"></a>Następne kroki
Początek zapisywania na maszynach wirtualnych przez zakup [zastrzeżone wystąpienie maszyny wirtualnej](https://go.microsoft.com/fwlink/?linkid=861721). 

Aby dowiedzieć się więcej o zastrzeżonych wystąpień maszyn wirtualnych, zobacz następujące artykuły.

- [Przedpłaty dla maszyn wirtualnych z wystąpień zastrzeżone maszyny Wirtualnej](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie wystąpieniami zastrzeżonej maszyny wirtualnej](billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat zastrzeżone wystąpienie maszyny wirtualnej](billing-understand-vm-reservation-charges.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla Twojej subskrypcji z](billing-understand-reserved-instance-usage.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla rejestracji w przedsiębiorstwie](billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania systemu Windows nie jest dołączony do wystąpienia zastrzeżone](billing-reserved-instance-windows-software-costs.md)
- [Zarezerwowane wystąpień w programie partnera Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną
Jeśli potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
