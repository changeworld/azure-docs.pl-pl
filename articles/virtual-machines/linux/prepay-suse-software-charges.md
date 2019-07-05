---
title: Zapłać z góry za plany oprogramowania - rezerwacje platformy Azure
description: Dowiedz się, jak wpłacenia zaliczki dla plany oprogramowania zaoszczędzić pieniądze w porównaniu z tego względu koszt zgodnie z rzeczywistym użyciem.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: c8e9d07bf01536d7865bd79f667a937037d96837
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67489879"
---
# <a name="prepay-for-azure-software-plans"></a>Opłacanie planów oprogramowania platformy Azure z góry

Gdy wpłacenia zaliczki użycia oprogramowania SUSE a RedHat na platformie Azure możesz zaoszczędzić pieniądze w porównaniu z tego względu koszt zgodnie z rzeczywistym użyciem. Zniżki dotyczą tylko w przypadku, SUSE i RedHat i nie na użycie maszyny wirtualnej. Możesz kupować rezerwacje w przypadku maszyn wirtualnych niezależnie w celu uzyskania dodatkowych oszczędności.

Możesz kupić plany oprogramowania SUSE i RedHat w witrynie Azure portal. Aby kupić plan:

- Konieczne jest posiadanie roli właściciela dla co najmniej jeden Enterprise lub poszczególnych subskrypcji przy użyciu zgodnie z rzeczywistym użyciem.
- W przypadku subskrypcji Enterprise **Dodawanie wystąpień zarezerwowanych** należy włączyć opcję [portalu EA portal](https://ea.azure.com/). Jeśli ustawienie jest wyłączone, musi być administratorem dla subskrypcji EA.
- W przypadku programu Cloud Solution Provider (CSP) agentów administratora lub agentom sprzedaży mogą kupować plany oprogramowania.

## <a name="buy-a-software-plan"></a>Kup plan oprogramowania

1. Zaloguj się do witryny Azure portal i przejdź do [rezerwacje](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Kliknij przycisk **Dodaj** i następnie wybierz plan oprogramowania, który chcesz kupić.
Wypełnij wymagane pola. Maszyny Wirtualnej z systemem SUSE Linux dowolnej maszyny Wirtualnej z systemem RedHat, odpowiadający atrybuty można zakupić pobiera rabat. Rzeczywista liczba wdrożeń, które zawierają Rabat zależy od tego, zakresu i ilość wybrana.
3. Wybierz subskrypcję. Służy do zapłacenia za plan.
Metoda płatności subskrypcji jest naliczana opłata z góry kosztów rezerwacji. Typ subskrypcji musi być umowy Enterprise Agreement (oferują liczb: MS-AZR-0017P lub MS-AZR - 0148 P) lub poszczególnych umowy z zgodnie z rzeczywistym użyciem (oferują liczb: MS-AZR-0003P lub MS-AZR-0023P).
    - W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka.
    - Dla poszczególnych subskrypcji zgodnie z rzeczywistym użyciem, cennik opłaty są naliczane do metody płatności karty kredytowej lub faktury dla subskrypcji.
4. Wybierz zakres. Zakres może obejmować subskrypcji jednej lub wielu subskrypcji (zakres udostępniony).
    - Pojedynczej subskrypcji — rabat w wysokości plan jest stosowany do pasujących użycie w ramach subskrypcji.
    - Czy rabat w wysokości plan w warstwie współdzielona — jest stosowany do pasujących wystąpień w dowolnej subskrypcji w kontekstu rozliczeń. Dla klientów korporacyjnych kontekstu rozliczeń jest rejestracji i zawiera wszystkie subskrypcje w rejestracji. Dla poszczególnych planu cen klientom zgodnie z rzeczywistym użyciem kontekstu rozliczeń jest planem poszczególnych zgodnie z rzeczywistym użyciem cen subskrypcji utworzonych przez administratora konta.
5. Wybierz produkt, aby wybrać rozmiar maszyny Wirtualnej i typ obrazu. Rabat dotyczy tylko wybrany rozmiar maszyny Wirtualnej.
6. Wybierz termin jeden rok lub trzy lata.
7. Wybierz ilość, czyli liczbę przedpłacone wystąpień maszyn wirtualnych, które mogą uzyskać rabat rozliczeń.
8. Dodaj produkt do koszyka, przejrzyj i zakupu.

Rabat związany z rezerwacją jest automatycznie stosowany do skali oprogramowania, w którym możesz Zapłać z góry za. Opłaty za zasoby obliczeniowe maszyny Wirtualnej nie są objęte planu. Rezerwacji maszyny Wirtualnej można zakupić osobno.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Rabat ma zastosowanie do różnych rozmiarów maszyn wirtualnych z systemem SUSE

Jak zarezerwowanych wystąpień maszyn wirtualnych systemu SUSE Linux plany miesięczne oferują elastyczność rozmiaru wystąpienia. Rabat ma zastosowanie, nawet wtedy, gdy wdrożysz maszynę Wirtualną, która ma inny rozmiar z planu SUSE, których używasz. Aby uzyskać więcej informacji, zobacz [zrozumieć, jak jest stosowany rabat w wysokości plan oprogramowania](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>RedHat plan rabatu

Plany są dostępne tylko dla maszyn wirtualnych Red Hat Enterprise Linux. Ten rabat nie ma zastosowania do maszyn wirtualnych RedHat Enterprise Linux SAP HANA lub maszyn wirtualnych z RedHat Enterprise Linux SAP Business Apps.

Rabaty w ramach planu RedHat dotyczą tylko rozmiar maszyny Wirtualnej możesz wybrać w momencie zakupu. RHEL planów nie może być zwrócona ani wymieniane po zakupie.


## <a name="cancellation-and-exchanges-not-allowed"></a>Anulowanie i wymiany niedozwolone

Nie można anulować lub wymiany SUSE lub RedHat plan, którego zakupiono. Sprawdź użycie, aby upewnić się, że zakupiony plan jest właściwy. Aby uzyskać pomoc, aby określić, co można kupić, zobacz [zrozumieć, jak jest stosowany rabat w wysokości plan oprogramowania](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak zarządzać rezerwacji, zobacz [rezerwacje Azure zarządzać](../../billing/billing-manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej, zobacz następujące artykuły:

- [Co to jest Azure rezerwacje?](../../billing/billing-save-compute-costs-reservations.md)
- [Zarządzanie zastrzeżeniami na platformie Azure](../../billing/billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat związany z rezerwacją SUSE](../../billing/billing-understand-suse-reservation-charges.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](../../billing/billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](../../billing/billing-understand-reserved-instance-usage-ea.md)
