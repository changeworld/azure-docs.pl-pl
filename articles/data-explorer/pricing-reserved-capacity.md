---
title: Przedpłata za znaczniki Usługi Azure Data Explorer w celu zaoszczędzenia pieniędzy
description: Dowiedz się, jak kupić zarezerwowaną pojemność Usługi Azure Data Explorer, aby zaoszczędzić na kosztach Eksploratora danych platformy Azure.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: bbb2108967353b851a0fa0ee610ec30380e3fc50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969267"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Przedpłata za jednostki znaczników Eksploratora danych platformy Azure z pojemnością zastrzeżoną Usługi Azure Data Explorer

Zaoszczędź pieniądze za pomocą usługi Azure Data Explorer, płacąc z góry za jednostki znaczników w porównaniu z cenami płatności zgodnie z rzeczywistym ratunkiem. Dzięki pojemności zarezerwowanej usługi Azure Data Explorer użytkownik zobowiązuje się do korzystania z usługi Azure Data Explorer przez okres jednego lub trzech lat, aby uzyskać znaczny rabat na koszty znaczników Usługi Azure Data Explorer. Aby zakupić pojemność zarezerwowaną usługi Azure Data Explorer, wystarczy określić termin, który będzie miał zastosowanie do wszystkich wdrożeń usługi Azure Data Explorer we wszystkich regionach.

Kupując rezerwację, płacisz z góry za koszty marży przez okres jednego lub trzech lat. Jak tylko kupisz rezerwację, opłaty znaczników Usługi Azure Data Explorer, które pasują do atrybutów rezerwacji, nie są już naliczane według stawek płatności zgodnie z rzeczywistymu. Klastry usługi Azure Data Explorer, które są już uruchomione lub te, które są nowo wdrożone automatycznie otrzymają korzyści. Ta rezerwacja nie obejmuje opłat obliczeniowych, sieciowych ani związanych z klastrami. Pojemność rezerwowa dla tych zasobów musi być zakupiona oddzielnie. Po zakończeniu okresu rezerwacji świadczenie rozliczeniowe wygasa, a jednostki znaczników Usługi Azure Data Explorer są rozliczane według ceny płatności zgodnie z rzeczywistymi oczekiwaniami. Rezerwacje nie są odnawiane automatycznie. Aby uzyskać informacje o cenach, zobacz [stronę cennika Usługi Azure Data Explorer](https://azure.microsoft.com/pricing/details/data-explorer/).

Zarezerwowaną pojemność usługi Azure Data Explorer można kupić w [witrynie Azure portal](https://portal.azure.com). Aby kupić pojemność rezerwową usługi Azure Data Explorer:

* Musisz być właścicielem co najmniej jednej subskrypcji Enterprise lub Pay-As-You-Go.
* W przypadku subskrypcji Enterprise w witrynie [EA Portal](https://ea.azure.com) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Alternatywnie, jeśli to ustawienie jest wyłączone, musisz być administratorem EA w ramach subskrypcji.
* W przypadku programu Dostawcy rozwiązań w chmurze (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą zakupić pojemność zarezerwowaną usługi Azure Data Explorer.

Aby uzyskać szczegółowe informacje na temat sposobu naliczania opłat za zakupy rezerwacji klientom korporacyjnym i klientom korzystającym z płatności zgodnie z rzeczywistym obciążeniem.
* [Opis użycia rezerwacji platformy Azure dla rejestracji w przedsiębiorstwie](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 
* [Poznaj użycie rezerwacji platformy Azure dla subskrypcji płatności zgodnie z rzeczywistym użyciem.](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-markup-usage-before-purchase"></a>Określanie właściwego użycia znaczników przed zakupem

Rozmiar rezerwacji powinien być oparty na całkowitej liczbie jednostek znaczników Usługi Azure Data Explorer używanych przez istniejące lub wkrótce wdrożone klastry usługi Azure Data Explorer. Liczba jednostek znaczników jest równa liczbie rdzeni klastra aparatu usługi Azure Data Explorer w produkcji (z wyłączeniem jednostki SKU deweloperskiej/testowej). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Kupowanie pojemności zarezerwowanej w Eksploratorze danych platformy Azure

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz **pozycję Wszystkie usługi** > **Rezerwacje** > **kup teraz**. Wybierz **pozycję Dodaj**
1. W okienku **Wybierz typ produktu** wybierz Pozycję **Eksploratora danych platformy Azure,** aby kupić nową rezerwację dla jednostek znaczników Usługi Azure Data Explorer. 
1. Wybierz **Kup**
1. Wypełnij wymagane pola. 

    ![Strona zakupu](media/pricing-reserved-capacity/purchase-page.png)

1. Przejrzyj koszt rezerwacji pojemności zarezerwowanej przez usługę Azure Data Explorer w sekcji **Koszty.**
1. Wybierz pozycję **Kup**.
1. Wybierz **pozycję Wyświetl tę rezerwację,** aby zobaczyć stan zakupu.

## <a name="cancellations-and-exchanges"></a>Anulowania i wymiany

Jeśli chcesz anulować rezerwację pojemności zarezerwowaną w Eksploratorze danych platformy Azure, może to być opłata za wcześniejsze rozwiązanie 12%. Zwroty są oparte na najniższej cenie zakupu lub aktualnej cenie rezerwacji. Zwroty są ograniczone do 50 000 USD rocznie. Otrzymany zwrot to pozostałe, obliczone proporcjonalnie saldo pomniejszone o 12% opłatę za wcześniejsze zakończenie. Aby zażądać anulowania, przejdź do rezerwacji w witrynie Azure portal i wybierz **zwrot pieniędzy,** aby utworzyć żądanie pomocy technicznej.

Jeśli musisz zmienić rezerwację pojemności zarezerwowaną w Eksploratorze danych platformy Azure na inny termin, możesz wymienić ją na inną rezerwację o takiej samej lub większej wartości. Data rozpoczęcia okresu nowej rezerwacji nie jest przenoszona z wymienionej rezerwacji. Okres 1 lub 3 lat rozpoczyna się od momentu utworzenia nowej rezerwacji. Aby zażądać wymiany, przejdź do rezerwacji w witrynie Azure portal i wybierz pozycję **Exchange,** aby utworzyć żądanie pomocy technicznej.

Aby uzyskać więcej informacji na temat wymiany lub zwrotu pieniędzy rezerwacji, zobacz [Wymiana rezerwacji i zwroty pieniędzy](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="manage-your-reserved-capacity-reservation"></a>Zarządzanie rezerwacją zarezerwowanej pojemności

Rabat rezerwacji jednostek znaczników Usługi Azure Data Explorer jest stosowany automatycznie do liczby jednostek znaczników, które odpowiadają zakresowi rezerwacji zarezerwowanej pojemności i atrybutom usługi Azure Data Explorer. 


> [!NOTE]
> * Zakres rezerwacji pojemności zarezerwowaną w eksploratorze danych platformy Azure można zaktualizować za pośrednictwem [witryny Azure portal](https://portal.azure.com), programu PowerShell, interfejsu wiersza polecenia lub interfejsu API.
> * Aby dowiedzieć się, jak zarządzać rezerwacją zarezerwowanej pojemności Usługi Azure Data Explorer, zobacz [zarządzanie pojemnością rezerwową Usługi Azure Data Explorer](../cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi Azure Reservations, zobacz następujące artykuły:

* [Co to są rezerwacje platformy Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Zarządzanie rezerwacjami platformy Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Informacje na temat rabatu na rezerwacje platformy Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
* [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations) (Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
