---
title: Kupić plany Red Hat Enterprise Linux — Azure rezerwacje | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Zapłać z góry za użycie firmy Red Hat i zaoszczędzić pieniądze w porównaniu z tego względu koszt zgodnie z rzeczywistym użyciem.
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
ms.date: 01/18/2019
ms.author: yashar
ms.openlocfilehash: e90241d9da140ca33d1aae8ffc5636f8eb2fbc74
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653020"
---
# <a name="prepay-for-red-hat-enterprise-linux-plans-from-azure-reservations"></a>Zapłać z góry za plany Red Hat Enterprise Linux z listy zastrzeżeń platformy Azure

Zapłać z góry za użycie firmy Red Hat i zaoszczędzić pieniądze w porównaniu z tego względu koszt zgodnie z rzeczywistym użyciem. Zniżki dotyczą tylko w przypadku, liczniki firmy Red Hat i nie na użycie maszyny wirtualnej. Możesz kupować rezerwacje dla maszyn wirtualnych zaoszczędzić jeszcze więcej.

Możesz kupić plany oprogramowania Red Hat na platformie Azure. Aby kupić plan:

- Musisz być w roli właściciela dla co najmniej jeden Enterprise lub subskrypcję z płatnością za rzeczywiste użycie.
- W przypadku subskrypcji Enterprise **Dodawanie wystąpień zarezerwowanych** musi być włączona w [portalu EA portal](https://ea.azure.com). Lub, jeśli to ustawienie jest wyłączone, musi być administratorem subskrypcji umowy EA.
- W przypadku programu Cloud Solution Provider (CSP) agentów administratora lub agentom sprzedaży można kupić plany firmy Red Hat.

## <a name="buy-a-red-hat-software-plan"></a>Kup plan oprogramowania Red Hat

1. Przejdź do [rezerwacje](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) w witrynie Azure portal.
1. Wybierz **Dodaj** i wybierz w systemie Red Hat Linux.
1. Wypełnij wymagane pola. Dowolnej Red Hat Linux maszyny Wirtualnej odpowiadający atrybuty można zakupić pobiera rabat. Rzeczywista liczba wdrożeń, które zawierają Rabat zależy od tego, zakresu i ilość wybrana.

    | Pole      | Opis|
    |:------------|:--------------|
    |Name (Nazwa)        |Nazwa tego zakupu.|
    |Subskrypcja|Subskrypcja użytej do zapłacenia dla tego planu. Metodę płatności dla subskrypcji jest naliczana opłata z góry kosztów rezerwacji. Typ subskrypcji musi być umowy enterprise agreement (oferują liczb: MS-AZR-0017P lub MS-AZR - 0148 P) lub płatność za rzeczywiste użycie (oferują liczb: MS-AZR-0003P lub MS-AZR - 0023 P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty obciążają kartę kredytową lub metodę płatności faktury powiązaną z subskrypcją.|
    |Zakres       |Zakres może obejmować subskrypcji jednej lub wielu subskrypcji (zakres udostępniony). Jeśli wybierzesz: <ul><li>Pojedynczej subskrypcji — rabat w wysokości plan jest stosowany do użycia w systemie Red Hat Linux w ramach tej subskrypcji. </li><li>W warstwie współdzielona — zastosowania rabatu plan do użycia w systemie Red Hat Linux w dowolnej subskrypcji w ramach kontekstu rozliczeń. Dla klientów korporacyjnych zakres udostępniony jest rejestracji i zawiera wszystkie subskrypcje w ramach rejestracji. Dla klientów rozliczana według bieżącego użycia udostępnionych zakres jest wszystkie subskrypcje płatność za rzeczywiste użycie utworzonego przez administratora konta.</li></ul>|
    |Planowanie     |Wybierz plan, Red Hat Linux. Aby uzyskać pomoc w identyfikacji, co należy kupić Zobacz Omówienie, jak jest stosowany rabat związany z rezerwacją Red Hat Linux Enterprise oprogramowania.|
    |Rozmiar maszyny wirtualnej     |Ceny w systemie Red Hat Linux zależy od liczby procesorów wirtualnych na maszynie Wirtualnej. Wybierz opcję, która reprezentuje liczbę procesorów wirtualnych na maszynach wirtualnych Red Hat Linux.|
    |Termin        |Jeden rok lub trzy lata.|
    |Ilość    |Liczba maszyn wirtualnych, które kupujesz ten plan w systemie Red Hat Linux. Ilość jest liczby uruchomionych wystąpień w systemie Red Hat Linux, które mogą uzyskać rabat rozliczeń.|
1. Wybierz pozycję **Kup**.
1. Wybierz **Wyświetl tę rezerwację** Aby wyświetlić stan zakupu.

Rabat związany z rezerwacją jest automatycznie stosowany do dowolnego firmy Red Hat maszyn wirtualnych, które odpowiada rezerwacji. Rabat dotyczy tylko mierników firmy Red Hat. Opłaty za zasoby obliczeniowe maszyny Wirtualnej nie są objęte tego planu.

## <a name="discount-applies-to-different-vm-sizes"></a>Rabat ma zastosowanie do różnych rozmiarów maszyn wirtualnych

Podobnie jak zarezerwowanych wystąpień maszyn wirtualnych Red Hat Linux plany oferuje elastyczność rozmiaru wystąpienia. Oznacza to, że ma zastosowanie rabat, nawet podczas wdrażania maszyny Wirtualnej, który jest inny rozmiar z planu firmy Red Hat, których używasz. Aby uzyskać więcej informacji zobacz omówienie, jak jest stosowany rabat związany z rezerwacją Red Hat Linux Enterprise oprogramowania.

## <a name="cancellation-and-exchanges-not-allowed"></a>Anulowanie i wymiany niedozwolone

Nie można anulować lub wymiany plan firmy Red Hat, którego zakupiono. Sprawdź użycie, aby upewnić się, że zakupiony plan jest właściwy. Aby uzyskać pomoc w identyfikacji, co należy kupić Zobacz Omówienie, jak jest stosowany rabat związany z rezerwacją Red Hat Linux Enterprise oprogramowania.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak zarządzać rezerwacji, zobacz [rezerwacje Azure zarządzać](../../billing/billing-manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej, zobacz następujące artykuły:

- [Co to jest Azure rezerwacje?](../../billing/billing-save-compute-costs-reservations.md)
- [Zarządzanie zastrzeżeniami na platformie Azure](../../billing/billing-manage-reserved-vm-instance.md)
- Zrozumienie, jak stosowany jest rabat związany z rezerwacją firmy Red Hat
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](../../billing/billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal masz dodatkowe pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.