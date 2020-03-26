---
title: Kupowanie rezerwacji platformy Azure przy użyciu przedpłat lub płatności miesięcznych
description: Dowiedz się, jak można kupować rezerwacje platformy Azure przy użyciu przedpłat lub płatności miesięcznych.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: ede60adc13dadc38e18ee5ade468e01b16523f4f
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235747"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Kupowanie rezerwacji platformy Azure przy użyciu płatności miesięcznych

Możesz płacić za rezerwacje przy użyciu płatności miesięcznych. W przeciwieństwie do zakupu z góry, gdy płacisz pełną kwotę, opcja płatności miesięcznych umożliwia równe podzielenie łącznego kosztu rezerwacji między wszystkie miesiące w danym okresie. Łączny koszt rezerwacji w przypadku płatności z góry i miesięcznych rat jest taki sam, a wybór płatności miesięcznych nie pociąga za sobą dodatkowych opłat.

Jeśli rezerwacja została zakupiona w ramach umowy klienta firmy Microsoft, miesięczna kwota płatności może różnić się w zależności od obowiązującego w danym miesiącu rynkowego kursu wymiany waluty lokalnej.

Miesięczne płatności nie są dostępne w przypadku następujących rozwiązań: Databricks, rezerwacje systemu SUSE Linux, plany oprogramowania Red Hat i Azure Red Hat OpenShift Compute.

Rezerwacje można kupować w witrynie [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs).

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

Podobnie jak w przypadku innych rezerwacji, można uzyskiwać zwroty rezerwacji zakupionych w ramach rozliczeń miesięcznych lub wymieniać te rezerwacje. 

Podczas wymiany rezerwacji opłacanej miesięcznie łączny koszt okresu istnienia nowego zakupu powinien być większy niż pozostałe płatności, które są anulowane dla zwróconej rezerwacji. Nie istnieją inne limity ani opłaty dotyczące wymian. Możesz wymienić rezerwację płatną z góry, aby kupić nową rezerwację, która jest rozliczana miesięcznie. Jednak wartość okresu istnienia nowej rezerwacji powinna być większa niż proporcjonalna wartość zwracanej rezerwacji.

Jeśli anulujesz rezerwację opłacaną miesięcznie, anulowane przyszłe płatności są wliczane do limitu zwrotu wynoszącego 50 000 USD.

Aby uzyskać więcej informacji o wymianach i zwrotach, zobacz temat [Self-service exchanges and refunds for Azure Reservations](exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o rezerwacjach, zobacz temat [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
- Aby dowiedzieć się więcej o zadaniach, które należy wykonać przed zakupem rezerwacji, zobacz sekcję dotyczącą [określania odpowiedniego rozmiaru maszyny wirtualnej przed zakupem](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
