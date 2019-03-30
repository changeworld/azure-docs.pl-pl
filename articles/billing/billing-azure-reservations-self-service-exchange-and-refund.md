---
title: Samoobsługowe wymiany i zwrot kosztów dla platformy Azure rezerwacji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można wymienić lub refundacji rezerwacje Azure.
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
ms.date: 03/28/2019
ms.author: banders
ms.openlocfilehash: 229b5a0948a81c9b5d78dd49f304e7b6891f95ee
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653251"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Samoobsługowe wymiany i zwrot kosztów dla platformy Azure rezerwacji

Rezerwacje platformy Azure zapewniają elastyczność pozwalającą spełniać potrzeby stale rosnących potrzeb. Mogą wymieniać rezerwacji do innej rezerwacji tego samego typu. Jeśli nie są już potrzebne, można również zwrócić rezerwacji, maksymalnie 50 000 USD rocznie.

## <a name="exchange-an-existing-reserved-instance"></a>Wymiany istniejącego wystąpienia zarezerwowane

Mogą wymieniać rezerwacji z trzech krótkich krokach w [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Wybierz rezerwacji, które chcesz zwrotu, a następnie kliknij przycisk **Exchange**.  
    ![Przykładowy obraz przedstawiający rezerwacje, aby zwrócić ](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Wybierz produkt maszyny Wirtualnej, który chcesz kupić, a następnie wpisz ilość. Upewnij się, że nowa suma zakupu jest większa niż suma zwrotu. [Rozmiar jest określany na prawo przed zakupem](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Przykładowy obraz przedstawiający produktu maszyny Wirtualnej do zakupu za pomocą programu exchange ](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Przejrzyj i Zakończ transakcję.  
    ![Przykładowy obraz przedstawiający produktu maszyny Wirtualnej do zakupu za pomocą programu exchange z ukończenie powrotu ](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Aby zwrot kosztów rezerwacji, przejdź do **szczegóły rezerwacji** i kliknij przycisk **zwrotu**.

## <a name="how-return-and-exchange-transactions-are-processed"></a>Jak wróć i przetwarzania transakcji programu exchange

Po pierwsze firma Microsoft anuluje istniejącą rezerwację i zwroty kwotę proporcjonalną dla tej rezerwacji. W przypadku wymiany nowy zakup jest przetwarzany. Firma Microsoft przetwarza zwroty przy użyciu jednej z następujących metod, w zależności od typu konta i formę płatności:

### <a name="enterprise-agreement-customers"></a>Enterprise agreement klienci

Pieniędzy zostanie dodany do zobowiązania pieniężnego do wymiany, a zwroty, jeśli pierwotnego zakupu dokonano przy użyciu jednego. Używany jest żadnych faktur użycie nadwyżkowe, ponieważ oryginalna zakupy są ponownie otwarte i rerated, aby upewnić się, że zobowiązania pieniężnego. Jeśli w ramach zobowiązań pieniężnych termin, używając zakupu rezerwacji nie jest już aktywna, środki jest dodawany do bieżącego okresu obowiązywania opartych na zobowiązaniu pieniężnym umowy enterprise.

Jeśli pierwotnego zakupu dokonano jako nadwyżkowe użycie, firma Microsoft udostępnia noty kredytowej.

### <a name="pay-as-you-go-invoice-payment-customers-and-cloud-solution-provider-program"></a>Płatność za rzeczywiste użycie faktury płatności klientów i Cloud solution provider program

Oryginalny faktury zakupu rezerwacji zostanie anulowane, a następnie nową fakturę jest tworzone zwrotu. Dla wymiany nową fakturę pokazuje refundacji i nowe możliwości zakupu. Kwota zwrotów jest dostosowywany względem zakupu. Jeśli rezerwacja jest tylko zwrotu, kwotę proporcjonalną pozostaje z firmą Microsoft i jest dostosowywany dla zakupu rezerwacji w przyszłości.

### <a name="pay-as-you-go-credit-card-customers"></a>Płatność za rzeczywiste użycie karty kredytowej klientów

Oryginalna faktura zostanie anulowane i utworzeniu nowego faktury. Pieniądze, jest zwracana do karty kredytowej, którego użyto do pierwotnego zakupu. Jeśli zmieniono Twoja karta [się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="exchange-policies"></a>Zasady programu Exchange

- Może zwracać wiele istniejących rezerwacji do zakupu nową rezerwację tego samego typu. Nie można wymienić rezerwacje jednego typu na inny. Na przykład nie można zwrócić rezerwacji maszyny Wirtualnej, aby kupić rezerwację SQL.
- Tylko właściciele rezerwacji może przetwarzać wymiany. [Dowiedz się, jak dodawanie lub zmienianie użytkowników, którzy mogą zarządzać rezerwacji](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation).
- Program exchange jest przetwarzany jako zwrot kosztów i odkupu — różne transakcje są tworzone dla anulowania i nowe możliwości zakupu. Kwotę proporcjonalną rezerwacji jest zwracana dla rezerwacji tego handlowe użytkownik. Opłaty są naliczane w pełni dla nowego zakupu. Kwotę proporcjonalną rezerwacji jest to codzienne proporcjonalną wartość rezerwacji zwracanego.
- Można wymienić lub zwrot kosztów rezerwacji nawet wtedy, gdy umowy enterprise agreement wykorzystać na zakup rezerwacji wygasła i została odnowiona umowy.
- Możesz zmienić dowolną właściwość rezerwacji, takich jak rozmiar, region, ilości i termin z programem exchange.
- Nowa suma zakupu należy równe lub być większa niż wartość zwrócona.
- Nową rezerwację nabyć w ramach programu exchange ma nowy termin, rozpoczynając od czasu programu exchange.
- Nie ma opłaty karnej lub roczne limity do wymiany.

## <a name="refund-policies"></a>Zasady zwrot kosztów

- Kwota całkowita zwrotu nie może przekraczać 50 000 USD w oknie stopniowe 12-miesięcznym.
- Tylko właściciele rezerwacji może przetwarzać zwrot pieniędzy. [Dowiedz się, jak dodawanie lub zmienianie użytkowników, którzy mogą zarządzać rezerwacji](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Firma Microsoft zastrzega sobie prawo do opłata w wysokości 12% karę za wszelkie zwraca, mimo że kary aktualnie nie jest rozliczane.

## <a name="exchange-a-non-premium-storage-vm-reservation-for-a-premium-storage-reservation"></a>Exchange magazynu innego niż premium rezerwacji maszyny Wirtualnej dla rezerwacji magazynu premium

Mogą wymieniać zakupionych dla rozmiaru maszyny Wirtualnej, która nie obsługuje usługę premium storage do odpowiedniego rozmiaru maszyny Wirtualnej, który wykonuje rezerwacji. Na przykład _F1_ dla _F1s_. Aby programu exchange, przejdź do szczegółów rezerwacji, a następnie kliknij przycisk **Exchange**. Programu exchange nie Resetuj okres wystąpienia zarezerwowanego lub Utwórz nową transakcję.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak zarządzać rezerwacji, zobacz [Zarządzanie zastrzeżeniami Azure](billing-manage-reserved-vm-instance.md).
- Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:
    - [Co to jest Azure rezerwacje?](billing-save-compute-costs-reservations.md)
    - [Zarządzanie zastrzeżeniami na platformie Azure](billing-manage-reserved-vm-instance.md)
    - [Zrozumienie, jak stosowany jest rabat związany z rezerwacją](billing-understand-vm-reservation-charges.md)
    - [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
    - [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)
    - [Koszty oprogramowania Windows nie jest dołączony do rezerwacji](billing-reserved-instance-windows-software-costs.md)
    - [Rezerwacje platformy Azure w programie partnera Centrum Cloud Solution Provider (CSP)](/partner-center/azure-reservations)
