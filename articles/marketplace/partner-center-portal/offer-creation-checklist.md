---
title: Lista kontrolna tworzenia oferty SaaS — rynek komercyjny dla platformy Azure
description: Szczegóły, które można podać w procesie tworzenia oferty SaaS. - Komercyjny rynek dla platformy Azure
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 60111b6a23c76314383c5f95be3eb4b38f90d3e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281344"
---
# <a name="saas-offer-creation-checklist"></a>Lista kontrolna tworzenia oferty SaaS

Proces tworzenia oferty SaaS poprowadzi Cię przez wiele stron. Oto szczegóły, które możesz podać na każdej stronie, z linkami, aby dowiedzieć się więcej o każdym elemencie.

Elementy, które musisz podać lub określić, są wymienione poniżej. Niektóre obszary są opcjonalne lub mają podane wartości domyślne, które można zmienić zgodnie z potrzebami. Nie musisz pracować nad tymi sekcjami w kolejności wymienionej tutaj.

| **Element**    | **Przeznaczenie**  |
| :---------- | :-------------------|
| [**Nowa oferta Modal**](#new-offer-modal) | Zbiera informacje o tożsamości.  |
| [Strona ustawienia oferty](#offer-setup-page) | Umożliwia wybranie funkcji korzystania z kluczowych funkcji i wybranie sposobu sprzedaży oferty za pośrednictwem firmy Microsoft.  |
| [Strona Właściwości](#properties-page) | Zdefiniuj kategorie i branże używane do grupowania oferty na rynkach, umowy prawne obsługujące twoją ofertę i wersję aplikacji. |
| [Strona aukcji oferty](#offer-listing-page) | Zdefiniuj szczegóły oferty, które mają być wyświetlane na rynku, w tym opisy oferty i zasobów marketingowych. |
| [Podgląd strony](#preview-page) | Zdefiniuj ograniczoną grupę odbiorców w wersji zapoznawczej, aby zwolnić ofertę przed opublikowaniem oferty na żywo szerszym odbiorcom marketplace. |
| [Strona konfiguracji technicznej oferty](#technical-configuration-page)  | Opcja dostępna tylko w przypadku, gdy zdecydujesz się sprzedać ofertę za pośrednictwem firmy Microsoft. Zdefiniuj szczegóły techniczne (ścieżka adresu URL, element webhook, identyfikator dzierżawy i identyfikator aplikacji) używane do łączenia się z ofertą. |
| [**Nowy modal planu**](#plan-identity-modal) | Zbiera informacje o tożsamości planu.  |
| [Strona aukcji planu](#plan-listing-page)  | Opcja dostępna tylko w przypadku, gdy zdecydujesz się sprzedać ofertę za pośrednictwem firmy Microsoft. Zdefiniuj szczegóły używane do listy planu w portalu marketplace.  |
| [Strona dostępności cen & planu](#plan-pricing--availability-page)  | Opcja dostępna tylko w przypadku, gdy zdecydujesz się sprzedać ofertę za pośrednictwem firmy Microsoft.  Zbiera cechy biznesowe (model cenowy), odbiorców i dostępność rynkową dla każdego planu (wersji) oferty.  |
| [Strona aukcji dysku testowego](#test-drive-listing-page)  | Dostępne tylko wtedy, gdy zdecydujesz się zaoferować jazdę próbną dla swojej oferty. Zdefiniuj szczegóły używane do listy jazdy testowej w portalu marketplace.  |
| Strona konfiguracji technicznej dysku testowego  | Dostępne tylko wtedy, gdy zdecydujesz się zaoferować jazdę próbną dla swojej oferty. Zdefiniuj szczegóły techniczne demonstracji (lub "jazdy próbne"), która umożliwi klientom wypróbowanie oferty przed jej zakupem.  |
| [Przeglądanie i publikowanie strony](#review-and-publish-page)  | Wybierz zmiany, które chcesz opublikować, zobacz stan każdej strony i przekaż notatki zespołowi certyfikacji.  |


## <a name="new-offer-modal"></a>Nowa oferta modalna 

Pierwsze informacje, które zostaną poproszone o podanie, to identyfikator i alias oferty. 

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Identyfikator oferty  | Wymagane, nie można zmienić po utworzeniu. Maksymalnie 50 znaków i musi składać się tylko z małych liter, znaków alfanumerycznych, kresek lub podkreśleń. |
| Zaoferuj alias  | Wymagany. |

## <a name="offer-setup-page"></a>Strona konfiguracji oferty

Strona konfiguracji oferty to miejsce, w którym można zdecydować się na różne kanały i ruchy sprzedaży, a także zadeklarować korzystanie z kluczowych funkcji, takich jak jazda próbna i potencjalnych klientów. 

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------|  
| Czy chcesz sprzedawać za pośrednictwem firmy Microsoft?  | Wymagany. Domyślnie: Tak |
| Jak chcesz, aby potencjalni klienci wchodzili w interakcje z ofertą? (Wezwanie do działania)  | Wymagane, jeśli nie sprzedaje za pośrednictwem firmy Microsoft. Domyślnie: Bezpłatna wersja próbna, opcje: "Pobierz teraz", "Bezpłatna wersja próbna", "Skontaktuj się ze mną". |
| Adres URL wersji próbnej  | Wymagane, jeśli wybrano opcję "Bezpłatna wersja próbna", ponieważ klienci powinni wchodzić w interakcje z ofertą. |
| Adres URL oferty  | Wymagane, jeśli zostanie wybrana opcja "Pobierz teraz", ponieważ sposób, w jaki klienci powinni wchodzić w interakcje z ofertą |
| Kanały  | Element opcjonalny. Domyślnie: nie włączona do kanału CSP (sprzedawcy).  |
| Wersja testowa | Element opcjonalny. Domyślnie: brak włączonej jazdy testowej.  |
| Typ dysku testowego | Wymagane, jeśli włączono jazdę próbną. Domyślnie: Nie wybrano opcji. Opcje: Usługa Azure Resource Manager, Dynamics 365 for Business Central, Dynamics 365 for Customer Engagement, Dynamics 365 for Operations, Logic app, Power BI.  |
| Zarządzanie potencjalnymi klientami - połączenie z systemem CRM | Wymagane, jeśli sprzedaż za pośrednictwem firmy Microsoft lub jeśli oferty aukcji jako "Skontaktuj się ze mną". Domyślnie: nie ma podłączonego systemu CRM. Opcje CRM: tabela platformy Azure, obiekt blob platformy Azure, program Dynamics CRM online, punkt końcowy HTTP, Marketo, Salesforce  |

## <a name="properties-page"></a>Strona właściwości

Strona właściwości to miejsce, na którym definiujesz kategorie i branże używane do grupowania oferty na rynkach, umowy prawne obsługujące twoją ofertę i wersję aplikacji. Pamiętaj, aby podać pełne i dokładne informacje o swojej ofercie na tej stronie, aby była odpowiednio wyświetlana i oferowana właściwemu zestawowi klientów. 

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------|  
| Kategoria i podkategoria | Wymagane 1 i max 3. Domyślnie: Nie wybrano opcji. |
| Branże i podprzemystwa | Element opcjonalny. max 2 L1 Industries i maksymalnie 2 podprzemysłów w każdej branży L1, Domyślnie: Brak wybranych |
| Wersja aplikacji  | Element opcjonalny. Domyślnie: Brak. |
| Użyj kontraktu standardowego  | Element opcjonalny. Domyślnie: nie zaznaczono.  | |
| Warunki użytkowania  | Wymagane, jeśli umowa standardowa nie jest zaznaczona.  |

## <a name="offer-listing-page"></a>Strona aukcji oferty

Strona aukcji to miejsce, na którym podajesz tekst i obrazy, które klienci widzą podczas wyświetlania oferty w portalu Marketplace. 

| **Nazwa pola**    | **Uwagi**   |
| :---------------- | :-----------| 
| Nazwa  | Wymagane, max 50 znaków. |
| Podsumowanie  | Wymagane, maksymalnie 100 znaków. | 
| Opis  | Wymagane, max 3000 znaków. |
| Instrukcje dotyczące rozpoczynania pracy  | Wymagane, max 3000 znaków. |
| Instrukcje dotyczące rozpoczynania pracy  | Wymagane, max 3000 znaków. |
| Szukaj słów kluczowych  | Opcjonalnie, zalecane, maksymalnie 3 słowa kluczowe. |
| Adres URL polityki prywatności  | Wymagany. |
| Adres URL materiałów marketingowych programu CSP  | Element opcjonalny. |
| Przydatne linki Tytuł + ADRES URL  | Element opcjonalny. |
| Tytuł + plik dokumentów uzupełniających  | Wymagane, min 1 i max 3. Musi być w formacie PDF. |
| Zrzuty ekranu  | Wymagane, min 1 zrzut ekranu i max 5 ; zalecane cztery lub więcej. Musi mieć 1280 X 720 w formacie PNG. |
| Logo sklepu (małe, średnie, duże, szerokie, bohater)  | Małe (48 X 48) i duże (216 X 216) wymagane; inne rozmiary opcjonalne, ale zalecane: Średni (90 x 90), Szeroki (255 x 115), Bohater (815 x 290). Musi być w formacie PNG. |
| Nazwa filmu + adres URL + miniatura  | Opcjonalnie, zalecane, maksymalnie 4 filmy. Miniatura musi mieć rozmiar 1280 x 720 w formacie PNG. Film musi być hostowany w YouTube lub Vimeo. |
| Kontakty (program CSP, inżynieria, wsparcie)  | Wymagany kontakt inżynierski i pomocy technicznej (imię i nazwisko, adres e-mail i numer telefonu); Kontakt programu CSP jest opcjonalny, ale zalecany. |
| Adres URL pomocy technicznej  | Wymagany. |

## <a name="preview-page"></a>Strona podglądu

Na stronie podglądu można określić grupę odbiorców, aby mieć dostęp do podglądu oferty, aby sprawdzić, czy oferta spełnia wszystkie Twoje wymagania, zanim zostanie wyeksponowanych. 

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------| 
| E-mail AAD/MSA + opis | Wymagane, min 1 i max 10, jeśli zostały wprowadzone ręcznie, lub do 20 w przypadku przesyłania pliku CSV. |

## <a name="technical-configuration-page"></a>Strona konfiguracji technicznej 

Na stronie konfiguracji technicznej określono szczegóły techniczne używane przez firmę Microsoft do łączenia się z ofertą. Ta strona nie jest widoczna, jeśli zdecydujesz się nie sprzedawać za pośrednictwem firmy Microsoft.

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Adres URL strony docelowej | Wymagane w przypadku sprzedaży za pośrednictwem firmy Microsoft. |
| Elementów webhook połączenia | Wymagane w przypadku sprzedaży za pośrednictwem firmy Microsoft. |
| Identyfikator dzierżawy usługi Azure AD | Wymagane w przypadku sprzedaży za pośrednictwem firmy Microsoft. |
| Identyfikator aplikacji usługi Azure AD | Wymagane w przypadku sprzedaży za pośrednictwem firmy Microsoft. |

## <a name="plan-identity-modal"></a>Planowanie modalności tożsamości

Pierwsze informacje, które użytkownik jest proszony o podanie, to nazwa i identyfikator planu. Ta strona nie jest widoczna, jeśli zdecydujesz się nie sprzedawać za pośrednictwem firmy Microsoft.

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Identyfikator planu  | Wymagane w przypadku sprzedaży za pośrednictwem firmy Microsoft. Nie można go zmienić po utworzeniu. Maksymalnie 50 znaków i musi składać się tylko z małych liter, znaków alfanumerycznych, kresek lub podkreśleń. |
| Plan Name  | Wymagane w przypadku sprzedaży za pośrednictwem firmy Microsoft. Musi być unikalny we wszystkich planach w ofercie. Maksymalnie 50 znaków. |

## <a name="plan-listing-page"></a>Strona aukcji planu

Strona z listą planu to miejsce, na którym udostępniasz klientom tekst, który można zobaczyć podczas wyświetlania planu w portalu marketplace. Ta strona nie jest widoczna, jeśli zdecydujesz się nie sprzedawać za pośrednictwem firmy Microsoft.

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Opis planu   | Wymagane w przypadku sprzedaży za pośrednictwem firmy Microsoft. Maksymalnie 500 znaków. | |

## <a name="plan-pricing--availability-page"></a>Strona dostępności cen & planu

Strona cen i dostępności planu umożliwia zdefiniowanie charakterystyki biznesowej, odbiorców i dostępności rynkowej dla każdego planu (wersji) oferty. Ta strona nie jest widoczna, jeśli zdecydujesz się nie sprzedawać za pośrednictwem firmy Microsoft.

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------| 
| Dostępność na rynku  | Wymagane, min 1 i max 141. |
| Model cenowy  | Wymagany. Domyślnie: Stawka ryczałtowa. Opcje: Stawka ryczałtowa, na użytkownika. |
| Minimalna i maksymalna liczba miejsc  | Opcjonalnie, dostępne tylko wtedy, gdy wybrany jest model cenowy oparty na fotelu. |
| Termin rozliczeniowy  | Wymagany. Domyślnie: co miesiąc. Opcje: Miesięczny, Roczny. |
| Price  | Wymagany USD miesięcznie, jeśli wybrany zostanie wybrany miesięczny termin rozliczeniowy; lub USD rocznie, jeśli wybrany zostanie roczny termin rozliczeniowy. |
| Zaplanuj grupę odbiorców  | Element opcjonalny. Domyślnie: plan publiczny. Opcje: Publiczny, Prywatny według identyfikatora dzierżawy |
| Grupa odbiorców planu ograniczonego (identyfikator dzierżawy + opis)  | Wymagane, jeśli wybrany plan prywatny. Min 1 i max 10 identyfikatorów dzierżawy, jeśli wprowadzone ręcznie. Max 20000 jeśli plik CSV import. |

## <a name="test-drive-listing-page"></a>Strona z listą z dyskami testowych

Dostępne tylko wtedy, gdy zdecydujesz się zaoferować jazdę próbną dla swojej oferty. Zdefiniuj szczegóły używane do listy jazdy testowej w portalu marketplace.

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------| 
| Opis  | Wymagany. |
| Nazwa podręcznika użytkownika + plik  | Wymagane, maksymalnie 1 doc. Musi być w formacie PDF. |
| Nazwa filmu, adres URL + miniatura  | Opcjonalnie, zalecane. Miniatura musi mieć rozmiar 533 x 324 w formacie JPGP lub PNG. Film musi być hostowany w YouTube lub Vimeo. |

## <a name="review-and-publish-page"></a>Przeglądanie i publikowanie strony

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------| 
| Uwagi do certyfikacji  | Element opcjonalny. |

## <a name="next-steps"></a>Następne kroki

- [Tworzenie nowej oferty SaaS](./create-new-saas-offer.md)
