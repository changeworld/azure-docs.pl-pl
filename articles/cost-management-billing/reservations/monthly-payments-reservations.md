---
title: Kupowanie rezerwacji platformy Azure przy użyciu przedpłat lub płatności miesięcznych
description: Dowiedz się, jak można kupować rezerwacje platformy Azure przy użyciu przedpłat lub płatności miesięcznych.
services: billing
author: bandersmsft
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: banders
ms.openlocfilehash: cf9bb7f3b9661d5957ad569fce7112fe16659761
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995951"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Kupowanie rezerwacji platformy Azure przy użyciu płatności miesięcznych

Do tej pory rezerwacje platformy Azure wymagały przedpłat. Teraz można płacić za rezerwacje przy użyciu płatności miesięcznych. W przeciwieństwie do zakupu z góry, gdy płacisz pełną kwotę, opcja płatności miesięcznych umożliwia równe podzielenie łącznego kosztu rezerwacji między wszystkie miesiące w danym okresie. Łączny koszt rezerwacji w przypadku płatności z góry i miesięcznych rat jest taki sam, a wybór płatności miesięcznych nie pociąga za sobą dodatkowych opłat.

Miesięczna kwota płatności może różnić się w zależności od obowiązującego w danym miesiącu rynkowego kursu wymiany waluty lokalnej.

Miesięczne płatności są dostępne w przypadku następujących rozwiązań:

- Maszyny wirtualne
- Azure Storage
- Baza danych SQL
- Hurtownia danych SQL
- Cosmos DB
- App Service — podatek od czynności cywilnoprawnych

Rezerwacje można kupować w witrynie [Azure Portal](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Przykład przedstawiający zakup rezerwacji](./media/monthly-payments-reservations/purchase-reservation.png)

Podczas zakupu rezerwacji można wyświetlić harmonogram płatności. Kliknij pozycję **Wyświetl pełny harmonogram płatności**.

![Przykład przedstawiający harmonogram płatności za rezerwację](./media/monthly-payments-reservations/prepurchase-schedule.png)

Aby wyświetlić harmonogram płatności po zakupie, wybierz rezerwację, kliknij pozycję **Identyfikator zamówienia rezerwacji**, a następnie kliknij kartę **Płatności**.

## <a name="view-payments-made"></a>Wyświetlanie dokonanych płatności

Możesz wyświetlić płatności, które zostały dokonane przy użyciu interfejsów API, danych użycia i analizy kosztów. W przypadku rezerwacji płaconych miesięcznie wartość częstotliwości jest pokazywana jako **cykliczne** w przypadku danych użycia i interfejsu API opłat za rezerwację. W przypadku rezerwacji zapłaconych z góry wartość jest wyświetlana jako **jednorazowa**.

Analiza kosztów pokazuje w widoku domyślnym zakupy miesięczne. Zastosuj filtr **zakup** w polu **Typ opłaty** i **cykliczne** w polu **Częstotliwość**, aby zobaczyć wszystkie zakupy. Aby wyświetlić tylko rezerwacje, zastosuj filtr **Rezerwacja**.

![Przykład przedstawiający koszty zakupu rezerwacji w analizie kosztów](./media/monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Przełączanie na raty miesięczne przy odnowieniu

Podczas odnawiania rezerwacji można zmienić częstotliwość rozliczeń na miesięczną.

## <a name="exchange-and-refunds"></a>Wymiana i zwroty

Podobnie jak w przypadku innych rezerwacji, można uzyskiwać zwroty rezerwacji zakupionych w ramach rozliczeń miesięcznych lub wymieniać te rezerwacje. Obecnie możesz przesłać wniosek o pomoc techniczną, aby rozpocząć wymianę lub zwrot dla rezerwacji zakupionej w ramach rozliczeń miesięcznych.

Podczas wymiany rezerwacji opłacanej miesięcznie łączny koszt okresu istnienia nowego zakupu powinien być większy niż pozostałe płatności, które są anulowane dla zwróconej rezerwacji. Nie istnieją inne limity ani opłaty dotyczące wymian. Możesz wymienić rezerwację płatną z góry, aby kupić nową rezerwację, która jest rozliczana miesięcznie. Jednak wartość okresu istnienia nowej rezerwacji powinna być większa niż proporcjonalna wartość zwracanej rezerwacji.

Jeśli anulujesz rezerwację płatną miesięcznie, firma Microsoft może zastosować opłatę za anulowanie do przyszłych zatwierdzonych płatności, które zostaną anulowane. Pozostałe płatności w ramach zobowiązania są naliczane do limitu wynoszącego 50 000 USD.

Aby uzyskać więcej informacji o wymianach i zwrotach, zobacz temat [Self-service exchanges and refunds for Azure Reservations](exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o rezerwacjach, zobacz temat [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
- Aby dowiedzieć się więcej o zadaniach, które należy wykonać przed zakupem rezerwacji, zobacz sekcję dotyczącą [określania odpowiedniego rozmiaru maszyny wirtualnej przed zakupem](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
