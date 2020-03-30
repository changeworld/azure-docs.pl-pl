---
title: Rozliczanie taryfowe za pomocą usługi pomiaru rynku | Azure Marketplace
description: Niniejsza dokumentacja jest przewodnikiem dla dostawców oprogramowania publikujących oferty SaaS z elastycznymi modelami rozliczeniowymi.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e5a4813301cbab16d1cffabaaa60688f6e826ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281327"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Rozliczanie taryfowe za pomocą usługi pomiaru rynku

Za pomocą usługi pomiarowej Marketplace można tworzyć oferty oprogramowania jako usługi (SaaS) w programie na rynku komercyjnym, które są naliczane zgodnie z niestandardowymi jednostkami.  Przed opublikowaniem tej oferty należy zdefiniować wymiary rozliczeń, takie jak przepustowość, bilety lub przetwarzane wiadomości e-mail.  Następnie klienci płacą zgodnie z zużyciem tych wymiarów, a system informuje firmę Microsoft za pośrednictwem interfejsu API usługi pomiarowej marketplace o zdarzeniach podlegających rozliczaniu w miarę ich występowania.  

## <a name="prerequisites-for-metered-billing"></a>Wymagania wstępne dotyczące rozliczeń taryfowych

Aby oferta SaaS korzystała z rozliczeń taryfowych, musi ona:

* Spełnij wszystkie wymagania dotyczące [oferty sprzedaży za pośrednictwem oferty firmy Microsoft,](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) zgodnie z [opisem w polu Utwórz ofertę SaaS.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer)
* Integracja z [interfejsami API realizacji SaaS dla](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) klientów w celu udostępnienia i połączenia z ofertą.  
* Być skonfigurowany dla modelu **zryczałtowanej** stawki cenowej do pobierania opłat od klientów za usługę.  Wymiary są opcjonalnym rozszerzeniem modelu zryczałtowanego. 
* Integracja z [interfejsami API usługi pomiaru marketplace,](./marketplace-metering-service-apis.md) aby informować firmę Microsoft o zdarzeniach podlegających rozliczaniu.

>[!Note]
>Usługa pomiaru w portalu Marketplace jest dostępna tylko dla modelu rozliczeń ryczałtowych i nie ma zastosowania do modelu rozliczeń na użytkownika.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Jak rozliczanie taryfowe wpisuje się w ceny

Jeśli chodzi o definiowanie oferty wraz z jej modeli cenowych, ważne jest, aby zrozumieć hierarchię ofert.

* Każda oferta SaaS jest skonfigurowana do sprzedaży za pośrednictwem firmy Microsoft lub nie.  Tego ustawienia nie można zmienić po opublikowaniu oferty.
* Każda oferta SaaS, skonfigurowana do sprzedaży za pośrednictwem firmy Microsoft, może mieć jeden lub więcej planów. Użytkownik subskrybuje ofertę SaaS, ale jest on amordyzuje za pośrednictwem firmy Microsoft w kontekście planu.
* Każdy plan ma model cenowy skojarzony z nim: **ryczałt** lub **na użytkownika**. Wszystkie plany w ofercie muszą być skojarzone z tym samym modelem cenowym. Na przykład nie może być oferta, w której jeden z jego planów jest modelem cen ryczałtowych, a inny jest modelem cenowym użytkownika.
* W ramach każdego planu skonfigurowanego dla modelu rozliczeń ryczałtowych uwzględniono co najmniej jedną opłatę cykliczną (która może wynosić 0 USD):
    * Cykliczna opłata **miesięczna:** stała opłata miesięczna, która jest wypłacana z góry w miesięcznym cyklu, gdy użytkownik kupuje plan.
    * Powtarzalna opłata **roczna:** stała opłata roczna, która jest uiszczana z góry w przypadku zakupu planu.
* Oprócz opłat cyklicznych plan może również zawierać opcjonalne wymiary używane do pobierania opłat od klientów za użycie, które nie są uwzględnione w stawce ryczałtowej.   Każdy wymiar reprezentuje rozliczaną jednostkę, którą usługa będzie komunikować z firmą Microsoft za pomocą [interfejsu API usługi pomiarowej marketplace](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Przykładowa oferta

Na przykład Contoso jest wydawcą z usługą SaaS o nazwie Contoso Notification Services (CNS). CNS umożliwia klientom wysyłanie powiadomień za pośrednictwem poczty elektronicznej lub wiadomości tekstowych. Contoso jest zarejestrowany jako wydawca w Centrum partnerów dla komercyjnego programu marketplace do publikowania ofert dla klientów platformy Azure.  Istnieją dwa plany związane z OUN, opisane poniżej:

* Plan bazowy
    * Wyślij 10000 e-maili i 1000 smsów za 0 USD miesięcznie
    * Poza 10000 e-maili, zapłacić $1 za każde 100 e-maili
    * Poza 1000 tekstów, zapłacić 0,02 dolarów za każdy tekst
* Plan Premium
    * Wyślij 50000 e-maili i 10000 smsów za $ 350 / miesiąc
    * Poza 50000 e-maili, zapłacić 0,5 dolarów za każde 100 e-maili
    * Poza 10000 tekstów, zapłacić 0,01 dolarów za każdy tekst

Klient platformy Azure subskrybujący usługę CNS będzie mógł wysyłać do wiadomości tekstowych i e-mail miesięcznie na podstawie wybranego planu.  Contoso mierzy użycie do uwzględnionej ilości bez wysyłania żadnych zdarzeń użycia do firmy Microsoft.  Gdy klienci zużywają więcej niż uwzględniona ilość, nie muszą zmieniać planów ani robić czegoś innego.  Contoso zmierzy nadmierną ilość poza uwzględniona ilość i rozpocznie emitowanie zdarzeń użycia do firmy Microsoft w celu dodatkowego użycia przy użyciu [interfejsu API usługi pomiaru marketplace](./marketplace-metering-service-apis.md).  Firma Microsoft z kolei obciąży klienta dodatkowym użyciem określonym przez wydawcę.

## <a name="billing-dimensions"></a>Wymiary rozliczeniowe

Wymiary rozliczeń służą do komunikowania się z klientem o sposobie naliczania na nie podatku za korzystanie z oprogramowania, a także do przekazywania zdarzeń użycia firmie Microsoft. Są one zdefiniowane w następujący sposób:

* **Identyfikator wymiaru:** niezmienny identyfikator, do którego odwołuje się podczas emitowania zdarzeń użycia.
* **Nazwa wymiaru**: nazwa wyświetlana skojarzona z wymiarem, np.
* **Jednostka miary:** opis jednostki rozliczeniowej, np.
* **Cena za jednostkę:** cena za jedną jednostkę wymiaru.  
* **Uwzględniona ilość na okres miesięczny:** ilość wymiaru uwzględniona miesięcznie dla odbiorców płacących cykliczną opłatę miesięczną, musi być liczeniem.
* **Uwzględniona ilość na okres roczny:** ilość wymiaru uwzględniona miesięcznie dla odbiorców płacących cykliczną opłatę roczną, musi być liczeniem całkowitym.

Wymiary rozliczeń są współużytkowane we wszystkich planach oferty.  Niektóre atrybuty mają zastosowanie do wymiaru we wszystkich planach, a inne atrybuty są specyficzne dla planu.

Atrybuty definiujące sam wymiar są współużytkowane we wszystkich planach oferty.  Przed opublikowaniem oferty zmiana tych atrybutów z kontekstu dowolnego planu wpłynie na definicję wymiaru we wszystkich planach.  Po opublikowaniu oferty te atrybuty nie będą już edytowalne.  Te atrybuty są następujące:

* Identyfikator
* Nazwa
* Jednostka miary

Inne atrybuty wymiaru są specyficzne dla każdego planu i mogą mieć różne wartości od planu do planowania.  Przed opublikowaniem planu można edytować te wartości i tylko ten plan będzie miał wpływ.  Po opublikowaniu planu te atrybuty nie będą już edytowalne.  Te atrybuty są następujące:

* Cena za jednostkę
* Uwzględniona ilość dla klientów miesięcznych 
* Uwzględniona ilość dla odbiorców rocznych 

Wymiary mają również dwie specjalne koncepcje: "włączone" i "nieskończone":

* **Włączone** wskazuje, że ten plan uczestniczy w tym wymiarze.  Można pozostawić to niezaewidencjonowane, jeśli tworzysz nowy plan, który nie wysyła zdarzeń użycia na podstawie tego wymiaru.  Ponadto wszystkie nowe wymiary dodane po pierwszym opublikowaniu planu będą wyświetlane jako "nie włączone" w już opublikowanym planie.  Wyłączony wymiar nie będzie widoczny na żadnych listach wymiarów planu widocznego przez klientów.
* **Nieskończony**, reprezentowany przez symbol nieskończoności "∞", wskazuje, że ten plan uczestniczy w tym wymiarze, ale nie mierzy użycia w stosunku do tego wymiaru.  Jeśli chcesz wskazać klientom, że funkcje reprezentowane przez ten wymiar jest uwzględniony w planie, ale bez limitu użycia.  Wymiar z nieskończonym użyciem pojawi się na listach wymiarów dla planu widocznego przez klientów, ze wskazaniem, że nigdy nie poniesie opłaty za ten plan.

>[!Note] 
>Następujące scenariusze są jawnie obsługiwane: <br> - Można dodać nowy wymiar do nowego planu.  Nowy wymiar nie będzie włączony dla żadnych już opublikowanych planów. <br> - Można opublikować plan **ryczałtowy** bez żadnych wymiarów, a następnie dodać nowy plan i skonfigurować nowy wymiar dla tego planu. Nowy wymiar nie będzie włączony dla już opublikowanych planów.

## <a name="constraints"></a>Ograniczenia

### <a name="trial-behavior"></a>Zachowanie próbne

Rozliczanie taryfowe za pomocą usługi pomiaru marketplace nie jest zgodne z oferowaniem bezpłatnej wersji próbnej.  Nie można skonfigurować planu do korzystania zarówno z rozliczeń taryfowych, jak i bezpłatnego okresu próbnego.

### <a name="locking-behavior"></a>Zachowanie blokowania

Ponieważ wymiar używany z usługą pomiaru marketplace reprezentuje zrozumienie, jak klient będzie płacić za usługę, wszystkie szczegóły dla wymiaru nie są już edytowalne po opublikowaniu.  Ważne jest, aby wymiary zostały w pełni zdefiniowane dla planu przed opublikowaniem.
  
Po opublikowaniu oferty z wymiarem nie można już zmieniać szczegółów na poziomie oferty dla tego wymiaru:

* Identyfikator
* Nazwa
* Jednostka miary

Po opublikowaniu planu nie można już zmieniać szczegółów na poziomie planu:

* Cena za jednostkę
* Uwzględniona ilość na okres miesięczny
* Uwzględniona ilość na okres roczny
* Czy wymiar jest włączony dla planu

### <a name="upper-limits"></a>Górne granice

Maksymalna liczba wymiarów, które można skonfigurować dla pojedynczej oferty, wynosi 18 unikatowych wymiarów.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Jeśli masz jedną z następujących opcji, możesz otworzyć bilet pomocy technicznej.

* Problemy techniczne z interfejsem API usługi pomiaru marketplace.
* Problem, który musi zostać eskalowany z powodu błędu lub błędu po twojej stronie (np. niewłaściwego użycia).
* Wszelkie inne problemy związane z rozliczaniem taryfowym. 

Wykonaj poniższe czynności, aby przesłać bilet pomocy technicznej:

1. Przejdź do [strony pomocy technicznej](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). Pierwsze kilka menu rozwijanych jest automatycznie wypełnianych za Ciebie. W przypadku pomocy technicznej w portalu Marketplace zidentyfikuj rodzinę produktów jako **usługi w chmurze i usługi online**, produkt jako **wydawca marketplace**.  Nie należy zmieniać wstępnie wypełnionych opcji menu rozwijanych.
2. W obszarze "Wybierz wersję produktu" wybierz opcję **Zarządzanie ofertami na żywo**.
3. W obszarze "Wybierz kategorię, która najlepiej opisuje problem", wybierz **pozycję Aplikacje SaaS**.
4. W obszarze "Wybierz problem, który najlepiej opisuje problem", wybierz **opcję rozliczanie taryfowe**.
5. Wybierając przycisk **Dalej,** zostaniesz przekierowany na stronę **Szczegóły wydania,** gdzie możesz wprowadzić więcej szczegółów dotyczących problemu.

Więcej opcji pomocy technicznej dla wydawców można znaleźć [w obszarze Obsługa programu dla rynku komercyjnego w Centrum partnerów.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz [Interfejsy API usługi pomiaru w portalu Marketplace.](./marketplace-metering-service-apis.md)
