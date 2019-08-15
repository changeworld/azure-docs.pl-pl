---
title: Lista kontrolna tworzenia oferty SaaS — Marketing komercyjny dla platformy Azure
description: Szczegóły, jakie można podać w procesie tworzenia oferty SaaS. — Komercyjne witryny Marketplace na platformie Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 66c28039f9126ed9e3f56c3ac15b1b3d82279b64
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036379"
---
# <a name="saas-offer-creation-checklist"></a>Lista kontrolna tworzenia oferty SaaS

Proces tworzenia oferty SaaS przeprowadzi Cię przez wiele stron. Poniżej znajdują się szczegóły, które można podać na każdej stronie, z linkami do dodatkowych informacji na temat poszczególnych elementów.

Elementy wymagane do podania lub określenia są wymienione poniżej. Niektóre obszary są opcjonalne lub mają podane wartości domyślne, które można zmienić zgodnie z potrzebami. Nie musisz już korzystać z tych sekcji w podanej kolejności.

| **Element**    | **Cel**  |
| :---------- | :-------------------|
| [**Nowa oferta — modalne**](#new-offer-modal) | Zbiera informacje o tożsamości oferty.  |
| [Strona konfiguracji oferty](#offer-setup-page) | Umożliwia korzystanie z kluczowych funkcji i wybór sposobu sprzedaży oferty przez firmę Microsoft.  |
| [Strona właściwości](#properties-page) | Zdefiniuj kategorie i branże używane do grupowania oferty na rynkach Marketplace, umowy prawne wspierające Twoją ofertę i wersję aplikacji. |
| [Strona z listą ofert](#offer-listing-page) | Zdefiniuj szczegóły oferty do wyświetlania w portalu Marketplace, w tym opisy oferty i zasobów marketingowych. |
| [Strona podglądu](#preview-page) | Zdefiniuj ograniczonego odbiorcę w wersji zapoznawczej, aby wypróbować ofertę przed opublikowaniem oferty w szerszym gronie odbiorców w portalu Marketplace. |
| [Strona konfiguracji technicznej oferty](#technical-configuration-page)  | Dostępne tylko w przypadku wybrania sprzedaży oferty przez firmę Microsoft. Zdefiniuj szczegóły techniczne (ścieżkę URL, element webhook, identyfikator dzierżawy i identyfikator aplikacji) używane do nawiązania połączenia z ofertą. |
| [**Nowy modalny plan**](#plan-identity-modal) | Zbiera informacje o tożsamości planu.  |
| [Strona aukcji planu](#plan-listing-page)  | Dostępne tylko w przypadku wybrania sprzedaży oferty przez firmę Microsoft. Zdefiniuj szczegóły używane do wyświetlania planu w portalu Marketplace.  |
| [Zaplanuj stronę dostępności & cennika](#plan-pricing--availability-page)  | Dostępne tylko w przypadku wybrania sprzedaży oferty przez firmę Microsoft.  Gromadzi informacje o cechach biznesowej (modelu cen), odbiorcach i dostępności dla każdego planu (wersji) oferty.  |
| [Strona listy dysków testowych](#test-drive-listing-page)  | Dostępne tylko wtedy, gdy wybrano opcję zaoferowania dysku testowego dla oferty. Zdefiniuj szczegóły używane do wyświetlania na liście dysku testowego w portalu Marketplace.  |
| Strona konfiguracji technicznej na dysku testowym  | Dostępne tylko wtedy, gdy wybrano opcję zaoferowania dysku testowego dla oferty. Zdefiniuj szczegóły techniczne dla demonstracji (lub "Test Drive"), która umożliwi klientom wypróbowanie oferty przed zatwierdzeniem jej zakupu.  |
| [Przeglądanie i publikowanie strony](#review-and-publish-page)  | Wybierz zmiany, które chcesz opublikować, zobacz stan poszczególnych stron i podaj uwagi dotyczące zespołu certyfikacji.  |


## <a name="new-offer-modal"></a>Nowa oferta — modalne 

Pierwsze fragmenty informacji, które należy podać, są IDENTYFIKATORem i aliasem oferty. 

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Identyfikator oferty  | Wymagane, nie można zmienić po utworzeniu. Maks. 50 znaków i musi zawierać tylko małe litery, znaki alfanumeryczne, łączniki i podkreślenia. |
| Alias oferty  | Wymagana. |

## <a name="offer-setup-page"></a>Strona konfiguracji oferty

Na stronie Konfiguracja oferty możesz wybrać różne kanały i sprzedawać ruchy, a także zadeklarować korzystanie z najważniejszych funkcji, takich jak Stacja testowa i klient. 

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------|  
| Czy chcesz sprzedawać w firmie Microsoft?  | Wymagana. Domyślne: Tak |
| Jak chcesz, aby potencjalni klienci mogli współdziałać z listą ofert? (Wywołanie do akcji)  | Wymagane, jeśli nie sprzedajesz przez firmę Microsoft. Domyślne: Bezpłatna wersja próbna, opcje: "Pobierz teraz", "bezpłatna wersja próbna", "kontakt ze mną". |
| Adres URL wersji próbnej  | Wymagany, jeśli wybrano opcję "bezpłatna wersja próbna", ponieważ sposób, w jaki klienci powinni korzystać z listy ofert. |
| Adres URL oferty  | Wymagane, jeśli wybrano opcję "Pobierz teraz", ponieważ sposób, w jaki klienci powinni korzystać z listy ofert |
| Kanały  | Opcjonalny. Domyślne: Nie zabrałeś do kanału dostawcy CSP (odsprzedawcy).  |
| Wersja testowa | Opcjonalny. Domyślne: Brak włączonego dysku testowego.  |
| Typ dysku testowego | Wymagane, jeśli włączono dysk testowy. Domyślne: Niczego nie wybrano. Opcje: Azure Resource Manager, Dynamics 365 for Business Central, Dynamics 365 for Customer Engagement, Dynamics 365 for Operations, Logic App, Power BI.  |
| Zarządzanie potencjalnymi klientami — łączenie z systemem CRM | Wymagane, jeśli sprzedajesz przez firmę Microsoft lub jeśli lista zawiera oferty "kontakt ze mną". Wartość domyślna: brak połączonego systemu CRM. Opcje programu CRM: Azure Table, Azure Blob, Dynamics CRM Online, HTTPs, punkt końcowy, Marketo, Salesforce  |

## <a name="properties-page"></a>Strona właściwości

Na stronie właściwości można zdefiniować kategorie i branże używane do grupowania oferty na rynkach Marketplace, umowy prawne wspierające Twoją ofertę i wersję aplikacji. Upewnij się, że podajesz pełne i dokładne szczegółowe informacje o ofercie na tej stronie, aby była ona wyświetlana odpowiednio i oferowana przez właściwy zestaw klientów. 

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------|  
| Kategoria i Podkategoria | Wymagane wartości 1 i maks. 3. Domyślne: Niczego nie wybrano. |
| Branże i podbranże | Opcjonalny. Maksymalna 2 branże L1 i maksymalna 2 podbranża w ramach każdej branży L1, domyślna: Nie wybrano żadnego |
| Wersja aplikacji  | Opcjonalny. Domyślne: Brak. |
| Użyj kontraktu standardowego  | Opcjonalna. Domyślnie: nie wybrano.  | |
| Warunki użytkowania  | Wymagane, jeśli nie wybrano kontraktu standardowego.  |

## <a name="offer-listing-page"></a>Strona z listą ofert

Na stronie lista znajduje się tekst i obrazy widoczne dla klientów podczas wyświetlania listy ofert w portalu Marketplace. 

| **Nazwa pola**    | **Uwagi**   |
| :---------------- | :-----------| 
| Name  | Wymagane, maks. 50 znaków. |
| Podsumowanie  | Wymagane, maks. 100 znaków. | 
| Opis  | Wymagane, maks. 3000 znaków. |
| Instrukcje Wprowadzenie  | Wymagane, maks. 3000 znaków. |
| Instrukcje Wprowadzenie  | Wymagane, maks. 3000 znaków. |
| Wyszukaj słowa kluczowe  | Opcjonalne, zalecane, maksymalnie 3 słowa kluczowe. |
| Adres URL zasad ochrony prywatności  | Wymagana. |
| Adres URL materiałów marketingowych programu CSP  | Opcjonalna. |
| Przydatne linki tytuł + adres URL  | Opcjonalna. |
| Dokument pomocniczy tytuł + plik  | Wymagane, minimum 1 i 3. Musi być formatem pliku PDF. |
| Zrzuty ekranu  | Wymagany, minimalny 1 zrzut ekranu i maksymalnie 5; co najmniej cztery zalecane. Musi mieć 1280 X 720 w formacie PNG. |
| Logo Sklepu (małe, średnie, duże, szerokie, Hero)  | Małe (48 X 48) i duże (216 X 216) wymagane; inne rozmiary opcjonalne, ale zalecane: Średnia (90 x 90), Wide (255 x 115), Hero (815 x 290). Musi być w formacie PNG. |
| Nazwa wideo + URL + miniatura  | Opcjonalne, zalecane, maksymalnie 4 wideo. Miniatura musi mieć 1280 x 720 w formacie PNG. Wideo musi być hostowane w serwisie YouTube lub Vimeo. |
| Kontakty (program CSP, inżynieria, pomoc techniczna)  | Wymagana osoba kontaktu inżynieryjnego i pomocy technicznej (nazwisko, adres e-mail i numer telefonu); Program CSP kontaktuje się z opcjonalnym, ale zalecanym. |
| Adres URL pomocy technicznej  | Wymagane. |

## <a name="preview-page"></a>Strona podglądu

Na stronie wersji zapoznawczej można określić odbiorców w celu uzyskania dostępu do wersji zapoznawczej oferty, aby upewnić się, że oferta spełnia wszystkie wymagania, zanim będzie ona aktywna. 

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------| 
| Wiadomości e-mail w usłudze AAD/MSA i opis | Wymagane, minimum 1 i maks. 10, jeśli zostało wprowadzone ręcznie, lub do 20 w przypadku przekazywania pliku CSV. |

## <a name="technical-configuration-page"></a>Strona konfiguracji technicznej 

Na stronie Konfiguracja techniczna można określić szczegóły techniczne używane przez firmę Microsoft do nawiązania połączenia z ofertą. Ta strona nie jest widoczna dla Ciebie, jeśli zdecydujesz się nie sprzedawać w firmie Microsoft.

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Adres URL strony docelowej | Wymagane, jeśli sprzedajesz przez firmę Microsoft. |
| Element webhook połączenia | Wymagane, jeśli sprzedajesz przez firmę Microsoft. |
| Identyfikator dzierżawy usługi Azure AD | Wymagane, jeśli sprzedajesz przez firmę Microsoft. |
| Identyfikator aplikacji usługi Azure AD | Wymagane, jeśli sprzedajesz przez firmę Microsoft. |

## <a name="plan-identity-modal"></a>Zaplanuj modalną tożsamość

Pierwsze informacje, które należy podać, to nazwa i identyfikator planu. Ta strona nie jest widoczna dla Ciebie, jeśli użytkownik zdecydował się nie sprzedawać w firmie Microsoft.

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Identyfikator planu  | Wymagane, jeśli sprzedajesz przez firmę Microsoft. Nie można go zmienić po utworzeniu. Maks. 50 znaków i musi zawierać tylko małe litery, znaki alfanumeryczne, łączniki i podkreślenia. |
| Nazwa planu  | Wymagane, jeśli sprzedajesz przez firmę Microsoft. Musi być unikatowa we wszystkich planach oferty. Maks. 50 znaków. |

## <a name="plan-listing-page"></a>Strona aukcji planu

Na stronie z listą planu można podać tekst, który mają być widoczne dla klientów podczas wyświetlania planu w portalu Marketplace. Ta strona nie jest widoczna dla Ciebie, jeśli zdecydujesz się nie sprzedawać w firmie Microsoft.

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Opis planu   | Wymagane, jeśli sprzedajesz przez firmę Microsoft. Maks. 500 znaków. | |

## <a name="plan-pricing--availability-page"></a>Zaplanuj stronę dostępności & cennika

Na stronie planowanie cen i dostępności można zdefiniować charakterystykę biznesową, odbiorców i dostępność rynkową dla każdego planu (wersji) oferty. Ta strona nie jest widoczna dla Ciebie, jeśli zdecydujesz się nie sprzedawać w firmie Microsoft.

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------| 
| Dostępność na rynku  | Wymagane, minimum 1 i maks. 141. |
| Model cen  | Wymagane. Domyślne: Stała stawka. Opcje: Stała stawka na użytkownika. |
| Minimalna i Maksymalna liczba stanowisk  | Opcjonalne, dostępne tylko wtedy, gdy wybrano model cenowy oparty na miejscu. |
| Okres rozliczeniowy  | Wymagany. Domyślne: Miesięczne. Opcje: Miesięczny, roczny. |
| Cena  | Wymagany USD miesięcznie, w przypadku wybrania miesięcznego okresu rozliczeniowego; lub USD na rok, jeśli wybrano roczny okres rozliczeniowy. |
| Planowanie odbiorców  | Opcjonalna. Domyślne: Plan publiczny. Opcje: Publiczne, prywatne według identyfikatora dzierżawy |
| Odbiorcy planu z ograniczeniami (identyfikator dzierżawy + opis)  | Wymagane, jeśli wybrano plan prywatny. Minimalna 1 i maksymalna 10 identyfikatorów dzierżawy, jeśli wprowadzono ją ręcznie. Max 20000, jeśli Importuj plik CSV. |

## <a name="test-drive-listing-page"></a>Strona listy dysków testowych

Dostępne tylko wtedy, gdy wybrano opcję zaoferowania dysku testowego dla oferty. Zdefiniuj szczegóły używane do wyświetlania na liście dysku testowego w portalu Marketplace.

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------| 
| Opis  | Wymagane. |
| Ręczna nazwa użytkownika + plik  | Wymagany, maks. 1 doc. Musi być w formacie PDF. |
| Nazwa wideo, adres URL i miniatura  | Opcjonalne, zalecane. Miniatura musi być 533 x 324 w formacie JPGP lub PNG. Wideo musi być hostowane w serwisie YouTube lub Vimeo. |

## <a name="review-and-publish-page"></a>Przeglądanie i publikowanie strony

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------| 
| Uwagi dotyczące certyfikacji  | Opcjonalny. |

## <a name="next-steps"></a>Następne kroki

- [Utwórz nową ofertę SaaS](./create-new-saas-offer.md)
