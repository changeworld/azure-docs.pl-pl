---
title: Przedpłata za plany oprogramowania — Azure Reservations
description: Dowiedz się, jak możesz przednieść za plany oprogramowania, aby zaoszczędzić pieniądze w porównaniu z rzeczywistym użyciem.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: e4cab8963e3083d265d0254c81b78fbabb84a269
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091538"
---
# <a name="prepay-for-azure-software-plans"></a>Opłacanie planów oprogramowania platformy Azure z góry

Gdy płacisz za korzystanie z oprogramowania SUSE i RedHat na platformie Azure, możesz zaoszczędzić pieniądze w kosztach związanych z płatność zgodnie z rzeczywistym użyciem. Zniżki dotyczą tylko zasobów SUSE i RedHat, a nie do użycia maszyn wirtualnych. Rezerwacje dla maszyn wirtualnych można zakupić osobno w celu uzyskania dodatkowych oszczędności.

W Azure Portal można kupić plany oprogramowania SUSE i RedHat. Aby kupić plan:

- Użytkownik musi mieć rolę właściciela dla co najmniej jednego przedsiębiorstwa lub subskrypcji indywidualnej z cennikiem z opcją płatność zgodnie z rzeczywistym użyciem.
- W przypadku subskrypcji przedsiębiorstwa w [portalu EA](https://ea.azure.com/)należy włączyć opcję **Dodaj wystąpienia zarezerwowane** . Jeśli to ustawienie jest wyłączone, musisz być administratorem EA dla subskrypcji.
- W przypadku programu Cloud Solution Provider (CSP) agenci administracyjni lub agenci sprzedaży mogą kupić plany oprogramowania.

## <a name="buy-a-software-plan"></a>Kup plan oprogramowania

1. Zaloguj się do Azure Portal i przejdź do pozycji [rezerwacje](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Kliknij przycisk **Dodaj** , a następnie wybierz plan oprogramowania, który chcesz kupić.
Wypełnij pola wymagane. Wszystkie maszyny wirtualne lub maszyny wirtualne z systemem SUSE Linux, które pasują do posiadanych przez Ciebie atrybutów, pobierają rabat. Rzeczywista liczba wdrożeń, które pobiera rabat, zależy od wybranego zakresu i ilości.
3. Wybierz subskrypcję. Jest on używany do płacenia za plan.
Metoda płatności za subskrypcję jest naliczana za koszty związane z pozostałymi kosztami rezerwacji. Typ subskrypcji musi być Umowa Enterprise (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualna umowa z cennikiem z opcją płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P).
    - W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka.
    - W przypadku indywidualnej subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury.
4. Wybierz zakres. Zakres może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony).
    - Pojedyncza subskrypcja — rabat planu jest stosowany do dopasowania użycia w subskrypcji.
    - Udostępnione — rabat planu jest stosowany do zgodnych wystąpień w dowolnej subskrypcji w kontekście rozliczania. W przypadku klientów korporacyjnych kontekst rozliczeń to Rejestracja i zawiera wszystkie subskrypcje rejestracji. W przypadku pojedynczego planu z klientami z płatnością zgodnie z rzeczywistym użyciem kontekst rozliczeń to wszystkie plany z subskrypcją z płatnością zgodnie z rzeczywistym użyciem utworzoną przez administratora konta.
5. Wybierz produkt, aby wybrać rozmiar maszyny wirtualnej i typ obrazu. Rabat ma zastosowanie tylko do wybranego rozmiaru maszyny wirtualnej.
6. Wybierz roczny lub trzyletni okres.
7. Wybierz liczbę, która jest liczbą wystąpień maszyn wirtualnych przedpłaty, które mogą uzyskać rabat rozliczeń.
8. Dodaj produkt do koszyka, przejrzyj i Kup.

Rabat związany z rezerwacją jest automatycznie stosowany do miernika oprogramowania, dla którego użytkownik jest wstępnie obciążany. Opłaty za obliczenia maszyn wirtualnych nie są objęte planem. Rezerwacje maszyn wirtualnych można zakupić osobno.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Rabat dotyczy różnych rozmiarów maszyn wirtualnych SUSE

Podobnie jak w przypadku zarezerwowanych wystąpień maszyn wirtualnych, plany systemu SUSE Linux oferują elastyczność rozmiaru wystąpienia. Zniżka obowiązuje nawet w przypadku wdrożenia maszyny wirtualnej o innym rozmiarze niż zakupiony plan SUSE. Aby uzyskać więcej informacji, zobacz [Opis sposobu stosowania rabatu planu oprogramowania](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>Rabat dla planu RedHat

Plany są dostępne tylko dla Red Hat Enterprise Linux maszyn wirtualnych. Rabat nie ma zastosowania do maszyn wirtualnych RedHat Enterprise Linux SAP HANA lub RedHat Enterprise Linux SAP Business Apps.

Rabaty planu RedHat dotyczą tylko rozmiaru maszyny wirtualnej, który wybrano w momencie zakupu. Po zakupie nie można przeprowadzić zwrotów ani wymieniać planów RHEL.


## <a name="cancellation-and-exchanges-not-allowed"></a>Anulowanie i zamiany są niedozwolone

Nie można anulować ani wymienić zakupionego planu SUSE lub RedHat. Sprawdź użycie, aby upewnić się, że zakupiony plan jest właściwy. Aby uzyskać pomoc dotyczącą określania, co należy kupić, zobacz [Opis sposobu stosowania rabatu planu oprogramowania](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zarządzać rezerwacją, zobacz [Zarządzanie rezerwacjami platformy Azure](../../billing/billing-manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej, zobacz następujące artykuły:

- [Co to są Azure Reservations?](../../billing/billing-save-compute-costs-reservations.md)
- [Zarządzanie rezerwacjami na platformie Azure](../../billing/billing-manage-reserved-vm-instance.md)
- [Informacje o sposobie stosowania rabatu w ramach rezerwacji SUSE](../../billing/billing-understand-suse-reservation-charges.md)
- [Informacje na temat użycia rezerwacji dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](../../billing/billing-understand-reserved-instance-usage.md)
- [Informacje na temat użycia rezerwacji na potrzeby rejestracji w przedsiębiorstwie](../../billing/billing-understand-reserved-instance-usage-ea.md)
