---
title: Kupowanie rezerwacji platformy Azure przy użyciu przedpłat lub płatności miesięcznych
description: Dowiedz się, jak można kupować rezerwacje platformy Azure przy użyciu przedpłat lub płatności miesięcznych.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: banders
ms.openlocfilehash: 77d663fa01e24acf63acd68d0b8d7cf4cc741055
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587094"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Kupowanie rezerwacji platformy Azure przy użyciu płatności miesięcznych

Do tej pory rezerwacje platformy Azure wymagały przedpłat. Teraz można płacić za rezerwacje przy użyciu płatności miesięcznych. W przeciwieństwie do zakupu z góry, gdy płacisz pełną kwotę, opcja płatności miesięcznych umożliwia równe podzielenie łącznego kosztu rezerwacji między wszystkie miesiące w danym okresie. Łączny koszt rezerwacji w przypadku płatności z góry i miesięcznych rat jest taki sam, a wybór płatności miesięcznych nie pociąga za sobą dodatkowych opłat.

Miesięczna kwota płatności może różnić się w zależności od obowiązującego w danym miesiącu rynkowego kursu wymiany waluty lokalnej.

Miesięczne płatności są dostępne w przypadku następujących rozwiązań:

- Maszyny wirtualne
- Azure Storage
- Bazy danych SQL
- SQL Data Warehouse
- Cosmos DB
- App Service — podatek od czynności cywilnoprawnych
- Dysk zarządzany
- Azure Data Explorer
- Azure Database for MariaDB, MySQL i PostgreSQL
- Azure VMware Solution by CloudSimple


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

Jeśli anulujesz rezerwację opłacaną miesięcznie, firma Microsoft może zastosować opłatę za anulowanie w wysokości 12% do anulowanych zatwierdzonych płatności. Jednak firma Microsoft obecnie nie nalicza tej kary. Anulowane płatności w ramach zobowiązania są wliczane do limitu zwrotu wynoszącego 50 000 USD. W przypadku naliczania kary za anulowanie nie ma ona wpływu na limit zwrotu.

Aby uzyskać więcej informacji o wymianach i zwrotach, zobacz temat [Self-service exchanges and refunds for Azure Reservations](exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o rezerwacjach, zobacz temat [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
- Aby dowiedzieć się więcej o zadaniach, które należy wykonać przed zakupem rezerwacji, zobacz sekcję dotyczącą [określania odpowiedniego rozmiaru maszyny wirtualnej przed zakupem](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
