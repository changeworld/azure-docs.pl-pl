---
title: Samoobsługowe wymianę i zwroty za Azure Reservations
description: Dowiedz się, w jaki sposób można wymienić i refundować Azure Reservations.
author: yashesvi
manager: yashesvi
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2019
ms.author: banders
ms.openlocfilehash: 5e38684500520d4565835456b94200aea399c938
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814103"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Samoobsługowe wymianę i zwroty za Azure Reservations

Azure Reservations zapewnia elastyczność, aby pomóc sprostać potrzebom rozwoju. Rezerwację można wymienić na inną rezerwację tego samego typu. Jeśli rezerwacja nie jest już potrzebna, można uzyskać zwrot jej kosztów, maksymalnie 50 000 USD rocznie.

Możliwości samoobsługowej wymiany i anulowania nie są dostępne dla klientów z umową Enterprise Agreement dla instytucji rządowych USA. Obsługiwane są inne typy subskrypcji dla instytucji rządowych Stanów Zjednoczonych, w tym płatność zgodnie z rzeczywistym użyciem i dostawcy usług kryptograficznych.

Użytkownik musi mieć dostęp właściciela w kolejności rezerwacji w celu wymiany lub zwrotu istniejącej rezerwacji.

## <a name="exchange-an-existing-reserved-instance"></a>Wymienia istniejące wystąpienie zarezerwowane

Możesz wymienić rezerwację na trzy szybkie kroki w [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Wybierz rezerwacje, które chcesz zwrócić, a następnie kliknij pozycję **Exchange**.  
    ![Przykładowy obraz przedstawiający rezerwacje do zwrócenia](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Wybierz produkt maszyny wirtualnej, który chcesz kupić, a następnie wpisz ilość. Upewnij się, że nowa suma zakupów jest większa niż suma zwracana. [Określ odpowiedni rozmiar przed zakupem](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Przykładowy obraz przedstawiający produkt maszyny wirtualnej do zakupu przy użyciu programu Exchange](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Przejrzyj i wypełnij transakcję.  
    ![Przykładowy obraz przedstawiający produkt maszyny wirtualnej do zakupu w ramach wymiany, kończąc zwrot](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Aby refundować rezerwację, przejdź do **szczegółów zastrzeżenia** i kliknij pozycję **zwrot**.

## <a name="how-transactions-are-processed"></a>Sposób przetwarzania transakcji

Po pierwsze firma Microsoft anuluje istniejącą rezerwację i zwrotuje kwotę proporcjonalną do tej rezerwacji. Jeśli istnieje wymiana, nowy zakup jest przetwarzany. Firma Microsoft przetwarza zwroty przy użyciu jednej z następujących metod, w zależności od typu konta i formy płatności:

### <a name="enterprise-agreement-customers"></a>Klienci z umową Enterprise Agreement

Pieniądze są dodawane do zobowiązania pieniężnego za wymianę i zwroty w przypadku, gdy pierwotny zakup został utworzony przy użyciu jednego z nich. Wszystkie faktury nadwyżkowe od pierwotnych zakupów są ponownie otwierane i klasyfikowane w celu upewnienia się, że jest używane zobowiązanie pieniężne. Jeśli termin zobowiązania pieniężnego korzystający z rezerwacji nie jest już aktywny, do bieżącego terminu zobowiązania pieniężnego w ramach umowy Enterprise Agreement zostanie dodany kredyt.

Jeśli oryginalny zakup został zrealizowany jako nadwyżka, firma Microsoft wystawia notę kredytową.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Płatność zgodnie z rzeczywistym użyciem faktury i program CSP

Oryginalna faktura zakupu rezerwacji została anulowana, a następnie dla zwrotu zostanie utworzona nowa faktura. W przypadku wymiany nowe faktury przedstawiają zwrot i nowy zakup. Kwota zwrotu jest dostosowywana do zakupu. Jeśli zakupisz tylko rezerwację, kwota proporcjonalnie do firmy Microsoft zostanie zmieniona i zostanie dostosowana do przyszłego zakupu rezerwacji.

### <a name="pay-as-you-go-credit-card-customers"></a>Klienci korzystający z karty kredytowej z płatność zgodnie z rzeczywistym użyciem

Oryginalna faktura została anulowana i zostanie utworzona nowa faktura. Pieniądze zostaną zwrócone na kartę kredytową, która została użyta w przypadku oryginalnego zakupu. Jeśli Twoja karta została zmieniona, [skontaktuj się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Zasady anulowania, wymiany i zwrotu pieniędzy

Platforma Azure ma następujące zasady dotyczące anulowania, wymiany i zwrotów.

**Zasady anulowania**

- W przypadku anulowania rezerwacji może istnieć 12% opłata za wczesne zakończenie.
- Zwrot otrzymywany w przypadku anulowania to pozostała stawka proporcjonalnie do oceny, pomniejszona o 12% opłaty za wczesne zakończenie. Aby anulować, przejdź do rezerwacji w Azure Portal i wybierz pozycję **zwrot**.

**Zasady programu Exchange**

- Można zwrócić wiele istniejących rezerwacji, aby zakupić nową rezerwację tego samego typu. Nie można wymieniać zastrzeżeń jednego typu dla innego. Na przykład nie można zwrócić rezerwacji maszyny wirtualnej w celu zakupienia rezerwacji SQL.
- Tylko właściciele rezerwacji mogą przetwarzać program Exchange. [Dowiedz się, jak dodawać i zmieniać użytkowników, którzy mogą zarządzać rezerwacją](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Wymiana jest przetwarzana jako zwrot i rezakup — różne transakcje są tworzone dla anulowania i nowego zakupu. Proporcjonalna kwota rezerwacji jest zwracana w przypadku rezerwacji, w których zawarto wymianę. Nastąpi całkowite naliczenie nowego zakupu. Kwota rezerwacji proporcjonalnie to dzienna, proporcjonalna wartość końcowa zwracanej rezerwacji.
- Możesz wymienić lub refundować rezerwacje, nawet jeśli umowa Enterprise Agreement użyta do zakupu rezerwacji wygasła i została odnowiona jako nowa umowa.
- Można zmienić dowolną Właściwość rezerwacji, taką jak rozmiar, region, ilość i termin, z wymianą.
- Nowa suma zakupu powinna być równa lub większa niż zwracana kwota.
- Nowe zastrzeżenie zakupione w ramach programu Exchange ma nowy termin od momentu wymiany.
- Nie ma kar ani rocznych limitów dla wymian.

**Zasady dotyczące zwrotów**
- Łączna kwota zwrotu nie może przekroczyć $50 000 USD w 12-miesięcznym oknie kroczącym.
- Zwroty są obliczane na podstawie najniższej ceny zakupu lub bieżącej ceny rezerwacji.
- Tylko właściciele rezerwacji mogą przetwarzać zwroty. [Dowiedz się, jak dodawać i zmieniać użytkowników, którzy mogą zarządzać rezerwacją](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Firma Microsoft zastrzega sobie prawo do naliczania opłat za 12% za wszelkie zwroty, chociaż kary nie są obecnie naliczane.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Wymiana magazynu niepremium dla magazynu w warstwie Premium

Rezerwacja zakupionego dla rozmiaru maszyny wirtualnej, który nie obsługuje magazynu Premium Storage, można wymienić na odpowiedni rozmiar maszyny wirtualnej. Na przykład _F1_ dla _F1s_. Aby dokonać wymiany, przejdź do szczegółów zastrzeżenia i kliknij pozycję **Exchange**. Program Exchange nie resetuje warunku zarezerwowanego wystąpienia lub tworzy nową transakcję.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak zarządzać rezerwacją, zobacz [zarządzanie Azure Reservations](billing-manage-reserved-vm-instance.md).
- Aby dowiedzieć się więcej na temat Azure Reservations, zobacz następujące artykuły:
    - [Co to są Azure Reservations?](billing-save-compute-costs-reservations.md)
    - [Zarządzanie rezerwacjami na platformie Azure](billing-manage-reserved-vm-instance.md)
    - [Zapoznaj się z zastosowaniem rabatu rezerwacji](billing-understand-vm-reservation-charges.md)
    - [Informacje na temat użycia rezerwacji dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
    - [Informacje na temat użycia rezerwacji na potrzeby rejestracji w przedsiębiorstwie](billing-understand-reserved-instance-usage-ea.md)
    - [Koszty oprogramowania systemu Windows, które nie są objęte rezerwacjami](billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations w programie Partner Center Cloud Solution Provider (CSP)](/partner-center/azure-reservations)
