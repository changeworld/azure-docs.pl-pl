---
title: Kupowanie rezerwacji platformy Azure przy użyciu przedpłat lub płatności miesięcznych
description: Dowiedz się, jak można kupować rezerwacje platformy Azure przy użyciu przedpłat lub płatności miesięcznych.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: banders
ms.openlocfilehash: d211334ad2aa760cd63b98c6827fb2512811a1d3
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70806950"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Kupowanie rezerwacji platformy Azure przy użyciu płatności miesięcznych

Do tej pory rezerwacje platformy Azure wymagały przedpłat. Teraz można płacić za rezerwacje przy użyciu płatności miesięcznych. W przeciwieństwie do zakupu z góry, gdy płacisz pełną kwotę, opcja płatności miesięcznych umożliwia równe podzielenie łącznego kosztu rezerwacji między wszystkie miesiące w danym okresie. Łączny koszt rezerwacji w przypadku płatności z góry i miesięcznych rat jest taki sam, a wybór płatności miesięcznych nie pociąga za sobą dodatkowych opłat.

Miesięczna kwota płatności może różnić się w zależności od obowiązującego w danym miesiącu rynkowego kursu wymiany waluty lokalnej.

Miesięczne płatności są dostępne w przypadku następujących rozwiązań:

- Maszyny wirtualne
- SQL Database
- SQL Data Warehouse
- Cosmos DB
- App Service — podatek od czynności cywilnoprawnych

Rezerwacje można kupować w witrynie [Azure Portal](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Przykład przedstawiający zakup rezerwacji](./media/billing-monthly-payments-reservations/purchase-reservation.png)

Podczas zakupu rezerwacji można wyświetlić harmonogram płatności. Kliknij pozycję **Wyświetl pełny harmonogram płatności**.

![Przykład przedstawiający harmonogram płatności za rezerwację](./media/billing-monthly-payments-reservations/prepurchase-schedule.png)

Aby wyświetlić harmonogram płatności po zakupie, wybierz rezerwację, kliknij pozycję **Identyfikator zamówienia rezerwacji**, a następnie kliknij kartę **Płatności**.

## <a name="view-payments-made"></a>Wyświetlanie dokonanych płatności

Możesz wyświetlić płatności, które zostały dokonane przy użyciu interfejsów API, danych użycia i analizy kosztów. W przypadku rezerwacji płaconych miesięcznie wartość częstotliwości jest pokazywana jako **cykliczne** w przypadku danych użycia i interfejsu API opłat za rezerwację. W przypadku rezerwacji zapłaconych z góry wartość jest wyświetlana jako **jednorazowa**.

Analiza kosztów pokazuje w widoku domyślnym zakupy miesięczne. Zastosuj filtr **zakup** w polu **Typ opłaty** i **cykliczne** w polu **Częstotliwość**, aby zobaczyć wszystkie zakupy. Aby wyświetlić tylko rezerwacje, zastosuj filtr **Rezerwacja**.

![Przykład przedstawiający koszty zakupu rezerwacji w analizie kosztów](./media/billing-monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Przełączanie na raty miesięczne przy odnowieniu

Podczas odnawiania rezerwacji można zmienić częstotliwość rozliczeń na miesięczną.

## <a name="exchange-and-refunds"></a>Wymiana i zwroty

Podobnie jak w przypadku innych rezerwacji, można uzyskiwać zwroty rezerwacji zakupionych w ramach rozliczeń miesięcznych lub wymieniać te rezerwacje. Obecnie możesz przesłać wniosek o pomoc techniczną, aby rozpocząć wymianę lub zwrot dla rezerwacji zakupionej w ramach rozliczeń miesięcznych.

Podczas wymiany rezerwacji opłacanej miesięcznie łączny koszt okresu istnienia nowego zakupu powinien być większy niż pozostałe płatności, które są anulowane dla zwróconej rezerwacji. Nie istnieją inne limity ani opłaty dotyczące wymian. Możesz wymienić rezerwację płatną z góry, aby kupić nową rezerwację, która jest rozliczana miesięcznie. Jednak wartość okresu istnienia nowej rezerwacji powinna być większa niż proporcjonalna wartość zwracanej rezerwacji.

Jeśli anulujesz rezerwację płatną miesięcznie, firma Microsoft może zastosować opłatę za anulowanie do przyszłych zatwierdzonych płatności, które zostaną anulowane. Pozostałe płatności w ramach zobowiązania są naliczane do limitu wynoszącego 50 000 USD.

Aby uzyskać więcej informacji o wymianach i zwrotach, zobacz temat [Self-service exchanges and refunds for Azure Reservations](billing-azure-reservations-self-service-exchange-and-refund.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="faq"></a>Często zadawane pytania

PYTANIE: Czy platforma Azure oferuje „rezerwacje płatne częściowo z góry”?<br>
A. Nie. Ze względu na to, że koszty rezerwacji płatnych z góry i miesięcznie są takie same, firma Microsoft nie obsługuje częściowych płatności z góry.

PYTANIE: Czy płatności miesięczne są dostępne dla programu Microsoft Cloud Solution Provider (CSP)?<br>
A. Tak, partnerzy mogą kupować rezerwacje dla klientów korzystających z programu CSP w witrynie Azure Portal. Możliwość zakupu rezerwacji z rozliczeniami miesięcznymi nie jest dostępna w centrum partnerskim.

PYTANIE: Jestem klientem platformy Azure Government (USA), czy mogę płacić za zakupy rezerwacji miesięcznie?<br>
A. Obecnie nie.

PYTANIE: Kiedy można będzie samodzielnie wymienić lub uzyskać zwrot w witrynie Azure Portal, zamiast tworzyć bilet pomocy technicznej?<br>
A. Obecnie nie. Żądania dotyczące wymiany i zwrotu rezerwacji z płatnościami miesięcznymi są obsługiwane przez dział pomocy technicznej platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o rezerwacjach, zobacz temat [Co to są rezerwacje platformy Azure?](billing-save-compute-costs-reservations.md)
- Aby dowiedzieć się więcej o zadaniach, które należy wykonać przed zakupem rezerwacji, zobacz sekcję dotyczącą [określania odpowiedniego rozmiaru maszyny wirtualnej przed zakupem](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
