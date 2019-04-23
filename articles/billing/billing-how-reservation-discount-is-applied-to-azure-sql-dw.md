---
title: Jak rezerwacji rabaty dotyczą usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rezerwacji rabaty dotyczą usługi Azure SQL Data Warehouse umożliwia oszczędzanie pieniędzy.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 10e19377d31489cd19465fe6171ffb530bd58c28
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014219"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Jak rezerwacji rabaty dotyczą usługi Azure SQL Data Warehouse

Po możesz kupić pojemność usługi Azure SQL Data Warehouse zarezerwowane, rabat związany z rezerwacją jest automatycznie stosowany do magazynów danych, które istnieją w tym regionie. Rabat związany z rezerwacją dotyczy użycia emitowanego przez licznik cDWU SQL Data Warehouse. Magazynu i sieci są naliczane według stawek zgodnie z rzeczywistym użyciem.

## <a name="reservation-discount-application"></a>Stosowania rabatów dotyczących rezerwacji

Rabat w wysokości rezerwowanie pojemności SQL Data Warehouse jest stosowany do uruchamiania magazynów w systemie godzinowym. Jeśli nie masz magazyn wdrożony na godzinę dla tej godziny zostanie zmarnowane rezerwowanie pojemności. Go nie jest przenoszone.

Po zakupie rezerwacji, który można kupić jest dopasowywany do użytkowaniu SQL Data Warehouse, uruchamiając magazynów w dowolnym momencie w czasie. Wyłączenie niektórych magazynów następnie rabaty rezerwacji automatycznie zastosować do innych magazynów dopasowania.

Dla magazynów, które nie korzystają z do pełnych godzin rezerwacja jest automatycznie stosowany do innych zgodnych wystąpień w ciągu tej godziny.

## <a name="discount-examples"></a>Przykłady z rabatami

W poniższych przykładach pokazano, jak usługa SQL Data Warehouse rabat rezerwowanie pojemności ma zastosowanie w zależności od wdrożenia.

- **Przykład 1**: Możesz kupić 5 jednostek pojemności 100 cDWU zastrzeżone. Wystąpienie DW1500c SQL Data Warehouse możesz uruchomić na godzinę. W tym przypadku jest informacje o użytkowaniu generowane dla jednostek 15 użytkowania cDWU 100. Rabat związany z rezerwacją ma zastosowanie do 5 jednostek, które były używane. Opłaty są naliczane przy użyciu stawki dla pozostałych 10 jednostek, 100 użycia cDWU, która została użyta.

- **Przykład 2**: Możesz kupić 5 jednostek pojemności 100 cDWU zastrzeżone. Dwa wystąpienia DW100c SQL Data Warehouse możesz uruchomić na godzinę. W takim przypadku dwa zdarzenia użycia są emitowane dla 1 jednostki 100 cDWU użycia. Zarówno zdarzeń użycia Uzyskaj rabaty rezerwowanie pojemności. Pozostałe 3 jednostki 100 pojemności cDWU zastrzeżone są zmarnowany i nie jest przenoszone do użytku w przyszłości.

- **Przykład 3**: Możesz kupić 1 jednostka mocy 100 cDWU zastrzeżone. Możesz uruchomić dwa wystąpienia DW100c SQL Data Warehouse. Każdy działa przez 30 minut. W takim przypadku zarówno zdarzeń użycia Uzyskaj rabaty rezerwowanie pojemności. Nie użycia jest naliczana przy użyciu stawek zgodnie z rzeczywistym użyciem.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

- Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:

- [Co to jest Azure rezerwacje?](billing-save-compute-costs-reservations.md)
- [Wyświetl transakcje rezerwacji](billing-view-reservations.md)
- [Transakcje rezerwacji i użycia za pomocą interfejsu API](billing-reservation-apis.md)
- [Zarządzanie rezerwacji](billing-manage-reserved-vm-instance.md)
