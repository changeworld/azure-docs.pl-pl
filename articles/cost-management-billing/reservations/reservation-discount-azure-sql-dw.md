---
title: Stosowanie rabatów na rezerwacje usługi Azure SQL Data Warehouse | Microsoft Docs
description: Dowiedz się, w jaki sposób rabaty na rezerwacje stosowane względem usługi Azure SQL Data Warehouse pomagają zaoszczędzić pieniądze.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: banders
ms.openlocfilehash: 24a673dc7949d5ce05aa1a701cc8b939a766fd99
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76313930"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Stosowanie rabatów na rezerwacje usługi Azure SQL Data Warehouse

Po zakupie pojemności zarezerwowanej usługi Azure SQL Data Warehouse rabat dotyczący rezerwacji jest automatycznie stosowany do magazynów danych istniejących w danym regionie. Rabat dotyczący rezerwacji ma zastosowanie do użycia emitowanego przez miernik cDWU usługi SQL Data Warehouse. Opłaty za magazyn i sieć są naliczane według stawek płatności zgodnie z rzeczywistym użyciem.

## <a name="reservation-discount-application"></a>Stosowanie rabatu na rezerwację

Rabat na pojemność zarezerwowaną usługi SQL Data Warehouse jest stosowany względem działających magazynów z rozliczeniem godzinowym. Jeśli magazyn nie będzie wdrożony przez godzinę, pojemność zarezerwowana zostanie utracona dla tej godziny. Niewykorzystane wartości nie są przenoszone na następne okresy.

Po zakupie rezerwacja jest dopasowywana do użycia usługi SQL Data Warehouse emitowanego przez działające magazyny w dowolnym momencie. Jeśli wyłączysz niektóre magazyny, rabaty na rezerwacje będą automatycznie stosowane do pozostałych zgodnych magazynów.

W przypadku magazynów, które pozostają wyłączone przez pełną godzinę, rezerwacja jest automatycznie stosowana do innych zgodnych wystąpień podczas tej godziny.

## <a name="discount-examples"></a>Przykłady rabatów

W poniższych przykładach pokazano, jak jest stosowany rabat na pojemność zarezerwowaną usługi SQL Data Warehouse w zależności od wdrożeń.

- **Przykład 1**: zakup 5 jednostek 100 cDWU zarezerwowanej pojemności. Uruchomiono na jedną godzinę wystąpienie usługi SQL Data Warehouse DW1500c. W takim przypadku użycie jest emitowane dla 15 jednostek po 100 jednostek cDWU użycia każda. Rabat związany z rezerwacją dotyczy 5 wykorzystywanych jednostek. Opłata jest naliczana według stawek płatności zgodnie z rzeczywistym użyciem dla pozostałych 10 jednostek po 100 jednostek cDWU użycia każda. Innymi słowy, w przypadku wielu rezerwacji jest możliwe częściowe pokrycie.

- **Przykład 2**: zakup 5 jednostek 100 cDWU zarezerwowanej pojemności. Uruchomiono na jedną godzinę dwa wystąpienia usługi SQL Data Warehouse DW100c. W takim przypadku dwa zdarzenia użycia są emitowane dla 1 jednostki obejmującej 100 jednostek cDWU użycia. Oba zdarzenia użycia mają rabaty dotyczące pojemności zarezerwowanej. Pozostałe 3 jednostki po 100 jednostek cDWU pojemności zarezerwowanej każda są tracone i nie są przenoszone do użytku w przyszłości. Innymi słowy pojedyncze rezerwacje można dopasować do wielu wystąpień SQL Data Warehouse.

- **Przykład 3**: zakup 1 jednostki 100 cDWU zarezerwowanej pojemności. Uruchomiono dwa wystąpienia usługi SQL Data Warehouse DW100c. Każdy przebieg trwał 30 minut. W takim przypadku oba zdarzenia użycia mają rabaty dotyczące pojemności zarezerwowanej. Nie jest naliczana opłata za użycie według stawek płatności zgodnie z rzeczywistym użyciem.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

- Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi Azure Reservations, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
- [Wyświetlanie transakcji rezerwacji](view-reservations.md)
- [Pobieranie transakcji rezerwacji i użycia za pośrednictwem interfejsu API](reservation-apis.md)
- [Zarządzanie rezerwacjami](manage-reserved-vm-instance.md)
