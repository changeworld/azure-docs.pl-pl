---
title: Przedpłata za usługę Azure Eksplorator danych Markup, aby zaoszczędzić pieniądze
description: Dowiedz się, jak kupić usługę Azure Eksplorator danych zarezerwowaną pojemność, aby zaoszczędzić na kosztach Eksplorator danych na platformie Azure.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: bbb2108967353b851a0fa0ee610ec30380e3fc50
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969267"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Przedpłata za usługę Azure Eksplorator danych Markup Unit przy użyciu zarezerwowanej pojemności platformy Azure Eksplorator danych

Skorzystaj z usługi Azure Eksplorator danych, aby zaoszczędzić pieniądze w porównaniu z cenami płatności zgodnie z rzeczywistym użyciem. Dzięki platformie Azure Eksplorator danych zarezerwowanej pojemności zobowiązujesz się do korzystania z usługi Azure Eksplorator danych przez okres jednego lub trzech lat w celu uzyskania znacznego rabatu w odniesieniu do kosztów naliczania Eksplorator danych platformy Azure. Aby kupić zastrzeżoną pojemność platformy Azure Eksplorator danych, wystarczy określić termin, który będzie stosowany do wszystkich wdrożeń platformy Azure Eksplorator danych we wszystkich regionach.

Zakup rezerwacji polega na przedpłaceniu za koszty związane z oznaczeniem przez okres jeden lub trzy lata. Po zakupieniu rezerwacji opłaty za korzystanie z usługi Azure Eksplorator danych Markup, które pasują do atrybutów rezerwacji, nie są już naliczane według stawek płatności zgodnie z rzeczywistym użyciem. W przypadku klastrów usługi Azure Eksplorator danych, które są już uruchomione lub te, które są nowo wdrożone, zostaną automatycznie pobrane korzyści. Ta rezerwacja nie obejmuje opłat za obliczenia, sieci ani magazynów związanych z klastrami. Zarezerwowana pojemność dla tych zasobów musi zostać zakupiona osobno. Na koniec okresu rezerwacji Pomoc dotycząca rozliczeń wygaśnie, a jednostki usługi Azure Eksplorator danych Markup są rozliczane według ceny płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są autoodnawiane. Aby uzyskać informacje o cenach, zobacz [stronę z cennikiem usługi Azure Eksplorator danych](https://azure.microsoft.com/pricing/details/data-explorer/).

Możesz kupić zastrzeżoną pojemność platformy Azure Eksplorator danych w [Azure Portal](https://portal.azure.com). Aby kupić zastrzeżoną pojemność platformy Azure Eksplorator danych:

* Musisz być właścicielem co najmniej jednej subskrypcji w przedsiębiorstwie lub z opcją płatność zgodnie z rzeczywistym użyciem.
* W przypadku subskrypcji Enterprise w witrynie [EA Portal](https://ea.azure.com) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Alternatywnie, jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji.
* W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą zakupić zastrzeżoną pojemność platformy Azure Eksplorator danych.

Aby uzyskać szczegółowe informacje na temat sposobu, w jaki Klienci korporacyjni i klienci korzystający z usługi płatność zgodnie z rzeczywistym użyciem są obciążani zakupami rezerwacji, zobacz:
* [Informacje na temat użycia usługi Azure Reservation na potrzeby rejestracji w przedsiębiorstwie](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 
* [Informacje na temat użycia usługi Azure Reservation dla subskrypcji z opcją płatność zgodnie z rzeczywistym](../cost-management-billing/reservations/understand-reserved-instance-usage.md)użyciem.

## <a name="determine-the-right-markup-usage-before-purchase"></a>Określ właściwe użycie znaczników przed zakupem

Rozmiar rezerwacji powinien opierać się na całkowitej liczbie jednostek znaczników Eksplorator danych platformy Azure używanych przez istniejące lub wkrótce wdrożone klastry Eksplorator danych platformy Azure. Liczba jednostek znaczników jest równa liczbie rdzeni klastra usługi Azure Eksplorator danych Engine w środowisku produkcyjnym (bez uwzględnienia jednostki SKU tworzenia i testowania). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Kupowanie zarezerwowanej pojemności platformy Azure Eksplorator danych

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Wybierz pozycję **wszystkie usługi** > **rezerwacje** > **Kup teraz**. Wybierz pozycję **Dodaj**.
1. W okienku **Wybierz typ produktu** wybierz pozycję **Azure Eksplorator danych** , aby zakupić nową rezerwację dla jednostek znaczników usługi Azure Eksplorator danych. 
1. Wybierz pozycję **Kup**
1. Wypełnij pola wymagane. 

    ![Strona kupowania](media/pricing-reserved-capacity/purchase-page.png)

1. Zapoznaj się z kosztem rezerwacji zarezerwowanej pojemności na platformie Azure Eksplorator danych w sekcji **kosztów** .
1. Wybierz pozycję **Kup**.
1. Wybierz pozycję **Wyświetl to zastrzeżenie** , aby zobaczyć stan zakupu.

## <a name="cancellations-and-exchanges"></a>Anulowania i wymiany

Jeśli zachodzi potrzeba anulowania rezerwacji zarezerwowanej na platformie Azure Eksplorator danych, może istnieć 12% opłata za wczesne zakończenie. Zwroty są ustalane na podstawie najniższej ceny zakupu lub bieżącej ceny rezerwacji. Zwroty są ograniczone do 50 000 USD rocznie. Otrzymany zwrot to pozostałe, obliczone proporcjonalnie saldo pomniejszone o 12% opłatę za wcześniejsze zakończenie. Aby zażądać anulowania, przejdź do rezerwacji w Azure Portal i wybierz pozycję **refund** , aby utworzyć żądanie pomocy technicznej.

Jeśli zachodzi potrzeba zmiany rezerwacji zarezerwowanej na platformę Azure Eksplorator danych na inny termin, można wymienić ją na inną rezerwację o wartości równej lub wyższej. Data rozpoczęcia okresu nowej rezerwacji nie jest przenoszona z wymienionej rezerwacji. Okres 1 lub 3-letniego zaczyna się od momentu utworzenia nowej rezerwacji. Aby zażądać wymiany, przejdź do rezerwacji w Azure Portal i wybierz pozycję **Exchange** , aby utworzyć żądanie obsługi.

Aby uzyskać więcej informacji na temat sposobu wymiany lub zastrzeżeń, zobacz temat [rezerwacje rezerwacji i](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)zwroty.

## <a name="manage-your-reserved-capacity-reservation"></a>Zarządzanie zarezerwowaną rezerwacją pojemności

Rabat zarezerwowany dla jednostek znaczników Eksplorator danych platformy Azure jest automatycznie stosowany do liczby jednostek znaczników, które pasują do zakresu rezerwacji i atrybutów zastrzeżonej pojemności platformy Azure Eksplorator danych. 


> [!NOTE]
> * Zakres rezerwacji zarezerwowanej pojemności Eksplorator danych platformy Azure można zaktualizować za pomocą [Azure Portal](https://portal.azure.com), programu PowerShell, interfejsu wiersza polecenia lub interfejsu API.
> * Aby dowiedzieć się, jak zarządzać rezerwacją zarezerwowaną dla platformy Azure Eksplorator danych, zobacz [Zarządzanie zastrzeżoną pojemnością platformy azure Eksplorator danych](../cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi Azure Reservations, zobacz następujące artykuły:

* [Co to jest Azure Reservations?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Zarządzanie usługą Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Informacje na temat rabatu na rezerwacje platformy Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
* [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations) (Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
