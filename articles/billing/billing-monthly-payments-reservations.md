---
title: Kupowanie rezerwacji platformy Azure z góry lub miesięczną płatnością
description: Dowiedz się, jak kupić rezerwacje platformy Azure przy użyciu płatności z góry lub miesięcznie.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: banders
ms.openlocfilehash: d211334ad2aa760cd63b98c6827fb2512811a1d3
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806950"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Zakup rezerwacji z płatnościami miesięcznymi

Do tej pory rezerwacja platformy Azure wymagała przedpłaty. Teraz możesz uiścić płatność za rezerwacje miesięczne. W przeciwieństwie do zakupu z góry, gdy płacisz pełną kwotę, miesięczna opcja płatności dzieli łączny koszt rezerwacji równomiernie w każdym miesiącu w danym okresie. Łączny koszt z góry i miesięcznych rezerwacji jest taki sam, a opłaty za nie są naliczane, gdy użytkownik zdecyduje się płacić miesięcznie.

Miesięczna kwota płatności może się różnić w zależności od stawki tytułu rynkowego bieżącego miesiąca dla waluty lokalnej.

Miesięczne płatności są dostępne dla:

- Maszyny wirtualne
- SQL Database
- SQL Data Warehouse
- Cosmos DB
- Opłata za sygnaturę App Service

Kup rezerwacje w [Azure Portal](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Przykład przedstawiający zakup rezerwacji](./media/billing-monthly-payments-reservations/purchase-reservation.png)

Podczas zakupu rezerwacji można wyświetlić harmonogram płatności. Kliknij pozycję **Wyświetl pełny harmonogram płatności**.

![Przykład przedstawiający harmonogram płatności rezerwacji](./media/billing-monthly-payments-reservations/prepurchase-schedule.png)

Aby wyświetlić harmonogram płatności po zakupie, wybierz rezerwację, kliknij **Identyfikator zamówienia rezerwacji**, a następnie kliknij kartę **płatności** .

## <a name="view-payments-made"></a>Wyświetl płatności

Możesz wyświetlić płatności, które zostały wprowadzone przy użyciu interfejsów API, danych użycia i analizy kosztów. W przypadku rezerwacji płaconych miesięcznie wartość częstotliwości jest pokazywana jako **cykliczne** w przypadku danych użycia i interfejsu API opłat za rezerwację. W przypadku rezerwacji zapłaconych na początku wartość jest wyświetlana jako **jednorazowej**.

Analiza kosztów pokazuje miesięczne zakupy w widoku domyślnym. Zastosuj filtr **zakupu** dla **typu opłaty** i **cyklicznie** dla **częstotliwości** , aby wyświetlić wszystkie zakupy. Aby wyświetlić tylko rezerwacje, Zastosuj filtr dla **rezerwacji**.

![Przykład przedstawiający koszty zakupu rezerwacji w analizie kosztów](./media/billing-monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Przełączanie na miesięczne raty przy odnowieniu

W przypadku odnowienia rezerwacji można zmienić częstotliwość rozliczeń na wartość miesięczną.

## <a name="exchange-and-refunds"></a>Wymiana i zwroty

Podobnie jak w przypadku innych rezerwacji, można refundować rezerwacje lub zakupione w ramach rozliczeń miesięcznych. Obecnie możesz przesłać żądanie pomocy technicznej, aby rozpocząć wymianę lub refundę dla rezerwacji zakupionej przy comiesięcznym rozliczeniach.

Podczas wymiany rezerwacji, która jest płatna miesięcznie, łączny koszt okresu istnienia nowego zakupu powinien być większy niż pozostałe płatności, które są anulowane dla zwróconej rezerwacji. Nie istnieją żadne inne limity ani opłaty za wymianę. Możesz wymienić rezerwację płatną na potrzeby zakupu nowej rezerwacji, która jest rozliczana miesięcznie. Jednak wartość okresu istnienia nowej rezerwacji powinna być większa niż proporcjonalna wartość zwracanej rezerwacji.

Jeśli anulujesz rezerwację płatną miesięcznie, firma Microsoft może zastosować opłatę za anulowanie do przyszłych zatwierdzonych płatności, które zostały anulowane. Pozostałe zatwierdzone płatności naliczane są do limitu $50 000 USD.

Aby uzyskać więcej informacji na temat wymiany i zwrotów, zobacz samoobsługowe [wymianę i zwroty za Azure Reservations](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="faq"></a>Często zadawane pytania

PYTANIE: Czy platforma Azure oferuje "częściowe rezerwacje z góry"?<br>
A. Nie. Ze względu na to, że koszty rezerwacji na początku i miesięcznie są takie same, firma Microsoft nie obsługuje częściowych płatności z góry.

PYTANIE: Czy dla programu Microsoft Cloud dostawcy rozwiązań (CSP) są dostępne miesięczne płatności?<br>
A. Tak. partnerzy mogą kupować rezerwacje dla klientów korzystających z programu CSP w Azure Portal. Możliwość zakupu rezerwacji z miesięcznymi rozliczeniami nie jest dostępna w centrum partnerskim.

PYTANIE: Jestem Azure Government klienta, czy mogę uregulować zakupy rezerwacji miesięcznie?<br>
A. Obecnie nie.

PYTANIE: Kiedy mogę wymienić lub refundować w Azure Portal, zamiast tworzyć bilet pomocy technicznej?<br>
A. Obecnie nie. Żądania dotyczące programu Exchange i rezerwacji zwrotnych z miesięcznymi opłatami są obsługiwane przez pomoc techniczną platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat rezerwacji, zobacz [co to są Azure Reservations?](billing-save-compute-costs-reservations.md)
- Aby dowiedzieć się więcej o zadaniach, które należy wykonać przed zakupem rezerwacji, zobacz [Określanie odpowiedniego rozmiaru maszyny wirtualnej przed zakupem](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
