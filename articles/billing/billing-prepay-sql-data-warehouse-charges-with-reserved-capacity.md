---
title: Oszczędność kosztów opłat za SQL Data Warehouse dzięki zarezerwowanej pojemności platformy Azure
description: Dowiedz się, w jaki sposób zaoszczędzić koszty SQL Data Warehouse naliczania opłat za zastrzeżoną pojemność, aby zaoszczędzić pieniądze.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 381a709c74f5fcf6bb1f89f07ad84d5e3af0c5e0
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806282"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Oszczędność kosztów SQL Data Warehouse opłatami za zarezerwowaną pojemność

Możesz zaoszczędzić pieniądze z Azure SQL Data Warehouse przez zatwierdzenie rezerwacji użycia cDWU przez okres jeden lub trzy lata. Aby kupić SQL Data Warehouse zarezerwowaną pojemność, musisz wybrać region i okres świadczenia usługi Azure. Następnie musisz dodać jednostkę SKU usługi SQL Data Warehouse do koszyka i wybrać liczbę jednostek cDWU do kupienia.

Gdy wykupisz rezerwację, za użycie usługi SQL Data Warehouse zgodne z atrybutami rezerwacji nie będą naliczane opłaty po stawkach płatności zgodnie z rzeczywistym użyciem.

Rezerwacja nie pokrywa kosztów magazynu i sieci wynikających z użycia usługi SQL Data Warehouse.

Gdy zarezerwowana pojemność wygaśnie, wystąpienia usługi SQL Data Warehouse dalej będą działać, ale będą za nie naliczane opłaty po stawkach płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są automatycznie odnawiane.

Aby uzyskać informacje o cenach, zobacz [SQL Data Warehouse zarezerwowanej pojemności](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

W [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)można kupić Azure SQL Data Warehouse zarezerwowaną pojemność. Płatność za rezerwację z [góry lub miesięczna płatność](billing-monthly-payments-reservations.md). Aby kupić zastrzeżoną pojemność:

- Musisz mieć rolę właściciela dla co najmniej jednej subskrypcji w przedsiębiorstwie lub z opcją płatność zgodnie z rzeczywistym użyciem.
- W przypadku subskrypcji przedsiębiorstwa w [portalu EA](https://ea.azure.com/)należy włączyć opcję **Dodaj wystąpienia zarezerwowane** . Jeśli to ustawienie jest wyłączone, musisz być administratorem EA.
- W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą zakupić SQL Data Warehouse zarezerwowaną pojemność.

Aby uzyskać więcej informacji na temat sposobu, w jaki Klienci korporacyjni i klienci korzystający z usługi płatność zgodnie z rzeczywistym użyciem są obciążani zakupami rezerwacji, zobacz [Opis użycia usługi Azure Reservation na potrzeby rejestracji w przedsiębiorstwie](billing-understand-reserved-instance-usage-ea.md) i [zrozumienie użycia rezerwacji platformy Azure dla Subskrypcja z płatność zgodnie z rzeczywistym](billing-understand-reserved-instance-usage.md)użyciem.

## <a name="choose-the-right-size-before-purchase"></a>Wybierz odpowiedni rozmiar przed zakupem

Rozmiar rezerwacji SQL Data Warehouse powinien opierać się na całkowitej liczbie zużywanych jednostek magazynu danych obliczeniowych (cDWU). Zakupy są dokonywane w przyrostach 100 cDWU.

Załóżmy na przykład, że całkowite użycie SQL Data Warehouse jest DW3000c. Chcesz zakupić zastrzeżoną pojemność dla wszystkich elementów. W związku z tym należy zakupić 30 jednostek zarezerwowanych pojemności cDWU.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Kupowanie SQL Data Warehouse zarezerwowanej pojemności

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **wszystkie** > **rezerwacje**usług.
3. Wybierz subskrypcję. Użyj listy subskrypcja, aby wybrać subskrypcję, która jest używana do regulowania zarezerwowanej pojemności. Metoda płatności za subskrypcję jest naliczana za koszt zarezerwowanej pojemności. Typ subskrypcji musi być umową Enterprise Agreement (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) lub płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P).
   - W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka.
   - W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty obciążają kartę kredytową lub metodę płatności faktury powiązaną z subskrypcją.
4. Wybierz zakres. Użyj listy zakres, aby wybrać zakres subskrypcji.
   - **Zakres pojedynczej grupy zasobów** — stosuje rabat rezerwacji do zasobów pasujących tylko w wybranej grupie zasobów.
   - **Zakres pojedynczej subskrypcji** — stosuje rabat rezerwacji do pasujących zasobów w wybranej subskrypcji.
   - **Zakres udostępniony** — stosuje rabat rezerwacji do pasujących zasobów w uprawnionych subskrypcjach, które znajdują się w kontekście rozliczeń. W przypadku klientów Umowa Enterprise, kontekst rozliczania to rejestracja. W przypadku indywidualnych subskrypcji z płatnością zgodnie z rzeczywistym użyciem zakres rozliczeń to wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.
   - W przypadku klientów korporacyjnych kontekst rozliczania to Rejestracja w ramach umowy EA.
   - W przypadku klientów z płatność zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie subskrypcje z płatność zgodnie z rzeczywistym uwzględnieniem utworzone przez administratora konta.
5. Wybierz region, aby wybrać region platformy Azure objęty zarezerwowaną pojemnością.
6. Wybierz liczbę. Wprowadź liczbę jednostek magazynu danych 100 (cDWU), które chcesz kupić.    
   Na przykład liczba 30 zapewni 3 000 cDWU pojemności zarezerwowanych co godzinę.
7. Zapoznaj się z SQL Data Warehouse koszt rezerwacji zarezerwowanej pojemności w sekcji **koszty** .
8. Wybierz pozycję **Kup**.
9. Wybierz pozycję **Wyświetl to zastrzeżenie** , aby wyświetlić stan zakupu.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zastrzeżenia zwrotne

Można anulować, wymienić lub refundować rezerwacje z pewnymi ograniczeniami. Aby uzyskać więcej informacji, zobacz samoobsługowe [weksle i zwroty dla Azure Reservations](billing-azure-reservations-self-service-exchange-and-refund.md).

Rabat rezerwacji jest automatycznie stosowany do liczby wystąpień SQL Data Warehouse, które pasują do SQL Data Warehouse zarezerwowanego zakresu i regionu pojemności. Zakres SQL Data Warehouse zarezerwowanej pojemności można zaktualizować za pomocą [Azure Portal](https://portal.azure.com/), programu PowerShell, interfejsu wiersza polecenia lub interfejsu API.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat sposobu stosowania rabatów rezerwacji do Azure SQL Data Warehouse, zobacz artykuł [jak rabaty rezerwacji mają zastosowanie do Azure SQL Data Warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Aby dowiedzieć się więcej na temat Azure Reservations, zobacz następujące artykuły:
  - [Co to są Azure Reservations?](billing-save-compute-costs-reservations.md)
  - [Zarządzanie usługą Azure Reservations](billing-manage-reserved-vm-instance.md)
  - [Opis rabatu Azure Reservations](billing-understand-reservation-charges.md)
  - [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](billing-understand-reserved-instance-usage.md)
  - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](billing-understand-reserved-instance-usage-ea.md)
