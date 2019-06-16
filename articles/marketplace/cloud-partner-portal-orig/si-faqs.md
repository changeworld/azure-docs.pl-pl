---
title: Sprzedawcy Insights — często zadawane pytania
description: Często zadawane pytania dotyczące funkcji sprzedawcy szczegółowe informacje o portalu Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 2719b6b47225576f2eadeb5e5b40b3aa7b39444d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943095"
---
<a name="seller-insights-faq"></a>Sprzedawcy Insights — często zadawane pytania
===================

Ten artykuł zawiera wskazówki dotyczące typowych procedur użytkownika w ramach i pytania dotyczące usługi Insights sprzedawcy.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Zawiera definicje dla wartości w pliku pobranego transakcji
------------------------------------------------------------------

Definicje metryk wartości w pliku transakcji znajdują się w artykule [definicje Insights sprzedawcy](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Zobacz szczegóły klienta transakcji, dla których zostały I czy została zapłacona
-------------------------------------------------------------

Po pobraniu transakcji z modułu informacje dotyczące wypłat Znajdź kolumnę z etykietą **stan informacje dotyczące wypłat**i Zastosuj filtr, aby wyświetlić tylko wartości "Płatne Out". Pojawi się następujące kolumny zawierające szczegóły klienta: **Nazwa firmy**, **klienta poczty E-mail**, **kraju klienta**, **stanu klienta**, i **kod pocztowy klienta**.


<a name="calculate-my-open-accounts-receivable"></a>Oblicz Moje Otwórz odbiorców
-------------------------------------

Po pobraniu transakcji z modułu informacje dotyczące wypłat Znajdź kolumnę z etykietą **stan informacje dotyczące wypłat**i Zastosuj filtr, aby wyświetlić tylko wartości "Nadchodzące informacje dotyczące wypłat" i "Nie jest gotowy do informacje dotyczące wypłat." Następnie Suma kolumnę z etykietą **kwoty wypłata (PC)** .


<a name="calculate-revenue-by-customer-usage-period"></a>Oblicz przychód według okresu użycia klienta
------------------------------------------

Po pobraniu transakcji z modułu informacje dotyczące wypłat Znajdź kolumnę z etykietą **stan transakcji**i wartość "Płatne" filtru.   Dla każdej transakcji wymienionej kolumnę z etykietą **kwoty wypłata (PC)**  reprezentuje zostały zapłaconej kwoty.  Aby oszacować okres użycia skojarzony z transakcją, można użyć kolumny **Data obciążenia**, który jest bliskie zbliżenia ostatni dzień użycia okres, do której stosują się transakcji.


<a name="calculate-your-bad-debt"></a>Oblicz zadłużenia niewłaściwe
---------------------

Po pobraniu transakcji z modułu informacje dotyczące wypłat Znajdź kolumnę z etykietą **ostateczny stan kolekcji**i Zastosuj filtr tylko do wyświetlania wartości "Write wyłączone." Następnie Suma kolumnę z etykietą **kwoty wypłata (PC)** .


<a name="view-payout-or-customer-contact-information"></a>Wyświetl informacje dotyczące wypłat lub klienta informacje kontaktowe
-------------------------------------------

Zaloguj się jako użytkownik mający rolę "owner" i nie rolę "Współautor". Rolę właściciela, zostaną wyświetlone informacje informacje dotyczące wypłat i klientów. Możesz dowiedzieć się więcej na temat ról użytkownika w artykule [Zarządzanie użytkownikami](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Oblicz Moje wypłaty zaawansowane
----------------------------

Po pobraniu transakcji z modułu informacje dotyczące wypłat Znajdź kolumnę z etykietą **typu transakcji**i Zastosuj filtr, aby wyświetlić tylko wartości "Opłat". Następnie odszukaj kolumnę z etykietą **ostateczny stan kolekcji**i Zastosuj filtr, aby wyświetlić tylko wartości "W toku". Na koniec suma **kwoty wypłata (PC)** kolumn do obliczania postępu wszystkich płatnych użytkownikowi przed kolekcji z klienta.


<a name="calculate-customer-refunds"></a>Oblicz zwroty klienta
--------------------------

Po pobraniu transakcji z modułu informacje dotyczące wypłat Znajdź kolumnę z etykietą **ostateczny stan kolekcji**i Zastosuj filtr, aby wyświetlić tylko wartości "Zwrot". Suma **dodatkowy kwotę (PC)** kolumn do obliczania zwrotów przetwarzane dla swoich klientów.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Identyfikowanie transakcji związane z partnerem firmy Microsoft kanału
----------------------------------------------------------------

Wszystkie transakcje w kolumnie **typu licencji Azure** filtrowania do wyświetlania wartości "Przedsiębiorstwa za pośrednictwem odsprzedawcy" i "Cloud Solution Provider" obejmują kanału partnera firmy Microsoft. Aby uzyskać więcej informacji dotyczących partnera, możesz znaleźć ich **nazwa odsprzedawcy** i **adres E-mail** pobierania modułu informacje dotyczące wypłat i pobieranie modułu klienta.


<a name="identify-trial-usage-and-trial-conversions"></a>Identyfikowanie próbnego użycia i konwersji z wersji próbnej
------------------------------------------

Kolejność, użycia i informacje dotyczące wypłat modułu pliki do pobrania zawierają teraz **wersji próbnej Data zakończenia** ułatwiające zrozumienie, kiedy okres próbny zakończył się dla tej konkretnej kolejności, jeśli ma to zastosowanie. Aby zobaczyć próbnego użycia i zamówienia, zlokalizuj **typ rozliczeń jednostki SKU** kolumny w plikach do pobrania i Zastosuj filtr, aby wyświetlić tylko wartości "Wersja próbna." Aby wyświetlić konwersji z wersji próbnej, zlokalizuj **wersji próbnej Data zakończenia** kolumny w plikach do pobrania i Zastosuj porządkuje filtru w celu wyświetlenia tylko, gdy **wersji próbnej Data zakończenia** osiągnęła bieżącą datę i **anulować data** kolumna jest pusta lub późniejsza niż **Data końcowa wersja próbna**.


<a name="when-is-my-monthly-payout-calculated"></a>Kiedy są obliczane Moje miesięczne informacje dotyczące wypłat
------------------------------------

Twoje wypłaty wydania do Ciebie przez 15 dnia każdego miesiąca dla wszystkich kwot gotowe, aby uzyskać informacje dotyczące wypłat przez ostatni dzień poprzedniego miesiąca kalendarzowego. Trzeciego dnia miesiąca, firma Microsoft obliczania kwoty wypłata poprzedniego miesiąca i zaktualizuj wszystkie transakcje odpowiednie opłaty w pobranego pliku z "Nadchodzące informacje dotyczące wypłat" w **stan informacje dotyczące wypłat** kolumny. Tych transakcji pozostanie w tym stanie do momentu żądanie płatności są wysyłane do konta bankowego co ich **stan informacje dotyczące wypłat** zostanie zaktualizowany do "Płatnych limit" i "Informacje dotyczące wypłat daty" zostanie zaktualizowane w celu wyświetlenia daty, możemy przesłane żądanie płatności bank.


<a name="calculate-customer-acquisition-and-loss"></a>Obliczanie zdobywanie klientów i strat
---------------------------------------

Możesz zobaczyć datę, kiedy klienta najpierw zakupionych w jednej z ofert, znajdując **nabyte data** kolumny do pobrania klient. Podobnie, można zobaczyć datę, po upływie którego już nie mieli oni dowolnej oferty, które zostały opublikowane przez Ciebie, znajdując **Data utraty** kolumny do pobrania klient.


<a name="finding-more-help"></a>Znajdowania dodatkowej pomocy
-----------------

- [Definicje Insights sprzedawcy](./si-insights-definitions-v4.md) — zawiera definicje metryki i dane

- [Wprowadzenie do usługi Insights sprzedawcy](./si-getting-started.md) — wprowadzenie do funkcji sprzedawcy szczegółowych informacji.

