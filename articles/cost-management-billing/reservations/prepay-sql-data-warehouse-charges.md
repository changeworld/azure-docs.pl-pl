---
title: Oszczędzaj przy SQL Data Warehouse naliczanie opłat za pomocą zarezerwowanej pojemności platformy Azure
description: Dowiedz się, w jaki sposób zaoszczędzić na opłatach za usługę SQL Data Warehouse w ramach pojemności zarezerwowanej.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: b4069c9b18f9591e79d983a1317f00df11cf0611
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995847"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Zmniejszenie opłat za usługę SQL Data Warehouse dzięki zarezerwowanej pojemności

Możesz oszczędzać pieniądze na usłudze Azure SQL Data Warehouse, zobowiązując się do użycia zarezerwowanych jednostek cDWU przez okres roku lub trzech lat. Aby kupić zarezerwowaną pojemność usługi SQL Data Warehouse, musisz wybrać region świadczenia usługi Azure oraz okres. Następnie musisz dodać jednostkę SKU usługi SQL Data Warehouse do koszyka i wybrać liczbę jednostek cDWU do kupienia.

Gdy wykupisz rezerwację, za użycie usługi SQL Data Warehouse zgodne z atrybutami rezerwacji nie będą naliczane opłaty po stawkach płatności zgodnie z rzeczywistym użyciem.

Rezerwacja nie pokrywa kosztów magazynu i sieci wynikających z użycia usługi SQL Data Warehouse.

Gdy zarezerwowana pojemność wygaśnie, wystąpienia usługi SQL Data Warehouse dalej będą działać, ale będą za nie naliczane opłaty po stawkach płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są odnawiane automatycznie.

Aby uzyskać informacje o cenach, zobacz [ofertę dotyczącą zarezerwowanej pojemności usługi SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Zarezerwowaną pojemność usługi Azure SQL Data Warehouse można kupić w witrynie [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](monthly-payments-reservations.md). Aby kupić pojemność zarezerwowaną:

- Musisz mieć rolę właściciela dla co najmniej jednej subskrypcji Enterprise lub z opcją płatności zgodnie z rzeczywistym użyciem.
- W przypadku subskrypcji Enterprise w witrynie [EA portal](https://ea.azure.com/) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Jeśli to ustawienie jest wyłączone, musisz być administratorem EA.
- W przypadku programu Cloud Solution Provider (CSP) wydajność rezerwową usługi SQL Data Warehouse mogą kupić tylko agenci administracyjni lub agenci sprzedaży.

Aby uzyskać więcej informacji na temat sposobu, w jaki klienci korzystający z umowy Enterprise i klienci z opcją płatności zgodnie z rzeczywistym użyciem są obciążani za zakupy rezerwacji, zobacz [opis użycia rezerwacji platformy Azure w przypadku rejestracji Enterprise](understand-reserved-instance-usage-ea.md) i [opis użycia rezerwacji platformy Azure w przypadku subskrypcji z opcją płatności zgodnie z rzeczywistym użyciem](understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Wybieranie odpowiedniego rozmiaru przed zakupem

Rozmiar rezerwacji usługi SQL Data Warehouse powinien opierać się na całkowitej liczbie zużywanych obliczeniowych jednostek magazynu danych (cDWU). Kupuje się wielokrotność ilości 100 cDWU.

Załóżmy na przykład, że całkowite zużycie usługi SQL Data Warehouse jest na poziomie DW3000c. Chcesz zakupić zarezerwowaną pojemność dla całego zużycia. W związku z tym należy zakupić 30 jednostek zarezerwowanej pojemności cDWU.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Kupowanie zarezerwowanej pojemności usługi SQL Data Warehouse

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).
2. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.
3. Wybierz subskrypcję. Z listy Subskrypcja wybierz subskrypcję, w ramach której jest opłacana zarezerwowana pojemność. Kosztami wydajności rezerwowej jest obciążana forma płatności subskrypcji. Typ subskrypcji musi być umową Enterprise Agreement (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) lub płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P).
   - W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka.
   - W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty obciążają kartę kredytową lub metodę płatności faktury powiązaną z subskrypcją.
4. Wybierz zakres. Użyj listy Zakres w celu wybrania zakresu subskrypcji.
   - **Zakres pojedynczej grupy zasobów** — rabat na rezerwację jest stosowany do odpowiednich zasobów tylko w wybranej grupie zasobów.
   - **Zakres pojedynczej subskrypcji** — rabat na rezerwację jest stosowany do odpowiednich zasobów w wybranej subskrypcji.
   - **Zakres udostępniony** — rabat na rezerwację jest stosowany do odpowiednich zasobów w kwalifikujących się subskrypcjach w ramach kontekstu rozliczeń. W przypadku klientów korzystających z umowy Enterprise Agreement kontekstem rozliczeń jest rejestracja. W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.
   - W przypadku klientów z subskrypcją Enterprise kontekstem rozliczeń jest rejestracja umowy EA.
   - W przypadku klientów z płatnością zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie subskrypcje z opcją płatności zgodnie z rzeczywistym użyciem utworzone przez administratora konta.
5. Wybierz region, aby wskazać region świadczenia usługi Azure, którego ma dotyczyć zarezerwowana pojemność.
6. Wybierz ilość. Wprowadź liczbę wskazującą wielokrotność 100 jednostek magazynu danych (cDWU), którą chcesz kupić.    
   Na przykład liczba 30 zapewni pojemność zarezerwowaną wynoszącą 3000 jednostek cDWU w każdej godzinie.
7. Zapoznaj się z kosztem rezerwacji zarezerwowanej pojemności usługi SQL Data Warehouse w sekcji **Koszty**.
8. Wybierz pozycję **Kup**.
9. Wybierz pozycję **Wyświetl tę rezerwację**, aby wyświetlić stan zakupu.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

Rabat na rezerwację jest automatycznie stosowany do tych wystąpień usługi SQL Data Warehouse, które pasują do zakresu i regionu zarezerwowanej pojemności usługi SQL Data Warehouse. Zakres zarezerwowanej pojemności usługi SQL Data Warehouse można zaktualizować za pomocą witryny [Azure Portal](https://portal.azure.com/), programu PowerShell, interfejsu wiersza polecenia lub interfejsu API.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat sposobu stosowania rabatów na rezerwację do usługi Azure SQL Data Warehouse, zobacz artykuł [Stosowanie rabatów na rezerwacje usługi Azure SQL Data Warehouse](prepay-sql-data-warehouse-charges.md).

- Aby dowiedzieć się więcej na temat usługi Azure Reservations, zobacz następujące artykuły:
  - [Co to jest Azure Reservations?](save-compute-costs-reservations.md)
  - [Zarządzanie usługą Azure Reservations](manage-reserved-vm-instance.md)
  - [Informacje na temat rabatu na rezerwacje platformy Azure](understand-reservation-charges.md)
  - [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md)
  - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
