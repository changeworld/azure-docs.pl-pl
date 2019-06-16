---
title: Oferty tworzenia listy kontrolnej - komercyjnych witryny Marketplace dla platformy Azure
description: Szczegółowe informacje podawane w procesie tworzenia oferty. -Komercyjnych witryny Marketplace dla platformy Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 904058c2c98c8ded2ea9c91e8aa7ec595aa49b05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481447"
---
# <a name="offer-creation-checklist"></a>Lista kontrolna tworzenia oferty

Proces tworzenia oferty wykonasz wiele stron. Poniżej przedstawiono szczegółowe informacje podane na każdej stronie, wraz z łączami do dalszych informacji na temat każdego elementu.

Elementy, które jest wymagane do zapewnienia lub określ zostały podane poniżej. Niektóre obszary są opcjonalne lub domyślne wartości podane, można zmienić zgodnie z potrzebami. Nie trzeba pracować nad te sekcje, w kolejności podanej poniżej.

| **Element**    | **Cel**  |
| :---------- | :-------------------|
| [**Nowe oferty modalne**](#new-offer-modal) | Zbiera liczbę zapewniają informacje o tożsamości.  |
| [Strona Ustawienia oferty](#offer-setup-page) | Umożliwia zgadzaj się na korzystanie z kluczowych funkcji, a następnie wybierz sposób sprzedaży oferty firmy Microsoft.  |
| [Strona właściwości](#properties-page) | Definiowanie kategorii i branż, używane do grupowania Twoją ofertę w rynków umów obsługi oferty i wersji aplikacji. |
| [Oferuje listę stron](#offer-listing-page) | Zdefiniuj szczegóły oferty, które mają być wyświetlane w portalu marketplace, w tym opis oferty i zasobów marketingowych. |
| [Strona (wersja zapoznawcza)](#preview-page) | Zdefiniuj ograniczonej grupie osób (wersja zapoznawcza) w celu zwalniania oferty przed opublikowaniem oferty na żywo do szerszego audience(s) portalu marketplace. |
| [Strona konfiguracji technicznych oferty](#technical-configuration-page)  | Dostępne tylko jeśli wybierzesz do sprzedaży oferty firmy Microsoft. Zdefiniuj szczegóły techniczne (Ścieżka adresu URL elementu webhook, identyfikator dzierżawy i identyfikator aplikacji) używany do łączenia z ofertą. |
| [**Nowy Plan modalne**](#plan-identity-modal) | Należy zaplanować zbiera informacje o tożsamości.  |
| [Planowanie strony](#plan-listing-page)  | Dostępne tylko jeśli wybierzesz do sprzedaży oferty firmy Microsoft. Zdefiniuj szczegóły używany do tworzenia listy Plan w witrynie marketplace.  |
| [Ceny za plan i stronicowe dostępności](#plan-pricing--availability-page)  | Dostępne tylko jeśli wybierzesz do sprzedaży oferty firmy Microsoft.  Gromadzi informacje o właściwości business (model cen), odbiorców i rynku dostępność w każdym planie (wersja) oferty.  |
| [Test stacji strony](#test-drive-listing-page)  | Dostępne tylko jeśli wybierzesz zaoferować wersję testową oferty. Zdefiniuj szczegóły używane do listy testów na dysku w portalu marketplace.  |
| Strony konfiguracji technicznej dysku testowej  | Dostępne tylko jeśli wybierzesz zaoferować wersję testową oferty. Zdefiniuj szczegóły techniczne demonstracyjne (lub "wersja testowa"), które umożliwi klientom wypróbować ofertę przed zatwierdzeniem dokonać zakupu.  |
| [Przejrzyj i strona publikowania](#review-and-publish-page)  | Wybierz zmiany, które chcesz opublikować, wyświetlany jest stan każdej stronie i wpisz notatki, aby zespół certyfikacji.  |


## <a name="new-offer-modal"></a>Nowa oferta modalne 

Pierwszy rodzajów informacji, które możesz może zostać poproszony o podanie są nazwa i identyfikator oferty. 

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Identyfikator oferty  | Wymagane, nie można zmienić po utworzeniu. Maksymalnie 50 znaków i musi zawierać tylko znaków alfanumerycznych, małe litery, łączniki i podkreślenia. |
| Nazwa oferty  | Wymagany. |

## <a name="offer-setup-page"></a>Strona Ustawienia oferty

Strony konfiguracji oferty to, gdzie użytkownik może zdecydować się na różnych kanałów i ruchy sprzedaży, a także zadeklarować, że korzystanie z kluczowych funkcji, takich jak wersji testowej i odbiorcy potencjalnych klientów. 

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------|  
| Czy chcesz sprzedawać za pośrednictwem firmy Microsoft?  | Wymagany. Wartość domyślna: Tak |
| Jak chcesz potencjalnym klientom interakcję z ofertą wystawienie? (Wywołanie akcji)  | Wymagany, jeśli nie sprzedaży firmy Microsoft. Wartość domyślna: Bezpłatna wersja próbna, opcje: "Pobierz ją teraz", "Bezpłatna wersja próbna", "Kontakt ze mną." |
| Adres URL w wersji próbnej  | Wymagane, jeśli wybrano opcję "Bezpłatna wersja próbna", jak klienci sposób powinny wchodzić w interakcje z listą oferty. |
| Adres URL oferty  | Wymagane, jeśli "opcji Pobierz teraz" jest zaznaczone, jak klienci sposób powinny wchodzić w interakcje z listą oferty |
| Kanały  | Opcjonalny. Wartość domyślna: Brak zgody na kanale dostawcy usług Kryptograficznych (odsprzedawcy).  |
| Wersja testowa | Opcjonalny. Wartość domyślna: Nie włączono wersję testową.  |
| Typ wersji testowej | Wymagany, jeśli włączono wersję testową. Wartość domyślna: Niczego nie wybrano. Opcje: Usługa Azure Resource Manager, Dynamics 365 dla firm siedziby, Dynamics 365 for Customer Engagement, Dynamics 365 dla operacji, aplikację logiki, usługa Power BI.  |
| Zarządzanie potencjalnymi klientami — łączą się z systemem CRM | Wymagane czy sprzedaży firmy Microsoft, czy lista oferuje jako "Kontakt ze mną." Wartość domyślna: nie połączenia systemu CRM. Opcje CRM: Tabela platformy Azure, obiektów blob platformy Azure, Dynamics CRM online, punkt końcowy HTTPs, Marketo, Salesforce  |

## <a name="properties-page"></a>Strona właściwości

Na stronie właściwości jest, gdzie należy zdefiniować kategorie i branż, używane do grupowania Twoją ofertę w rynków umów obsługi oferty i wersji aplikacji. Pamiętaj zapewnić dokładne i kompletne szczegółowe informacje o ofercie na tej stronie, tak, aby wyświetlane prawidłowo i oferowany odpowiednie grupy klientów. 

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------|  
| Kategorii i podkategorii | Wymagane 3-1 lub maksymalnego rozmiaru. Wartość domyślna: Niczego nie wybrano. |
| Branż i subindustries | Opcjonalny. maksymalnie 2 L1 branż i maksymalnie 2 subindustries w każdej branży L1 domyślne: Niczego nie wybrano |
| Wersja aplikacji  | Opcjonalny. Wartość domyślna: Brak. |
| Użyj standardowej umowy  | Opcjonalny. Wartość domyślna: nie wybrano.  | |
| Warunki użytkowania  | Wymagane, jeśli nie wybrano standardowej umowy.  |

## <a name="offer-listing-page"></a>Oferuje listę stron

Na stronie listy jest określane tekstu i obrazów, spotykane przez klientów podczas przeglądania listy swoją ofertę w portalu marketplace. 

| **Nazwa pola**    | **Uwagi**   |
| :---------------- | :-----------| 
| Name (Nazwa)  | Wymagane, maks 50 znaków. |
| Podsumowanie  | Wymagane, maks 100 znaków. | 
| Opis  | Wymagane, maks 3000 znaki. |
| Instrukcje z wprowadzeniem  | Wymagane, maks 3000 znaki. |
| Instrukcje z wprowadzeniem  | Wymagane, maks 3000 znaki. |
| Słowa kluczowe do wyszukania  | Opcjonalne, zalecane maksymalnie 3 słowa kluczowe. |
| Adres URL zasad ochrony prywatności  | Wymagany. |
| URL materiały marketingowe Program dostawcy usług Kryptograficznych  | Opcjonalny. |
| Przydatne linki, tytuł i adres URL  | Opcjonalny. |
| Obsługa dokumentów tytuł + pliku  | Wymagana minimalna 1 i maksymalnej liczby 3. Musi mieć format pliku PDF. |
| Zrzuty ekranu  | Wymagane, zrzut ekranu min 1 i max 5; cztery lub więcej (zalecane). Musi być w formacie PNG 1280 X 720. |
| Logo Store (małe, średnie, duże, sieci, element Hero)  | Mała liczba godzin (48 X 48) i dużym (216 X 216) wymagane; o innych rozmiarach opcjonalny, ale zalecane: Medium (90 x 90), Wide (255 x 115), Hero (815 x 290). Musi być w formacie PNG. |
| Nazwa wideo + adres URL miniatury  | Opcjonalne, zalecane maksymalnie 4 filmy wideo. Miniatura musi być 1280 x 720 w formacie PNG. Wideo musi być hostowany w usłudze YouTube lub Vimeo. |
| Kontakty (dostawcy usług Kryptograficznych programu, inżynierii, pomocy technicznej)  | Inżynieria i pomocy technicznej, skontaktuj się z pomocą wymagane (nazwa, adres e-mail i numer telefonu); CSP Program skontaktuj się z opcjonalny, ale zalecane. |
| Adres URL pomocy technicznej  | Wymagany. |

## <a name="preview-page"></a>Strona (wersja zapoznawcza)

Na stronie (wersja zapoznawcza) określa się tam odbiorców, aby mieć dostęp do oferty w wersji zapoznawczej, aby sprawdzić, czy oferty spełnia wszystkie wymagania dotyczące przed jego przejściem na żywo. 

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------| 
| Wiadomość e-mail z usługi AAD/MSA i opis | Wymagana min 1 i max 10, jeśli wprowadzone ręcznie lub maksymalnie 20 w przypadku przekazywania pliku CSV. |

## <a name="technical-configuration-page"></a>Strona konfiguracji Technical Preview 

Na stronie konfiguracji techniczne określa się tam szczegóły techniczne używane przez firmę Microsoft, aby nawiązać połączenie z ofertą. Ta strona nie jest widoczna dla Ciebie, jeśli nie chcesz sprzedawać za pośrednictwem firmy Microsoft.

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Adres URL strony | Wymagany, jeśli sprzedaży firmy Microsoft. |
| Połączenie elementu webhook | Wymagany, jeśli sprzedaży firmy Microsoft. |
| Identyfikator dzierżawy usługi Azure AD | Wymagany, jeśli sprzedaży firmy Microsoft. |
| Identyfikator aplikacji usługi Azure AD | Wymagany, jeśli sprzedaży firmy Microsoft. |

## <a name="plan-identity-modal"></a>Plan tożsamości modalne

Pierwszy rodzajów informacji, który zostanie wyświetlony monit, aby zapewnić są nazwa i identyfikator planu. Ta strona nie jest widoczne, jeśli nie zamierzasz sprzedaży firmy Microsoft.

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Identyfikator planu  | Wymagany, jeśli sprzedaży firmy Microsoft. Nie można zmienić po utworzeniu. Maksymalnie 50 znaków i musi zawierać tylko znaków alfanumerycznych, małe litery, łączniki i podkreślenia. |
| Nazwa planu  | Wymagany, jeśli sprzedaży firmy Microsoft. Musi być unikatowa we wszystkich planów w ramach oferty. Maksymalna liczba 50 znaków. |

## <a name="plan-listing-page"></a>Planowanie strony

Plan stronie listy jest określane tekstu dla klientów zobaczyć, wyświetlając planem w portalu marketplace. Ta strona nie jest widoczna dla Ciebie, jeśli nie chcesz sprzedawać za pośrednictwem firmy Microsoft.

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Opis planu   | Wymagany, jeśli sprzedaży firmy Microsoft. Maksymalna liczba 500 znaków. | |

## <a name="plan-pricing--availability-page"></a>Stronę cennika i dostępność planu

Określa właściwości biznesowych, odbiorców i rynku dostępności w każdym planie (wersja) oferty się stronę cennika i dostępność planu. Ta strona nie jest widoczna dla Ciebie, jeśli nie chcesz sprzedawać za pośrednictwem firmy Microsoft.

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------| 
| Dostępność na rynku  | Wymagane i min 1 i max 141. |
| Model cen  | Wymagany. Wartość domyślna: Stosowana jest stała stawka. Opcje: Stosowana jest stała stawka za użytkownika. |
| Minimalna i maksymalna liczba miejsc  | Opcjonalne, dostępną tylko, jeśli stanowisko model cen oparty wybrane. |
| Termin rozliczeń  | Wymagany. Wartość domyślna: Miesięczne. Opcje: Miesięczne i roczne. |
| Cena  | Wymagane USD na miesiąc, jeśli miesięczne rozliczenie warunku wybranego; lub USD rocznie, w przypadku rocznych rozliczeń wybrany okres. |
| Planowanie odbiorców  | Opcjonalny. Wartość domyślna: Publiczny plan. Opcje: Publiczne, prywatne za pomocą Identyfikatora dzierżawy |
| Ograniczone odbiorców Plan (identyfikator dzierżawy + opisu)  | Wymagane, jeśli wybrany plan prywatnych. Min 1 i max 10 dzierżawy identyfikatory, jeśli wprowadzone ręcznie. Maksymalna liczba 20000, jeśli import z pliku CSV. |

## <a name="test-drive-listing-page"></a>Test stacji strony

Dostępne tylko jeśli wybierzesz zaoferować wersję testową oferty. Zdefiniuj szczegóły używane do listy testów na dysku w portalu marketplace.

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------| 
| Opis  | Wymagany. |
| Ręczne nazwy użytkownika i plików  | Wymagane, maks doc 1. Musi być w formacie PDF. |
| Nazwa klipu wideo, adres URL + miniatury  | Opcjonalne, zalecane. Miniatura musi być 533 x 324 w formacie JPGP lub PNG. Wideo musi być hostowany w usłudze YouTube lub Vimeo. |

## <a name="review-and-publish-page"></a>Przejrzyj i strona publikowania

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------| 
| Uwagi dotyczące certyfikacji  | Opcjonalny. |

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie nowej oferty SaaS](./create-new-saas-offer.md)
