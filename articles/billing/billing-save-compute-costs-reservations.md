---
title: Co to są zastrzeżone wystąpieniach platformy Azure? | Microsoft Docs
description: Informacje o zastrzeżonych wystąpień maszyn wirtualnych Azure i cenach zapisać na kosztów maszyn wirtualnych w celu uzyskania najlepszej skuteczne maszyny Wirtualnej.
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
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: 12fc88596b4283b6f809575328ab801704cc1c8d
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063939"
---
# <a name="what-are-azure-reserved-vm-instances"></a>Co to są zastrzeżone wystąpień maszyn wirtualnych Azure?
[Azure zastrzeżone wystąpień maszyn wirtualnych](https://azure.microsoft.com/pricing/reserved-vm-instances) Użyj pomaga oszczędności przez wstępnie płatności dla jednego roku lub trzech lat wydajności obliczeniowej pozwala uzyskać rabat na maszyny wirtualne. Zarezerwowane wystąpieniach platformy Azure może znacznie obniżyć koszty maszyny wirtualnej — do 72 procent płatności obejmujące cen — z jednego roku lub trzech lat góry zobowiązań. Zarezerwowane wystąpień Podaj rabat rozliczeń i nie ma wpływu na stan czasu wykonywania maszyn wirtualnych.

Zarezerwowane wystąpienia (RI) można kupić [portalu Azure](https://aka.ms/reservations). Aby uzyskać więcej informacji, zobacz [Przedpłata dla maszyn wirtualnych, a następnie zapisz pieniędzy wystąpieniami zastrzeżone](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-instance"></a>Dlaczego warto kupić zastrzeżone wystąpienia?
Jeśli masz maszyny wirtualne, które są uruchamiane przez dłuższy czas zakupu zastrzeżone wystąpienia z opcją najbardziej ekonomiczne. Na przykład po uruchomieniu stale cztery wystąpienia maszyny wirtualnej standardowego D2 regionu zachodnie stany USA, bez zastrzeżone wystąpienia naliczane są opłaty z szybkością. Jeśli kupisz wystąpienia zarezerwowane dla tych cztery maszyny wirtualne, maszyn wirtualnych od razu skorzystać rozliczeń. Są one już naliczane stawkami płatności obejmujące. 

## <a name="what-charges-does-a-reserved-instance-cover"></a>Co opłaty czy okładce zastrzeżone wystąpienia?
Zarezerwowane wystąpienia obejmuje jedynie opłat za infrastrukturę maszyny wirtualnej dla maszyn wirtualnych systemu Windows lub Linux. Zarezerwowane wystąpienia nie obejmuje dodatkowych opłat oprogramowania, sieci lub magazynu. Dla maszyn wirtualnych systemu Windows, mogą obejmować koszty z licencjonowania systemu Windows [korzyści hybrydowego Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-instance"></a>Kto jest uprawniony do zakupu zastrzeżone wystąpienia?
Azure klientów z tych typów subskrypcji można kupić zastrzeżone wystąpienie:
-   Enterprise umowy typu oferty subskrypcji (MS-AZR - 0017P).
-   [Płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/) typu oferty subskrypcji (MS-AZR - 003 P). Musisz być w roli "Właściciela" w subskrypcji kupić zastrzeżone wystąpienia. Do zakupu zastrzeżone wystąpień w rejestracji enterprise, administrator przedsiębiorstwa należy włączyć zakupy wystąpienia zarezerwowane w portalu EA. Domyślnie to ustawienie jest włączone.
-   Cloud Solution Provider (CSP) partnerzy mogą korzystać z portalu Azure lub [Centrum partnerskiego](https://docs.microsoft.com/partner-center/azure-reservations) zakupu wystąpień zastrzeżone.

## <a name="how-is-a-reserved-instance-purchase-billed"></a>Jak jest on rozliczany zakupu zastrzeżone wystąpienia?
Zakupu zastrzeżone wystąpienia jest pobierana do metody płatności, związana z subskrypcji. Jeśli masz subskrypcję korporacyjną koszt zastrzeżone wystąpienia jest odejmowany od Saldo zobowiązań. Jeśli konta zobowiązania pieniężnego nie obejmuje kosztów zastrzeżone wystąpienia, są rozliczane nadwyżkowe.
Jeśli masz subskrypcję płatność za rzeczywiste użycie, natychmiastowe Naliczanie karty kredytowej, które masz na Twoim koncie. Jeśli opłaty są naliczane według faktury, można wyświetlić opłat przy następnej fakturze.

## <a name="how-is-the-purchased-reserved-instance-discount-applied"></a>Sposób stosowania rabat zakupionych zastrzeżone wystąpienia?
Zniżki zastrzeżone wystąpienia stosuje się do maszyn wirtualnych, które jest zgodny z atrybutem, wybrane w przypadku zakupu zastrzeżone wystąpienia. Atrybuty obejmują zakresu, gdzie uruchomić pasującego maszyn wirtualnych. Na przykład jeśli chcesz rabat wystąpienia zastrzeżone dla czterema maszynami wirtualnymi standardowe D2 regionu zachodnie stany USA wybierz subskrypcji, w którym są uruchomione maszyny wirtualne. Jeśli maszyny wirtualne są uruchomione w ramach różnych subskrypcji w ramach rejestracji/konta, wybierz zakres jako udostępniony. Zakres udostępniony umożliwia rabat zastrzeżone wystąpienia mają być stosowane w subskrypcjach. Można zmienić zakres po kupić zastrzeżone wystąpienia. Aby zmienić zakres, zobacz [Zarządzanie wystąpieniami zarezerwowane na platformie Azure](billing-manage-reserved-vm-instance.md).

Zniżki zastrzeżone wystąpienia dotyczy tylko maszyny wirtualne powiązane z enterprise lub płatność za rzeczywiste użycie typów subskrypcji. Maszyn wirtualnych działających w ramach subskrypcji z innych typów oferta nie jest wyświetlany rabat zastrzeżone wystąpienia. Dla rejestracji enterprise subskrypcji i testowania enterprise nie są kwalifikuje się do korzyści wystąpienia zastrzeżone.

Aby lepiej zrozumieć zastrzeżone wystąpienia wpłynie na rozliczenia Twojej maszyny wirtualnej, zobacz [zrozumieć, jak stosowany jest rabat zastrzeżone wystąpienia](billing-understand-vm-reservation-charges.md).

## <a name="what-happens-when-the-reserved-instance-term-expires"></a>Co się stanie po upływie terminu zastrzeżone wystąpienia?
Na końcu termin zastrzeżone wystąpienia rozliczeń rabat wygasa i infrastruktury maszyny wirtualnej jest on rozliczany cenie płatności — jako — możesz Przejdź. Zarezerwowane wystąpieniach platformy Azure nie automatyczne odnawianie. Aby kontynuować pobieranie rabat rozliczeń, musisz kupić nowe wystąpienie zastrzeżone. 

## <a name="sizes-and-regional-availability"></a>Rozmiary i dostępności regionalne
Zarezerwowane wystąpienia są dostępne dla większości rozmiarów maszyn wirtualnych z pewnymi wyjątkami:
- Maszyny wirtualne w wersji zapoznawczej — seria maszyn wirtualnych ani rozmiar, który jest w wersji zapoznawczej nie są dostępne dla wystąpienia zastrzeżone zakupu.
- Chmury — zastrzeżone wystąpień nie są dostępne dla zakupu w regionach Azure instytucji rządowych Stanów Zjednoczonych, Niemczech lub Chinach. 
- Za mało zasobów — zastrzeżone wystąpienia, które są ograniczone do jednego subskrypcji musi mieć vCPU przydziału dostępne w ramach subskrypcji dla nowych RI. Na przykład jeśli subskrypcja docelowa ma limit przydziału Vcpu 10 dla D-Series, następnie nie kupisz zastrzeżone wystąpienia dla wystąpień Standard_D1 11. Wyboru limitu przydziału dla wystąpień zastrzeżone obejmuje maszyn wirtualnych już wdrożona w subskrypcji. Na przykład jeśli subskrypcja ma limit przydziału wynoszący 10 Vcpu dla D-Series, ma dwa wystąpienia standard_D1 wdrożone można kupować zastrzeżone wystąpienia 10 wystąpień standard_D1 w ramach tej subskrypcji. 
- Ograniczenia dotyczące pojemności — w rzadkich przypadkach, Azure limitów rozmiarów zakupu nowego zastrzeżone wystąpień dla podzbioru maszyny wirtualnej, z powodu niskiej wydajności w regionie.

## <a name="next-steps"></a>Kolejne kroki
Początek zapisywania na maszynach wirtualnych przez zakup [wystąpienia zastrzeżone Azure](https://go.microsoft.com/fwlink/?linkid=861721). 

Aby dowiedzieć się więcej o wystąpieniach zastrzeżonych, zobacz następujące artykuły:

- [Co to są zastrzeżone wystąpień maszyn wirtualnych Azure?](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie wystąpieniami zarezerwowane na platformie Azure](billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat zastrzeżone wystąpienia](billing-understand-vm-reservation-charges.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla Twojej subskrypcji z](billing-understand-reserved-instance-usage.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla rejestracji w przedsiębiorstwie](billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania systemu Windows nie jest dołączony do wystąpienia zastrzeżone](billing-reserved-instance-windows-software-costs.md)
- [Zarezerwowane wystąpień w programie partnera Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli masz więcej pytań, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
