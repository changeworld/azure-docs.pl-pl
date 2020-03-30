---
title: Statystyki sprzedającego – często zadawane pytania
description: Często zadawane pytania dotyczące funkcji Statystyk sprzedawcy w portalu Cloud Partner Portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 011558baa43ee3db2803e9229d1d15df5158d668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285388"
---
<a name="seller-insights-faq"></a>Statystyki sprzedającego – często zadawane pytania
===================

Ten artykuł zawiera wskazówki dotyczące typowych procedur użytkownika w ramach statystyk sprzedawcy oraz pytania dotyczące statystyk sprzedawcy.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Znajdowanie definicji wartości w pobranym pliku transakcji
------------------------------------------------------------------

Definicje wartości metryk w pliku transakcji znajdują się w artykule [Definicje statystyk sprzedawcy](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Zobacz dane klienta dotyczące transakcji, za które zostałem zapłacony
-------------------------------------------------------------

Po pobraniu transakcji z modułu Wypłata znajdź kolumnę z etykietą **Status wypłaty**i zastosuj filtr, aby wyświetlić tylko wartość "Wypłacone". Pojawią się następujące kolumny zawierające dane klienta: **Nazwa firmy,** **Adres e-mail odbiorcy,** **Kraj odbiorcy,** **Stan odbiorcy**i **Kod pocztowy odbiorcy.**


<a name="calculate-my-open-accounts-receivable"></a>Obliczanie moich otwartych rozrachunków z odbiorcami
-------------------------------------

Po pobraniu transakcji z modułu Wypłata znajdź kolumnę o statusie **wypłaty**i zastosuj filtr, aby wyświetlić tylko wartość "Nadchodząca wypłata" i "Nie jest gotowa do wypłaty". Następnie zsumuj kolumnę z etykietą **Kwota wypłaty (PC)**.


<a name="calculate-revenue-by-customer-usage-period"></a>Obliczanie przychodów według okresu użytkowania klienta
------------------------------------------

Po pobraniu transakcji z modułu Wypłata znajdź kolumnę o statusie **transakcji**i przefiltruj wartość "Zapłacone".   Dla każdej wymienionej transakcji kolumna o **nazwie Kwota wypłaty (PC)** reprezentuje kwotę, którą zapłacono.  Aby oszacować okres użytkowania skojarzony z transakcją, należy użyć kolumny **Data obciążenia**, która jest zbliżonym przybliżeniem ostatniego dnia użycia dla okresu, do którego transakcja ma zastosowanie.


<a name="calculate-your-bad-debt"></a>Oblicz swój nieściągalny dług
---------------------

Po pobraniu transakcji z modułu Wypłata zlokalizuj kolumnę o **statusie ostatecznej kolekcji**i zastosuj filtr, aby wyświetlić tylko wartość "Odpis". Następnie zsumuj kolumnę z etykietą **Kwota wypłaty (PC)**.


<a name="view-payout-or-customer-contact-information"></a>Wyświetlanie informacji o wypłatach lub kontaktach klienta
-------------------------------------------

Zaloguj się jako użytkownik z rolą "właściciel", a nie z rolą "współautor". Tylko rola właściciela zobaczy informacje o wypłatach i klientach. Więcej informacji na temat ról użytkowników można znaleźć w artykule [Zarządzanie użytkownikami](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Oblicz moje wypłaty z góry
----------------------------

Po pobraniu transakcji z modułu Wypłata zlokalizuj kolumnę o **nazwie Typ transakcji**i zastosuj filtr, aby wyświetlić tylko wartość "Opłata". Następnie zlokalizuj kolumnę z etykietą **Stan kolekcji końcowej**i zastosuj filtr, aby wyświetlić tylko wartość "W toku". Na koniec zsumuj kolumnę **Kwota wypłaty (PC),** aby obliczyć wszystkie zaliczki wypłacone przed pobraniem od odbiorcy.


<a name="calculate-customer-refunds"></a>Obliczanie zwrotów od odbiorców
--------------------------

Po pobraniu transakcji z modułu Wypłata znajdź kolumnę z etykietą **Stan ostatecznej kolekcji**i zastosuj filtr, aby wyświetlić tylko wartość "Zwrot". Zsumuj kolumnę **Kwota naliczania (PC),** aby obliczyć wszystkie zwroty przetworzone dla klientów.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Określanie, które transakcje dotyczyły partnera usługi Microsoft Channel Partner
----------------------------------------------------------------

Wszystkie transakcje w kolumnie **Typ licencji platformy Azure,** które są filtrowane w celu wyświetlenia wartości "Enterprise through Reseller" i "Cloud Solution Provider" obejmują partnera usługi Microsoft Channel. Aby uzyskać więcej informacji na temat partnera, możesz znaleźć jego **nazwę sprzedawcy** i **adres e-mail sprzedawcy w pobraniu** modułu wypłat i pobraniu modułu Klienta.


<a name="identify-trial-usage-and-trial-conversions"></a>Identyfikowanie użycia próbnego i konwersji próbnych
------------------------------------------

Pobieranie modułu Zamówienia, Użycia i Wypłaty zawiera teraz **datę zakończenia okresu próbnego,** która pomaga zrozumieć, kiedy zakończył się okres próbny dla tego konkretnego zamówienia, w stosownych przypadkach. Aby wyświetlić użycie próbne i zamówienia, znajdź kolumnę **Typ rozliczenia jednostki SKU** w pobranych plikach i zastosuj filtr, aby wyświetlić tylko wartość "Wersja próbna". Aby wyświetlić konwersje próbne, znajdź kolumnę **Data zakończenia wersji próbnej** w plikach do pobrania i zastosuj filtr tylko do wyświetlania zamówień, gdy **data zakończenia procesu** jest późniejsza, a kolumna **Data anulowania** jest pusta lub późniejsza niż **data zakończenia procesu.**


<a name="when-is-my-monthly-payout-calculated"></a>Kiedy obliczana jest moja miesięczna wypłata
------------------------------------

Twoje wypłaty są wydawane do 15 dnia każdego miesiąca dla wszystkich kwot gotowych do wypłaty do ostatniego dnia kalendarzowego poprzedniego miesiąca. Trzeciego dnia miesiąca firma Microsoft obliczy kwotę wypłaty za poprzedni miesiąc i zaktualizuje wszystkie stosowne transakcje opłat w pobraniu za pomocą "Nadchodzącej wypłaty" w kolumnie **Status wypłaty.** Transakcje te pozostaną w tym stanie do momentu wysłania żądania płatności na Twoje konto bankowe, w którym to czasie ich **status wypłaty** zostanie zaktualizowany do "Wypłacone", a "Data wypłaty" zostanie zaktualizowana, aby pokazać datę złożenia wniosku o płatność do Twojego banku.


<a name="calculate-customer-acquisition-and-loss"></a>Obliczanie pozyskiwania i strat klientów
---------------------------------------

Możesz zobaczyć datę, kiedy klient po raz pierwszy kupił jedną z twoich ofert, lokalizując kolumnę **Data nabyta** w pobraniu klienta. Podobnie możesz zobaczyć datę, po której nie mają już żadnej oferty opublikowanej przez Ciebie, lokalizując kolumnę **Data utracona** w pobraniu klienta.


<a name="finding-more-help"></a>Znajdowanie większej pomocy
-----------------

- [Definicje statystyk sprzedawcy](./si-insights-definitions-v4.md) — znajdowanie definicji danych i danych

- [Wprowadzenie do statystyk sprzedawcy](./si-getting-started.md) — wprowadzenie do funkcji Statystyka sprzedawcy.

