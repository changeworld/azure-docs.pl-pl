---
title: Kupić plany SUSE Linux — Azure rezerwacje | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Zapłać z góry za użycie SUSE i zaoszczędzić pieniądze w porównaniu z tego względu koszt zgodnie z rzeczywistym użyciem.
services: virtual-machines-linux
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2018
ms.author: yashar
ms.openlocfilehash: 9c3976a5fa98049de03f2a65b71f1fc927947142
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307879"
---
# <a name="prepay-for-suse-software-plans-from-azure-reservations"></a>Zapłać z góry za plany oprogramowania SUSE z listy zastrzeżeń platformy Azure

Zapłać z góry za użycie SUSE i zaoszczędzić pieniądze w porównaniu z tego względu koszt zgodnie z rzeczywistym użyciem. Zniżki dotyczą tylko w przypadku, SUSE liczniki i nie na użycie maszyny wirtualnej. Możesz kupować rezerwacje dla maszyn wirtualnych zaoszczędzić jeszcze więcej.

Możesz kupić plany oprogramowania SUSE w witrynie Azure portal. Aby kupić plan:

- Musisz być w roli właściciela dla co najmniej jeden Enterprise lub subskrypcję z płatnością za rzeczywiste użycie.
- W przypadku subskrypcji Enterprise, musi być włączony zakupu rezerwacji [portalu EA portal](https://ea.azure.com).
- W przypadku programu Cloud Solution Provider (CSP) agentów administratora lub agentom sprzedaży można kupić plany SUSE.

## <a name="buy-a-suse-software-plan"></a>Kup plan oprogramowania SUSE

1. Przejdź do [rezerwacje](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) w witrynie Azure portal.
1. Wybierz **Dodaj** i wybierz SUSE Linux.
1. Wypełnij wymagane pola. Wszystkie maszyny Wirtualnej systemu Linux SUSE, odpowiadający atrybuty można zakupić pobiera rabat. Rzeczywista liczba wdrożeń, które zawierają Rabat zależy od tego, zakresu i ilość wybrana.

    | Pole      | Opis|
    |:------------|:--------------|
    |Name (Nazwa)        |Nazwa tego zakupu.|
    |Subskrypcja|Subskrypcja użytej do zapłacenia dla tego planu. Metodę płatności dla subskrypcji jest naliczana opłata z góry kosztów rezerwacji. Wymagany typ subskrypcji to Enterprise Agreement (numer oferty MS-AZR-0017P) lub Płatność zgodnie z rzeczywistym użyciem (numer oferty MS-AZR-0003P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty obciążają kartę kredytową lub metodę płatności faktury powiązaną z subskrypcją.|
    |Zakres       |Zakres może obejmować subskrypcji jednej lub wielu subskrypcji (zakres udostępniony). Jeśli wybierzesz: <ul><li>Pojedynczej subskrypcji — rabat w wysokości plan jest stosowany do użycia SUSE Linux w ramach tej subskrypcji. </li><li>W warstwie współdzielona — zastosowania rabatu plan do użycia w systemie SUSE Linux w dowolnej subskrypcji w ramach kontekstu rozliczeń. Dla klientów korporacyjnych zakres udostępniony jest rejestracji i zawiera wszystkie subskrypcje (z wyjątkiem tworzenia i testowania subskrypcje) w ramach rejestracji. Dla klientów rozliczana według bieżącego użycia udostępnionych zakres jest wszystkie subskrypcje płatność za rzeczywiste użycie utworzonego przez administratora konta.</li></ul>|
    |Plan oprogramowania     |Wybierz plan, SUSE Linux. Aby uzyskać pomoc w identyfikacji, co należy kupić, zobacz [zrozumieć, jak jest stosowany rabat związany z rezerwacją SUSE Linux Enterprise software](../../billing/billing-understand-suse-reservation-charges.md).|
    |Rozmiar maszyny wirtualnej     |Cennik SUSE Linux, zależy od liczby procesorów wirtualnych na maszynie Wirtualnej. Wybierz opcję, która reprezentuje liczbę procesorów wirtualnych na maszynach wirtualnych systemu Linux SUSE.|
    |Termin        |Jeden rok lub trzy lata.|
    |Ilość    |Liczba maszyn wirtualnych, które kupujesz ten plan SUSE Linux. Ilość jest liczby uruchomionych wystąpień systemu SUSE Linux, które mogą uzyskać rabat rozliczeń.|
1. Wybierz pozycję **Kup**.
1. Wybierz **Wyświetl tę rezerwację** Aby wyświetlić stan zakupu.

Rabat związany z rezerwacją jest automatycznie stosowany do dowolnego SUSE maszyn wirtualnych, które odpowiada rezerwacji. Rabat dotyczy tylko miernika SUSE. Opłaty za zasoby obliczeniowe maszyny Wirtualnej nie są objęte tego planu.

## <a name="discount-applies-to-different-vm-sizes-with-instance-size-flexibility"></a>Rabat ma zastosowanie do różnych rozmiarów maszyn wirtualnych za pomocą elastyczność rozmiaru wystąpienia

Jak zarezerwowanych wystąpień maszyn wirtualnych systemu SUSE Linux plany miesięczne oferują elastyczność rozmiaru wystąpienia. Oznacza to, że ma zastosowanie rabat, nawet podczas wdrażania maszyny Wirtualnej, który jest inny rozmiar z planu SUSE, których używasz. Aby uzyskać więcej informacji, zobacz [zrozumieć, jak jest stosowany rabat związany z rezerwacją SUSE Linux Enterprise software](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="cancellation-and-exchanges-not-allowed"></a>Anulowanie i wymiany niedozwolone

Nie można anulować lub wymiany plan SUSE, którego zakupiono. Sprawdź wykorzystanie, aby upewnić się, że kupić właściwego planu. Aby uzyskać pomoc w identyfikacji, co należy kupić, zobacz [zrozumieć, jak jest stosowany rabat związany z rezerwacją SUSE Linux Enterprise software](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak zarządzać rezerwacji, zobacz [rezerwacje Azure zarządzać](../../billing/billing-manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej, zobacz następujące artykuły:

- [Co to jest Azure rezerwacje?](../../billing/billing-save-compute-costs-reservations.md)
- [Zarządzanie zastrzeżeniami na platformie Azure](../../billing/billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat związany z rezerwacją SUSE](../../billing/billing-understand-suse-reservation-charges.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](../../billing/billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal masz dodatkowe pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.