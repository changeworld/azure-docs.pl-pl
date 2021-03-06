---
title: Stosowanie rabatów na rezerwacje usługi Azure SQL Data Warehouse | Microsoft Docs
description: Dowiedz się, w jaki sposób rabaty na rezerwacje stosowane względem usługi Azure SQL Data Warehouse pomagają zaoszczędzić pieniądze.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: b08f11799c1471af22138fefcd57ed1eb951a1a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199385"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Stosowanie rabatów na rezerwacje usługi Azure SQL Data Warehouse

Po zakupie pojemności zarezerwowanej usługi Azure SQL Data Warehouse rabat dotyczący rezerwacji jest automatycznie stosowany do magazynów danych istniejących w danym regionie. Rabat dotyczący rezerwacji ma zastosowanie do użycia emitowanego przez miernik cDWU usługi SQL Data Warehouse. Opłaty za magazyn i sieć są naliczane według stawek płatności zgodnie z rzeczywistym użyciem.

## <a name="reservation-discount-application"></a>Stosowanie rabatu na rezerwację

Rabat na pojemność zarezerwowaną usługi SQL Data Warehouse jest stosowany względem działających magazynów z rozliczeniem godzinowym. Jeśli magazyn nie będzie wdrożony przez godzinę, pojemność zarezerwowana zostanie utracona dla tej godziny. Niewykorzystane wartości nie są przenoszone na następne okresy.

Po zakupie rezerwacja jest dopasowywana do użycia usługi SQL Data Warehouse emitowanego przez działające magazyny w dowolnym momencie. Jeśli wyłączysz niektóre magazyny, rabaty na rezerwacje będą automatycznie stosowane do pozostałych zgodnych magazynów.

W przypadku magazynów, które pozostają wyłączone przez pełną godzinę, rezerwacja jest automatycznie stosowana do innych zgodnych wystąpień podczas tej godziny.

## <a name="discount-examples"></a>Przykłady rabatów

W poniższych przykładach pokazano, jak jest stosowany rabat na pojemność zarezerwowaną usługi SQL Data Warehouse w zależności od wdrożeń.

- **Przykład 1**: Zakupiono 5 jednostek po 100 obliczeniowych jednostek magazynu danych (cDWU) pojemności zarezerwowanej. Uruchomiono na jedną godzinę wystąpienie usługi SQL Data Warehouse DW1500c. W takim przypadku użycie jest emitowane dla 15 jednostek po 100 jednostek cDWU użycia każda. Rabat związany z rezerwacją dotyczy 5 wykorzystywanych jednostek. Opłata jest naliczana według stawek płatności zgodnie z rzeczywistym użyciem dla pozostałych 10 jednostek po 100 jednostek cDWU użycia każda. Innymi słowy, w przypadku wielu rezerwacji jest możliwe częściowe pokrycie.

- **Przykład 2**: Zakupiono 5 jednostek po 100 obliczeniowych jednostek magazynu danych (cDWU) pojemności zarezerwowanej. Uruchomiono na jedną godzinę dwa wystąpienia usługi SQL Data Warehouse DW100c. W takim przypadku dwa zdarzenia użycia są emitowane dla 1 jednostki obejmującej 100 jednostek cDWU użycia. Oba zdarzenia użycia mają rabaty dotyczące pojemności zarezerwowanej. Pozostałe 3 jednostki po 100 jednostek cDWU pojemności zarezerwowanej każda są tracone i nie są przenoszone do użytku w przyszłości. Innymi słowy, pojedyncze rezerwacje można dopasować do wielu wystąpień usługi SQL Data Warehouse.

- **Przykład 3**: Zakupiono 1 jednostkę obejmującą 100 obliczeniowych jednostek magazynu danych (cDWU) pojemności zarezerwowanej. Uruchomiono dwa wystąpienia usługi SQL Data Warehouse DW100c. Każdy przebieg trwał 30 minut. W takim przypadku oba zdarzenia użycia mają rabaty dotyczące pojemności zarezerwowanej. Nie jest naliczana opłata za użycie według stawek płatności zgodnie z rzeczywistym użyciem.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

- Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
- [Wyświetlanie transakcji rezerwacji](view-reservations.md)
- [Pobieranie transakcji rezerwacji i użycia za pośrednictwem interfejsu API](reservation-apis.md)
- [Zarządzanie rezerwacjami](manage-reserved-vm-instance.md)
